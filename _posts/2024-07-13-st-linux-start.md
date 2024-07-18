---
layout: post
title:  "ST-Linux Environment Docker Development - 1"
date:   2024-07-13
last_modified_at: 2024-07-13
categories: [ST-Linux Develop]
tags: [Getting Start]
---

# Concepts
- Supply docker image to setup environment easily.

# Need Note
## Environment
- Ubuntu 20.04
## Toolchain
- GCC 10 for ubuntu
- GCC ARM Embedded Toolchain for build ST-Linux
## Packages
- make
- gcc-10
- libncurses-dev
- gawk 
- flex 
- bison 
- openssl 
- libssl-dev 
- dkms 
- libelf-dev 
- libudev-dev 
- libpci-dev 
- libiberty-dev 
- autoconf 
- llvm

# Docker
## Add User
- In environmet docker, 'stlinux' user is default. So I put code on Dockerfile likes under codeblock.
```docker
USER stlinux
WORKDIR /home/stlinux
```
