---
layout: post
title:  "Linux Kernel에서 Rust 개발 - 1"
date:   2024-10-02
last_modified_at: 2024-10-02
categories: [Linux Kernel]
tags: [Develop]
---

# Linux Kernel과 Rust
- Linux Kernel은 6.1 부터 Rust에 대해 지원하기 시작
- Android를 시작으로, Linux에 널리 사용되기 시작
- C언어보다 엄격한 **제한과 규칙**으로 Android 기준으로 절반이 넘게 메모리 관련 취약점이 줄어듬.
- [Linux Kernel Rust 문서](https://docs.kernel.org/rust/quick-start.html) 참고

# Linux Kernel에 Rust 사용해보기
**본 글은 Mainline인 Linux 6.12-rc1를 기준으로 작성한 글입니다.**

1. 아래 명령어를 입력하여, Linux Kernel에서 Rust 사용 여부 확인

```
$ make LLVM=1 rustavailable
```

- 만약 다음과 같이 Error가 발생하면, 위의 Linux Kernel Rust 문서 참고하여, 개발환경 구축.

![Error Rust Available](/assets/images/Screenshot%20from%202024-10-01%2023-39-39.png)

- 성공적으로 적용되면, 아래와 같이 메시지 출력.

![Success Rust Availble](/assets/images/Screenshot%20from%202024-10-01%2023-41-40.png)

2. menuconfig를 통해, Rust 지원 활성 후 Save

- Rust 지원 설정은 [General Setup] - [Rust Support] 활성화
![Rust Support Enable](/assets/images/Screenshot%20from%202024-10-01%2023-48-02.png)

3. Recompile 진행

- 다음과 같이 Rust 관련된 컴파일도 동일하게 진행.
![Rust Compile Image](/assets/images/Screenshot%20from%202024-10-01%2023-52-09.png)

4. Kernel 부팅 여부 확인
![Linux Kernel Compile](/assets/images/Screenshot%20from%202024-10-02%2000-09-14.png)

# Linux Kernel Rust 작성해보기
- 드라이버 작성 스타일을 살펴보면, 기존 C언어로 작성한 스타일보다는 Embedded C++ 스타일로 작성됨.
- 마치 Apple의 IoKit 작성과 비슷

## 기존 C언어 스타일

```c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>

static int __init
c_module_init(void)
{
    printk(KERN_INFO "Hello Driver!");
    return 0;
}

static void __exit
c_module_exit(void)
{
    printk(KERN_INFO "Bye Driver!");
}

module_init(c_module_init);
module_exit(c_module_exit);
```

- Init과 Exit Function을 선언하여, 사용해야 한다.
- 그리고 이를 module_init과 module_exit 매크로에 선언한 Function을 등록한은 방식

## Rust 스타일

```Rust
//! You must write this Annotation
//! Becasue this annotation is for generate documentation
//
// Normal Annotation : //
// Document Annotation : // + !
use kernel::prelude::*;

module! {
    type: RustModule
    name: "rust_module"
    author: "Nakada Tokumei"
    description: "Just Testing"
    license: "MIT"
}

struct RustModule

impl kernel::Module for RustModule {
    fn init(module: &'static ThisModule) -> Result<Self> {
        pr_info!("Hello, Rust!");
        Ok(Self)
    }
}

impl Drop for RustModule {
    fn drop(&mut self) {
        pr_info!("Bye, Rust!");
    }
}
```

- Rust는 Init과 Exit function을 객체의 Method로 선언, Method가 선언된 객체는 module! 매크로를 통해 지정.
- Init Method는 kernel::Module객체를 통해, 일종의 상속(정확히 동일한 상속은 아니다.)하여 선언
- Exit Method는 Drop 객체를 통해, 일종의 상속하여 선언

## Module 동작 여부 확인
- 커널을 동작시켜, Module 동작 확인

![Rust Kernel Module Load](/assets/images/Screenshot%20from%202024-10-02%2000-55-27.png)