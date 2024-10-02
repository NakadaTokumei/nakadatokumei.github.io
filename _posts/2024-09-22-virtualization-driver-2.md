---
layout: post
title:  "Linux Kernel에서 가상화 드라이버 개발 - 2"
date:   2024-09-22
last_modified_at: 2024-09-22
categories: [Virtualization Driver Develop]
tags: [Develop]
---

# 이번 글에 해야하는 것
- VMX 지원 여부 확인.
- 기본적인 Character Driver 작성.

# VMX 지원 여부 확인하기.
## /proc/cpuinfo 응용
- /proc/cpuinfo를 이용하여, VMX 지원여부 확인 가능


```bash
cat /proc/cpuinfo | grep vmx
```

![cpuinfo](/assets/images/Screenshot%20from%202024-09-22%2015-03-10.png)

## CPUID Instruction 이용하기
- CPUID Instruction을 활용하여, VMX 지원 여부를 확인할 수 있다.
- 보통 드라이버에서 VMX 지원 여부를 확인하기 위해서 사용.
