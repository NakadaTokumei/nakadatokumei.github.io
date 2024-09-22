---
layout: post
title:  "Linux Kernel에서 가상화 드라이버 개발 - 1"
date:   2024-09-22
last_modified_at: 2024-09-22
categories: [Virtualization Driver Develop]
tags: [Getting Start]
---

# 본 글을 시작하기 앞서
- 이 글은 본인(Nakatoku)의 배운 것들 정리이므로, 일부 정보 누락 및 생략이 발생할 수 있으므로, 양해 바랍니다.

# 가상화(Virtualzation)란?
- **가상화**란 말 그대로, 하나의 하드웨어에서 가상머신(Virtual Machines, VM)을 통해 분할하여, 추상화하는 것을 의미.
    - 간단하게 요약해보면, 하나의 하드웨어에서 여러 대의 다양한 운영체제로 필요에 따른 역할을 분별할 수 있음. (아래 이미지 참조)

![Virtualzation](/assets/images/Virtualization.png)

# Hypervisor?
- Hypervisor라는 것은 가상 머신을 관리하고 사용할 수 있는 소프트웨어. 어느 부분에서 실행하느냐에 따라, Type 1 & 2로 나누어짐.
    - Type 1 : Host OS 보다 하위 계층에서 제어되는 하이퍼바이저를 의미.\
    간단하게 설명하자면, 본래 운영체제가 제어하는 부분을 Hypervisor가 담당.
    - Type 2 : Host OS 보다 상위 계층에서 제어되는 하이퍼바이저를 의미.\
    VM들을 제어할 때, 무조건 Host OS를 거쳐야하는 특징이 있다.
- KVM과 같이 Type 1.5로 별개로 구분되는 경우도 있음.
- Type 1 하이퍼바이저 같은 경우에는 사실상 운영체제에 가까운 형태이기에, Microkernel 형태로 작성되는 경우가 대부분.
    - 대표적인 Type 1 하이퍼바이저 : **Xen**, **Hyper-V**, **Proxmox** 등

![Xen Project](/assets/images/Da2BXtzpzPF59WMwPBsK24KUJ_E692fcRFl65iJpQbsC9fMYQYpHhASxy-byBUbaa6gyf6hg--Q8aqCQGJDyzLwV1tvK0K6NOuc-2QA_fJ0QlrhIY8kXTrhd-PXGLn9U1TMxRcBjdL3cqUTA6dTmVQ.svg)


# 가상화를 구현하기 위한 다양한 방법
## System Call
- 바로 어느정도 딥하게 개발해본 개발자들은 아는 **그 System Call**이다.

```assembly
    syscall ; <- 얘네들 맞다.
```

- 정확히는 PV(Paravirtualization, 반가상화)를 사용할 때 사용한다.
- System Call을 사용한 반가상화인만큼, 각 운영체제들이 하이퍼바이저에 맞는 System Call 호출에 대한 수정이 무조건 필요하다는 단점이 존재.
- 현재는 Xen에서 대표적으로 사용.

## VMX(Virtual Machine Extension)
- 직접 System Call을 이용한, Hypercall 구현을 직접해줄 필요 없음.
- CPU에서 지원되는지만 확인이 되면, 큰 어려움 없이 사용할 수 있음.
- 대신 일종의 CPU 기능을 사용하는 만큼, CPU Reference Manual을 통해 사용방법을 알아야함.

# 기본적인 Linux Kernel 드라이버 작성하기
- 기본적인 드라이버 작성.
## 코드

```c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>

static int __init
henry_virt_init(void)
{
    printk(KERN_INFO "Hello, Linux Kernel!\n");
    return 0;
}

static void __exit
henry_virt_exit(void)
{
    printk(KERN_INFO "Bye, Linux Kernel!\n");
}

module_init(henry_virt_init);
module_exit(henry_virt_exit);
```

## 결과 (예시)
![kernel_output](/assets/images/Screenshot%20from%202024-09-22%2002-10-54.png)

# 참조 문서 및 프로젝트
- [Nakada Tokumei's Simple Ubuntu Builder](https://github.com/NakadaTokumei/simple-ubuntu)