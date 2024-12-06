---
title: Checksum校验
mathjax: true
date: 2024-12-06 22:02:38
tags:
categories:
---

本文在RFC1071的基础上使用C语言实现了IPV4，ICMP,UDP,TCP协议的识别以及checksum字段的计算。

<!--more-->

### 代码

```c
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>
#include <string.h>
#include <dirent.h>

// 计算校验和
unsigned short checksum(void *addr, int count) {
    register long sum = 0;

    while (count > 1) {
        sum += *(unsigned short *)addr;
        addr += 2;
        count -= 2;
    }

    if (count > 0)
        sum += *(unsigned char *)addr;

    while (sum >> 16)
        sum = (sum & 0xFFFF) + (sum >> 16);

    return ~sum;
}

// 打印十六进制数据
void printHex(const unsigned char *packet, unsigned int offset, unsigned int size) {
    int i = 0;int count = 0;
    for (; i < size; i++) {
        printf("%02x ", packet[offset + i]);
        if (i >= 0x50) {
            printf("...\n");
            break;
        } 
        if ((i + 1) % 16 == 0) printf("\n");
    }
    if (i % 16 != 0) printf("\n");
}

// 构造伪首部
void construct_pseudo_header(unsigned char *pseudo_header, const unsigned char *packet, unsigned short protocol, unsigned short length) {
    // 源 IP 地址
    memcpy(pseudo_header, &packet[26], 4);
    // 目的 IP 地址
    memcpy(&pseudo_header[4], &packet[30], 4);
    // 保留字节
    pseudo_header[8] = 0;
    // 协议号
    pseudo_header[9] = protocol;
    // TCP/UDP 长度
    pseudo_header[10] = length >> 8;
    pseudo_header[11] = length & 0xFF;
}

// 处理 ICMP 报文
void process_icmp(const unsigned char *packet, unsigned int offset, unsigned int size, unsigned int IPlength) {
    printf("=========================================================================\n");
    printf("识别到使用的协议为 ICMP，开始解析......\n");

    unsigned short ICMPlength = size - offset;
    printf("ICMP 长度为 %d，其内容为:\n", ICMPlength);
    printHex(packet, offset, ICMPlength);

    unsigned short icmp_checksum = (packet[offset + 2] << 8) | packet[offset + 3];
    printf("报文中 checksum 字段为: 0x%02x 0x%02x\n", icmp_checksum >> 8, icmp_checksum & 0xFF);

    unsigned char *icmp_data = (unsigned char *)malloc(ICMPlength);
    memcpy(icmp_data, &packet[offset], ICMPlength);

    printf("清空 checksum 字段后，报文变为:\n");
    icmp_data[2] = 0x00;
    icmp_data[3] = 0x00;
    printHex(icmp_data, 0, ICMPlength);

    unsigned short calculated_checksum = checksum(icmp_data, ICMPlength);
    printf("本地计算 checksum 为: 0x%02x 0x%02x\n", calculated_checksum & 0xFF, calculated_checksum >> 8);
    // printf("=========================================================================\n");

    free(icmp_data);
}

// 解析 TCP 报文
void process_tcp(const unsigned char *packet, unsigned int offset, unsigned int size, unsigned int IPlength) {
    printf("=========================================================================\n");
    printf("识别到使用的协议为 TCP，开始解析......\n");

    unsigned short TCPlength = size - offset;
    printf("TCP 长度为 %d，其内容为:\n", TCPlength);
    printHex(packet, offset, TCPlength);

    unsigned short tcp_checksum = (packet[offset + 16] << 8) | packet[offset + 17];
    printf("报文中 checksum 字段为: 0x%02x 0x%02x\n", tcp_checksum >> 8, tcp_checksum & 0xFF);

    unsigned char pseudo_header[12];
    construct_pseudo_header(pseudo_header, packet, 0x06, TCPlength);

    unsigned char *checksum_data = (unsigned char *)malloc(12 + TCPlength);
    memcpy(checksum_data, pseudo_header, 12);
    memcpy(&checksum_data[12], &packet[offset], TCPlength);

    printf("清空 checksum 字段后，报文变为:\n");
    checksum_data[28] = 0x00;
    checksum_data[29] = 0x00;
    printHex(packet, offset, TCPlength);

    unsigned short calculated_checksum = checksum(checksum_data, 12 + TCPlength);
    printf("本地计算 checksum 为: 0x%02x 0x%02x\n", calculated_checksum & 0xFF, calculated_checksum >> 8);
    // printf("=========================================================================\n");

    free(checksum_data);
}

// 解析 UDP 报文
void process_udp(const unsigned char *packet, unsigned int offset, unsigned int size, unsigned int IPlength) {
    printf("=========================================================================\n");
    printf("识别到使用的协议为 UDP，开始解析......\n");

    unsigned short UDPlength = size - offset;
    printf("UDP 长度为 %d，其内容为:\n", UDPlength);
    printHex(packet, offset, UDPlength);

    unsigned short udp_checksum = (packet[offset + 6] << 8) | packet[offset + 7];
    printf("报文中 checksum 字段为: 0x%02x 0x%02x\n", udp_checksum >> 8, udp_checksum & 0xFF);

    unsigned char pseudo_header[12];
    construct_pseudo_header(pseudo_header, packet, 0x11, UDPlength);

    unsigned char *checksum_data = (unsigned char *)malloc(12 + UDPlength);
    memcpy(checksum_data, pseudo_header, 12);
    memcpy(&checksum_data[12], &packet[offset], UDPlength);

    printf("清空 checksum 字段后，报文变为:\n");
    checksum_data[18] = 0x00;
    checksum_data[19] = 0x00;
    printHex(packet, offset, UDPlength);

    unsigned short calculated_checksum = checksum(checksum_data, 12 + UDPlength);
    printf("本地计算 checksum 为: 0x%02x 0x%02x\n", calculated_checksum & 0xFF, calculated_checksum >> 8);
    // printf("=========================================================================\n");

    free(checksum_data);
}

// 解析报文
void process_packet(unsigned char *packet, int size) {
    if (size < 20) {
        printf("Invalid packet size: %d bytes\n", size);
        return;
    }

    unsigned short IPVersion = (packet[12] << 8) | packet[13];
    switch (IPVersion) {
        case 0x0800: {
            printf("=========================================================================\n");
            printf("识别到使用的协议为 IPV4，开始解析......\n");
            unsigned short IPlength = (packet[14] & 0x0F) << 2;
            printf("IP 头长度为 %d，其内容为:\n", IPlength);
            printHex(packet, 14, IPlength);

            unsigned short packet_checksum = (packet[24] << 8) | packet[25];
            // 从低字节向高字节打印
            printf("在该段报文中，checksum字段为:0x%02x 0x%02x\n",packet_checksum >> 8,packet_checksum & 0x00ff);
            printf("清空源报文中checksum字段，开始本地校验......\n");
            packet[24] = 0x00; packet[25] = 0x00;
            printf("清空checksum字段后报文变为:\n");
            printHex(packet,14,IPlength);
            unsigned short my_checksum = checksum((void *)packet+14, IPlength);
            // 小端存储，从低字节向高字节打印
            printf("本地checksum计算为:0x%02x 0x%02x\n",my_checksum & 0x00ff,my_checksum >> 8);
            // printf("=========================================================================\n");

            unsigned short protocol = packet[23];
            unsigned int offset = 14 + IPlength;

            if (protocol == 0x01) {
                process_icmp(packet, offset, size, IPlength);
            } else if (protocol == 0x06) {
                process_tcp(packet, offset, size, IPlength);
            } else if (protocol == 0x11) {
                process_udp(packet, offset, size, IPlength);
            }
            break;
        }
        default:
            printf("未知的协议类型: 0x%02x\n", IPVersion);
            break;
    }
}

// 从文件中读取数据并解析
void read_and_process_file(const char *filename) {
    FILE *file = fopen(filename, "rb");
    if (!file) {
        perror("Error opening file");
        return;
    }

    unsigned char buffer[2048];
    int read_size;

    while ((read_size = fread(buffer, 1, sizeof(buffer), file)) > 0) {
        printf("\n读取到大小为 %d 字节的报文文件: %s\n", read_size, filename);
        process_packet(buffer, read_size);
    }

    fclose(file);
}

// 自动读取当前目录下的 .bin 文件
void process_bin_files_in_directory(const char *directory) {
    struct dirent *entry;
    DIR *dir = opendir(directory);

    if (!dir) {
        perror("Error opening directory");
        return;
    }

    while ((entry = readdir(dir)) != NULL) {
        if (strstr(entry->d_name, ".bin")) {
            char filepath[1024];
            snprintf(filepath, sizeof(filepath), "%s/%s", directory, entry->d_name);

            printf("\n处理文件: %s", filepath);
            read_and_process_file(filepath);
        }
    }

    closedir(dir);
}

int main() {
    printf("从当前目录读取 .bin 文件...\n");
    process_bin_files_in_directory(".");

    return 0;
}
```