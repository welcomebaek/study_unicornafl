> **__NOTE__**: This document is translated with AI assistance

# Unicorn-Engine API Documentation

| Version | 2.0.0 |
| ------- | ----- |

**Official API document by [kabeor](https://github.com/kabeor)**

[PDF File](https://github.com/kabeor/Micro-Unicorn-Engine-API-Documentation) 

[Unicorn Engine](http://www.unicorn-engine.org/)은 경량, 다중 플랫폼, 다중 아키텍처의 CPU 에뮬레이터 프레임워크로, 현재 버전은 [Qemu](https://www.qemu.org/) 5.0.1을 기반으로 개발되었습니다. 이는 CPU 에뮬레이션 코드의 실행을 대체할 수 있으며, 주로 프로그램 가상화, 악성 코드 분석, Fuzzing 등에 사용됩니다. 이 프로젝트는 [Qiling 가상화 프레임워크](https://github.com/qilingframework/qiling), [Radare2 리버스 엔지니어링 프레임워크](https://github.com/qilingframework/qiling), [GEF(gdb의 pwn 분석 플러그인)](https://github.com/hugsy/gef), [Pwndbg](https://github.com/pwndbg/pwndbg), [Angr 심볼릭 실행 프레임워크](https://github.com/angr/angr) 등 여러 유명 프로젝트에서 사용되고 있습니다.

## 0x0 개발 준비

Unicorn웹사이트:     http://www.unicorn-engine.org

```bash
git clone https://github.com/unicorn-engine/unicorn.git
```

### 빌드

<details><summary>Linux & MacOS</summary>

Ubuntu
```bash
sudo apt install cmake pkg-config
```

MacOS
```bash
brew install cmake pkg-config
```

다음 명령을 사용하여 컴파일하십시오.
```bash
mkdir build; cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
make
```
</details>

<details><summary>Windows</summary>

Microsoft MSVC 컴파일러로 컴파일
```bash
// cmake 및 Microsoft Visual Studio (>=16.8) 설치
// Visual Studio Command Prompt에서 다음 명령을 사용하여 컴파일
mkdir build; cd build
cmake .. -G "NMake Makefiles" -DCMAKE_BUILD_TYPE=Release
nmake
```

<details><summary>VS GUI에서 lib와 dll 컴파일 (구 버전)</summary>

소스코드： https://github.com/unicorn-engine/unicorn/archive/master.zip

다운로드 후 압축 해제

파일 구조는 다음과 같습니다:

```
. <- 주요 엔진 core engine + README + 컴파일 문서 COMPILE.TXT 등
├── bindings <- 바인딩
│ ├── dotnet <- .Net 바인딩 + 테스트 코드
│ ├── go <- go 바인딩 + 테스트 코드
│ ├── haskell <- Haskell 바인딩 + 테스트 코드
│ ├── java <- Java 바인딩 + 테스트 코드
│ ├── pascal <- Pascal 바인딩 + 테스트 코드
│ ├── python <- Python 바인딩 + 테스트 코드
│ ├── ruby <- Ruby 바인딩 + 테스트 코드
│ ├── rust <- Rust 바인딩 + 테스트 코드
│ └── vb6 <- VB6 바인딩 + 테스트 코드
├── docs <- 문서
├── glib_compat <- glib 2.64.4 기반으로 수정된 호환 라이브러리
├── include <- C 헤더 파일
├── msvc <- Microsoft Visual Studio 지원 (Windows)
├── qemu <- 수정된 qemu 소스 코드
├── samples <- Unicorn 사용 예제
└── tests <- C 언어 테스트 케이스
```

아래는 Windows 10에서 Visual Studio 2019를 사용하여 컴파일하는 방법입니다.

msvc 폴더를 열면 내부 구조는 다음과 같습니다.

![image.png](API_Doc_Pic/1_1.png)

Visual Studio에서 unicorn.sln 프로젝트 파일을 열면, 솔루션이 자동으로 다음 항목들을 로드합니다.

![image.png](API_Doc_Pic/1_2.png)

모든 항목이 필요하다면, 바로 컴파일하면 됩니다. 일부 항목만 필요하다면, 솔루션을 마우스 오른쪽 버튼으로 클릭 -> 속성 -> 구성 속성 -> 빌드 옵션에서 필요한 지원 항목을 선택하면 됩니다.

또한, 시작 프로젝트에서 여러 프로젝트를 구성할 수 있습니다. 아래와 같이 설정하십시오.

![image.png](API_Doc_Pic/1_3.png)

컴파일 후 현재 폴더의 Debug 디렉토리에 unicorn.lib 정적 라이브러리와 unicorn.dll 동적 라이브러리가 생성됩니다. 이렇게 하면 Unicorn을 사용한 개발을 시작할 수 있습니다.

공식적으로 제공되는 최신 컴파일된 버전은 1.0.3 버전이며, 최신 버전의 소스를 직접 편집하여 더 많은 사용 가능한 API를 얻을 수 있습니다.

> Win32：https://github.com/unicorn-engine/unicorn/releases/download/2.0.0/unicorn-2.0.0-win32.zip

> Win64：https://github.com/unicorn-engine/unicorn/releases/download/2.0.0/unicorn-2.0.0-win64.zip

**주의: x32 또는 x64를 선택하면 이후 개발의 아키텍처에 영향을 미칩니다**

컴파일을 클릭한 후, unicorn\msvc\x32 또는 x64\Debug 또는 Release 디렉토리에서 unicorn.dll과 unicorn.lib를 찾으면 됩니다.

</details>
</details>

> 다른 컴파일 방법은 [여기](https://github.com/unicorn-engine/unicorn/blob/master/docs/COMPILE.md)를 클릭하세요.

### 설치
- Python모듈
``` bash
pip install unicorn

//1.x 버전을 설치한 경우, 다음 명령을 통해 직접 업그레이드할 수 있습니다.
pip install unicorn --upgrade
```

- MacOS HomeBrew패키지
```bash
brew install unicorn
```

### 엔진 호출 테스트
(Windows VS2019을 예로 들었습니다)

1. 새 VS 프로젝트를 생성합니다.
2. ..\unicorn-master\include\unicorn 폴더에 있는 헤더 파일과 컴파일된 lib 및 dll 파일을 새 프로젝트의 주 디렉토리로 복사합니다.

![image.png](API_Doc_Pic/1_5.png)

VS 솔루션에서 다음 단계를 수행합니다:

1. 헤더 파일에 기존 항목으로 `unicorn.h`를 추가합니다.
2. 리소스 파일에 `unicorn.lib`를 추가합니다.
3. 솔루션을 다시 빌드합니다.

![image.png](API_Doc_Pic/1_6.png)

이제 생성한 Unicorn 엔진을 테스트해봅시다.

메인 파일 코드는 다음과 같습니다:

<details><summary> Code </summary>

```cpp
#include <iostream>
#include "unicorn/unicorn.h"

// 에뮬레이션할 명령
#define X86_CODE32 "\x41\x4a" // INC ecx; DEC edx

// 시작 주소
#define ADDRESS 0x1000000

int main()
{
    uc_engine* uc;
    uc_err err;
    int r_ecx = 0x1234;     // ECX 레지스터
    int r_edx = 0x7890;     // EDX 레지스터

    printf("Emulate i386 code\n");

    // X86-32bit 모드 초기화 에뮬레이션
    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }

    // 에뮬레이터에 2MB 메모리 할당
    uc_mem_map(uc, ADDRESS, 2 * 1024 * 1024, UC_PROT_ALL);

    // 에뮬레이션할 명령을 메모리에 씀
    if (uc_mem_write(uc, ADDRESS, X86_CODE32, sizeof(X86_CODE32) - 1)) {
        printf("Failed to write emulation code to memory, quit!\n");
        return -1;
    }

    // 레지스터 초기화
    uc_reg_write(uc, UC_X86_REG_ECX, &r_ecx);
    uc_reg_write(uc, UC_X86_REG_EDX, &r_edx);

    printf(">>> ECX = 0x%x\n", r_ecx);
    printf(">>> EDX = 0x%x\n", r_edx);

     // 명령어 에뮬레이션
    err = uc_emu_start(uc, ADDRESS, ADDRESS + sizeof(X86_CODE32) - 1, 0, 0);
    if (err) {
        printf("Failed on uc_emu_start() with error returned %u: %s\n",
        err, uc_strerror(err));
    }

    // 레지스터 값 출력
    printf("Emulation done. Below is the CPU context\n");

    uc_reg_read(uc, UC_X86_REG_ECX, &r_ecx);
    uc_reg_read(uc, UC_X86_REG_EDX, &r_edx);
    printf(">>> ECX = 0x%x\n", r_ecx);
    printf(">>> EDX = 0x%x\n", r_edx);

    uc_close(uc);

    return 0;
}
```

</details>


실행 결과는 다음과 같습니다.

![image.png](API_Doc_Pic/1_7.png)

ECX +1과 EDX -1이 성공적으로 에뮬레이션되었습니다.

## 0x1 데이터 타입

### 차례

[uc_arch](#uc_arch)

[uc_mode](#uc_mode)

[uc_err](#uc_err)

[uc_mem_type](#uc_mem_type)

[uc_hook_type](#uc_hook_type)

[Hook Types](#hook_types)

[uc_mem_region](#uc_mem_region)

[uc_query_type](#uc_query_type)

[uc_control_type](#uc_control_type)

[uc_context](#uc_context)

[uc_prot](#uc_prot)

---

### uc_arch

아키텍처 선택

<details><summary> Code </summary>

```cpp
typedef enum uc_arch {
    UC_ARCH_ARM = 1,    // ARM 아키텍처 (Thumb, Thumb-2 포함)
    UC_ARCH_ARM64,      // ARM-64, AArch64라고도 함
    UC_ARCH_MIPS,       // MIPS 아키텍처
    UC_ARCH_X86,        // X86 아키텍처 (x86 및 x86-64 포함)
    UC_ARCH_PPC,        // PowerPC 아키텍처
    UC_ARCH_SPARC,      // SPARC 아키텍처
    UC_ARCH_M68K,       // M68K 아키텍처
    UC_ARCH_RISCV,      // RISCV 아키텍처
    UC_ARCH_S390X,      // S390X 아키텍처
    UC_ARCH_TRICORE,    // TriCore 아키텍처
    UC_ARCH_MAX,
} uc_arch;
```

</details>

### uc_mode

모드 선택

<details><summary> Code </summary>

```cpp
typedef enum uc_mode {
    UC_MODE_LITTLE_ENDIAN = 0,    // 리틀 엔디안 모드 (기본값)
    UC_MODE_BIG_ENDIAN = 1 << 30, // 빅 엔디안 모드

    // arm / arm64
    UC_MODE_ARM = 0,              // ARM 모드
    UC_MODE_THUMB = 1 << 4,       // THUMB 모드 (Thumb-2 포함)
    
    // 폐기됨, UC_ARM_CPU_* 및 uc_ctl로 전환
    UC_MODE_MCLASS = 1 << 5,      // ARM의 Cortex-M 시리즈
    UC_MODE_V8 = 1 << 6,          // ARMv8 A32 인코딩
    UC_MODE_ARMBE8 = 1 << 10, // 빅 엔디안 데이터와 리틀 엔디안 코드, UC1 버전과의 호환성을 위해

    // arm (32bit) CPU 타입
    // 폐기됨, UC_ARM_CPU_* 및 uc_ctl로 전환
    UC_MODE_ARM926 = 1 << 7,	  // ARM926 CPU 타입
    UC_MODE_ARM946 = 1 << 8,	  // ARM946 CPU 타입
    UC_MODE_ARM1176 = 1 << 9,	  // ARM1176 CPU 타입

    // mips
    UC_MODE_MICRO = 1 << 4,       // MicroMips 모드 (지원되지 않음)
    UC_MODE_MIPS3 = 1 << 5,       // Mips III ISA (지원되지 않음)
    UC_MODE_MIPS32R6 = 1 << 6,    // Mips32r6 ISA (지원되지 않음)
    UC_MODE_MIPS32 = 1 << 2,      // Mips32 ISA
    UC_MODE_MIPS64 = 1 << 3,      // Mips64 ISA

    // x86 / x64
    UC_MODE_16 = 1 << 1,          // 16비트 모드
    UC_MODE_32 = 1 << 2,          // 32비트 모드
    UC_MODE_64 = 1 << 3,          // 64비트 모드

    // ppc
    UC_MODE_PPC32 = 1 << 2,       // 32비트 모드
    UC_MODE_PPC64 = 1 << 3,       // 64비트 모드 (지원되지 않음)
    UC_MODE_QPX = 1 << 4,         // Quad Processing eXtensions 모드 (지원되지 않음)

    // sparc
    UC_MODE_SPARC32 = 1 << 2,     // 32비트 모드
    UC_MODE_SPARC64 = 1 << 3,     // 64비트 모드
    UC_MODE_V9 = 1 << 4,          // SparcV9 모드 (지원되지 않음)

    // riscv
    UC_MODE_RISCV32 = 1 << 2,     // 32비트 모드
    UC_MODE_RISCV64 = 1 << 3,     // 64비트 모드

    // m68k
} uc_mode;
```

</details>


### uc_err

오류 유형, 이는 [uc_errno()](#uc_errno)의 반환 값입니다.

<details><summary> Code </summary>

```cpp
typedef enum uc_err {
    UC_ERR_OK = 0,           // 오류 없음
    UC_ERR_NOMEM,            // 메모리 부족: uc_open(), uc_emulate()
    UC_ERR_ARCH,             // 지원되지 않는 아키텍처: uc_open()
    UC_ERR_HANDLE,           // 사용 불가능한 핸들
    UC_ERR_MODE,             // 사용 불가능/지원되지 않는 아키텍처: uc_open()
    UC_ERR_VERSION,          // 지원되지 않는 버전 (또는 언어 바인딩)
    UC_ERR_READ_UNMAPPED,    // 맵핑되지 않은 메모리에서 읽기 때문에 에뮬레이션 종료: uc_emu_start()
    UC_ERR_WRITE_UNMAPPED,   // 맵핑되지 않은 메모리에 쓰기 때문에 에뮬레이션 종료: uc_emu_start()
    UC_ERR_FETCH_UNMAPPED,   // 맵핑되지 않은 메모리에서 데이터 가져오기 때문에 에뮬레이션 종료: uc_emu_start()
    UC_ERR_HOOK,             // 잘못된 훅 타입: uc_hook_add()
    UC_ERR_INSN_INVALID,     // 잘못된 명령어 때문에 에뮬레이션 종료: uc_emu_start()
    UC_ERR_MAP,              // 잘못된 메모리 맵핑: uc_mem_map()
    UC_ERR_WRITE_PROT,       // UC_MEM_WRITE_PROT 충돌 때문에 에뮬레이션 중지: uc_emu_start()
    UC_ERR_READ_PROT,        // UC_MEM_READ_PROT 충돌 때문에 에뮬레이션 중지: uc_emu_start()
    UC_ERR_FETCH_PROT,       // UC_MEM_FETCH_PROT 충돌 때문에 에뮬레이션 중지: uc_emu_start()
    UC_ERR_ARG,              // uc_xxx 함수에 제공된 잘못된 인수
    UC_ERR_READ_UNALIGNED,   // 정렬되지 않은 읽기
    UC_ERR_WRITE_UNALIGNED,  // 정렬되지 않은 쓰기
    UC_ERR_FETCH_UNALIGNED,  // 정렬되지 않은 가져오기
    UC_ERR_HOOK_EXIST,       // 이 이벤트에 대한 훅이 이미 존재함
    UC_ERR_RESOURCE,         // 자원 부족: uc_emu_start()
    UC_ERR_EXCEPTION,        // 처리되지 않은 CPU 예외
} uc_err;
```

</details>


### uc_mem_type

UC_HOOK_MEM_*의 모든 메모리 접근 유형

<details><summary> Code </summary>

```cpp
typedef enum uc_mem_type {
    UC_MEM_READ = 16,        // 메모리에서..읽기
    UC_MEM_WRITE,            // 메모리에 쓰기..
    UC_MEM_FETCH,            // 메모리에서 가져오기
    UC_MEM_READ_UNMAPPED,    // 맵핑되지 않은 메모리에서..읽기
    UC_MEM_WRITE_UNMAPPED,   // 맵핑되지 않은 메모리에 쓰기..
    UC_MEM_FETCH_UNMAPPED,   // 맵핑되지 않은 메모리에서 가져오기
    UC_MEM_WRITE_PROT,       // 메모리 쓰기 보호, 하지만 맵핑됨
    UC_MEM_READ_PROT,        // 메모리 읽기 보호, 하지만 맵핑됨
    UC_MEM_FETCH_PROT,       // 메모리 실행 불가, 하지만 맵핑됨
    UC_MEM_READ_AFTER,       // 메모리에서 (성공적으로 접근된 주소에서) 읽기
} uc_mem_type;
```

</details>


### uc_hook_type 

[uc_hook_add()](#uc_hook_add)의 모든 hook 유형 매개변수 

<details><summary> Code </summary> 

```cpp 
typedef enum uc_hook_type 
{ 
    // 모든 인터럽트/syscall 이벤트를 Hook 
    UC_HOOK_INTR = 1 << 0, 
    // 특정 명령어를 Hook - 매우 작은 명령어 하위 집합만 지원 
    UC_HOOK_INSN = 1 << 1, 
    // 코드 영역을 Hook 
    UC_HOOK_CODE = 1 << 2, 
    // 기본 블록을 Hook 
    UC_HOOK_BLOCK = 1 << 3, 
    // 매핑되지 않은 메모리에서 메모리 읽기 이벤트를 Hook 
    UC_HOOK_MEM_READ_UNMAPPED = 1 << 4, 
    // 잘못된 메모리 쓰기 이벤트를 Hook 
    UC_HOOK_MEM_WRITE_UNMAPPED = 1 << 5, 
    // 잘못된 메모리에서 실행 이벤트를 Hook 
    UC_HOOK_MEM_FETCH_UNMAPPED = 1 << 6, 
    // 읽기 보호된 메모리를 Hook 
    UC_HOOK_MEM_READ_PROT = 1 << 7, 
    // 쓰기 보호된 메모리를 Hook 
    UC_HOOK_MEM_WRITE_PROT = 1 << 8, 
    // 실행 불가능한 메모리에서 메모리를 Hook 
    UC_HOOK_MEM_FETCH_PROT = 1 << 9, 
    // 메모리 읽기 이벤트를 Hook 
    UC_HOOK_MEM_READ = 1 << 10, 
    // 메모리 쓰기 이벤트를 Hook 
    UC_HOOK_MEM_WRITE = 1 << 11, 
    // 메모리 실행 가져오기 이벤트를 Hook 
    UC_HOOK_MEM_FETCH = 1 << 12, 
    // 메모리 읽기 이벤트를 Hook, 성공적으로 액세스할 수 있는 주소만 허용 
    // 성공적으로 읽은 후 콜백이 트리거됨 
    UC_HOOK_MEM_READ_AFTER = 1 << 13, 
    // 잘못된 명령어 예외를 Hook 
    UC_HOOK_INSN_INVALID = 1 << 14, 
    // 새로운 (실행 흐름) 에지 생성 이벤트를 Hook. 프로그램 분석에 유용할 수 있음. 
    // 참고: 이 Hook은 UC_HOOK_BLOCK과 두 가지 측면에서 다름: 
    // 1. 이 Hook은 명령어 실행 전에 호출됨. 
    // 2. 이 Hook은 생성 이벤트가 트리거될 때만 호출됨. 
    UC_HOOK_EDGE_GENERATED = 1 << 15, 
    // 특정 tcg 연산 코드를 Hook. 사용법은 UC_HOOK_INSN과 유사함. 
    UC_HOOK_TCG_OPCODE = 1 << 16, 
} uc_hook_type; 
``` 

</details>


### hook_types 
매크로 정의 Hook 유형 

<details><summary> Code </summary> 

```cpp 
// 모든 매핑되지 않은 메모리 접근 이벤트를 Hook 
#define UC_HOOK_MEM_UNMAPPED (UC_HOOK_MEM_READ_UNMAPPED + UC_HOOK_MEM_WRITE_UNMAPPED + UC_HOOK_MEM_FETCH_UNMAPPED) 
// 보호된 메모리에 대한 모든 잘못된 접근 이벤트를 Hook 
#define UC_HOOK_MEM_PROT (UC_HOOK_MEM_READ_PROT + UC_HOOK_MEM_WRITE_PROT + UC_HOOK_MEM_FETCH_PROT) 
// 모든 잘못된 메모리 읽기 이벤트를 Hook 
#define UC_HOOK_MEM_READ_INVALID (UC_HOOK_MEM_READ_PROT + UC_HOOK_MEM_READ_UNMAPPED) 
// 모든 잘못된 메모리 쓰기 이벤트를 Hook 
#define UC_HOOK_MEM_WRITE_INVALID (UC_HOOK_MEM_WRITE_PROT + UC_HOOK_MEM_WRITE_UNMAPPED) 
// 모든 잘못된 메모리 가져오기 이벤트를 Hook 
#define UC_HOOK_MEM_FETCH_INVALID (UC_HOOK_MEM_FETCH_PROT + UC_HOOK_MEM_FETCH_UNMAPPED) 
// 모든 잘못된 메모리 접근 이벤트를 Hook 
#define UC_HOOK_MEM_INVALID (UC_HOOK_MEM_UNMAPPED + UC_HOOK_MEM_PROT) 
// 모든 유효한 메모리 접근 이벤트를 Hook 
// 참고: UC_HOOK_MEM_READ는 UC_HOOK_MEM_READ_PROT와 UC_HOOK_MEM_READ_UNMAPPED 전에 트리거되므로, 
// 이 Hook은 일부 잘못된 읽기를 트리거할 수 있음. 
#define UC_HOOK_MEM_VALID (UC_HOOK_MEM_READ + UC_HOOK_MEM_WRITE + UC_HOOK_MEM_FETCH) 
``` 

</details>


### uc_mem_region
메모리 영역은 [uc_mem_map()](#uc_mem_map)과 [uc_mem_map_ptr()](#uc_mem_map_ptr)에 의해 매핑됩니다. 
[uc_mem_regions()](#uc_mem_regions)를 사용하여 이 메모리 영역의 목록을 검색합니다.

 <details><summary> Code </summary> 
 
 ```cpp 
 typedef struct uc_mem_region { 
    uint64_t begin; // 영역 시작 주소 (포함) 
    uint64_t end; // 영역 끝 주소 (포함) 
    uint32_t perms; // 영역의 메모리 권한 
} uc_mem_region; 
``` 
</details>


### uc_query_type

[uc_query()](#uc_query)의 모든 쿼리 유형 매개변수 

<details><summary> Code </summary>

```cpp
typedef enum uc_query_type {
    // 현재 하드웨어 모드를 동적으로 쿼리
    UC_QUERY_MODE = 1,
    UC_QUERY_PAGE_SIZE, // 엔진 인스턴스의 페이지 크기를 쿼리
    UC_QUERY_ARCH, // 엔진 인스턴스의 아키텍처 유형을 쿼리
    UC_QUERY_TIMEOUT, // 시뮬레이션이 타임아웃으로 인해 중지되었는지 쿼리 (result = True이면 타임아웃임을 나타냄)
} uc_query_type;
```
</details>


### uc_control_type

[uc_ctl()](#uc_ctl)의 모든 쿼리 유형 매개변수

<details><summary> Code </summary>

```cpp
// uc_ctl의 구현은 Linux ioctl과 유사하지만 약간 다릅니다.
//
// uc_control_type은 uc_ctl에서 다음과 같이 구성됩니다:
//
//    R/W       NR       Reserved     Type
//  [      ] [      ]  [         ] [       ]
//  31    30 29     26 25       16 15      0
//
// @R/W: 작업이 읽기/쓰기 액세스인지 여부.
// @NR: 매개변수 수.
// @Reserved: 0이며 향후 확장을 위해 예약됨.
// @Type: uc_control_type의 열거형.

// 입력 및 출력 매개변수가 없습니다.
#define UC_CTL_IO_NONE (0)
// 쓰기 작업을 위한 입력 매개변수만 있습니다.
#define UC_CTL_IO_WRITE (1)
// 읽기 작업을 위한 출력 매개변수만 있습니다.
#define UC_CTL_IO_READ (2)
// 매개변수에 읽기와 쓰기 작업이 모두 포함되어 있습니다.
#define UC_CTL_IO_READ_WRITE (UC_CTL_IO_WRITE | UC_CTL_IO_READ)

#define UC_CTL(type, nr, rw)                                                   \
    (uc_control_type)((type) | ((nr) << 26) | ((rw) << 30))
#define UC_CTL_NONE(type, nr) UC_CTL(type, nr, UC_CTL_IO_NONE)
#define UC_CTL_READ(type, nr) UC_CTL(type, nr, UC_CTL_IO_READ)
#define UC_CTL_WRITE(type, nr) UC_CTL(type, nr, UC_CTL_IO_WRITE)
#define UC_CTL_READ_WRITE(type, nr) UC_CTL(type, nr, UC_CTL_IO_READ_WRITE)
```

```cpp
// 제어는 트리 구조로 구성됩니다.
// 제어 상태가 @args에 대해 `Set` 또는 `Get`을 지정하지 않으면 r/o 또는 w/o입니다.
typedef enum uc_control_type {
    // 현재 모드
    // Read: @args = (int*)
    UC_CTL_UC_MODE = 0,
    // 현재 page size.
    // Write: @args = (uint32_t)
    // Read: @args = (uint32_t*)
    UC_CTL_UC_PAGE_SIZE,
    // 현재 아키텍쳐
    // Read: @args = (int*)
    UC_CTL_UC_ARCH,
    // 현재 타임아웃
    // Read: @args = (uint64_t*)
    UC_CTL_UC_TIMEOUT,
    // 여러 개의 종료 지점을 허용합니다.
    // 이 제어 상태가 없으면 종료 지점을 읽거나 설정할 수 없습니다.
    // Write: @args = (int)
    UC_CTL_UC_USE_EXITS,
    // 현재 입력 수
    // Read: @args = (size_t*)
    UC_CTL_UC_EXITS_CNT,
    // 현재 입력
    // Write: @args = (uint64_t* exits, size_t len)
    //        @len = UC_CTL_UC_EXITS_CNT
    // Read: @args = (uint64_t* exits, size_t len)
    //       @len = UC_CTL_UC_EXITS_CNT
    UC_CTL_UC_EXITS,

    // uc인스턴스의 CPU모드를 설정합니다.
    // Note this option can only be set before any Unicorn
    // API is called except for uc_open.
    // Write: @args = (int)
    // Read:  @args = (int*)
    UC_CTL_CPU_MODEL,
    // 특정 주소의 tb(변환 블록) 캐시를 쿼리합니다.
    // Read: @args = (uint64_t, uc_tb*)
    UC_CTL_TB_REQUEST_CACHE,
    // 특정 주소의 tb(변환 블록) 캐시를 비활성화합니다.
    // Write: @args = (uint64_t, uint64_t)
    UC_CTL_TB_REMOVE_CACHE,
    // 모든 tb(변환 블록)을 비활성화합니다.
    // 매개변수 없음
    UC_CTL_TB_FLUSH
} uc_control_type;
```

</details>


### uc_context

uc_context_*() 함수와 함께 사용되며, CPU 컨텍스트의 불투명한 저장을 관리합니다.

<details><summary> Code </summary>

```cpp
struct uc_context;
typedef struct uc_context uc_context;
```

</details>


### uc_prot

새로 매핑된 영역의 권한

<details><summary> Code </summary>

```cpp
typedef enum uc_prot {
UC_PROT_NONE = 0, // 권한 없음
UC_PROT_READ = 1, // 읽기
UC_PROT_WRITE = 2, // 쓰기
UC_PROT_EXEC = 4, // 실행 가능
UC_PROT_ALL = 7, // 모든 권한
} uc_prot;
```

</details>


## 0x2 API

> 索引

[uc_version](#uc_version)

[uc_arch_supported](#uc_arch_supported)

[uc_open](#uc_open)

[uc_close](#uc_close)

[uc_query](#uc_query)

[uc_errno](#uc_errno)

[uc_strerror](#uc_strerror)

[uc_reg_write](#uc_reg_write)

[uc_reg_read](#uc_reg_read)

[uc_reg_write_batch](#uc_reg_write_batch)

[uc_reg_read_batch](#uc_reg_read_batch)

[uc_mem_write](#uc_mem_write)

[uc_mem_read](#uc_mem_read)

[uc_emu_start](#uc_emu_start)

[uc_emu_stop](#uc_emu_stop)

[uc_hook_add](#uc_hook_add)

[uc_hook_del](#uc_hook_del)

[uc_mem_map](#uc_mem_map)

[uc_mem_map_ptr](#uc_mem_map_ptr)

[uc_mem_unmap](#uc_mem_unmap)

[uc_mem_protect](#uc_mem_protect)

[uc_mem_regions](#uc_mem_regions)

[uc_free](#uc_free)

[uc_context_alloc](#uc_context_alloc)

[uc_context_save](#uc_context_save)

[uc_context_restore](#uc_context_restore)

[uc_context_size](#uc_context_size)

[uc_context_free](#uc_context_free)

---

### uc_version

```cpp
unsigned int uc_version(unsigned int *major, unsigned int *minor);
```

Unicorn API의 주 버전과 부 버전 정보를 반환하는 데 사용됩니다.
```
@major: API 주 버전 번호
@minor: API 부 버전 번호
@return 16진수, 계산 방식: (major << 8 | minor)
힌트: 이 반환값은 UC_MAKE_VERSION 매크로와 비교할 수 있습니다.
```

<details><summary> 소스코드 구현 </summary>

```c
unsigned int uc_version(unsigned int *major, unsigned int *minor)
{
    if (major != NULL && minor != NULL) {
        *major = UC_API_MAJOR;  //매크로
        *minor = UC_API_MINOR;  //매크로
    }

    return (UC_API_MAJOR << 8) + UC_API_MINOR;   //(major << 8 | minor)
}
```

</details>


컴파일 후에는 변경할 수 없으며, 사용자 정의 버전을 허용하지 않습니다.

사용 예시:

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    unsigned int version;
    version = uc_version(NULL,NULL);
    cout << hex << version << endl;
    return 0;
}
```

출력：

![image.png](API_Doc_Pic/1_8.png)

버전 번호 얻기1.0.0



### uc_arch_supported

```c
bool uc_arch_supported(uc_arch arch);
```

현재 아키텍처가 Unicorn에서 지원되는지 확인

```
 @arch: 아키텍처 유형 (UC_ARCH_*)
 @return 지원되는 경우 True 반환
```

<details><summary> 소스코드 구현 </summary>

```c
bool uc_arch_supported(uc_arch arch)
{
    switch (arch) {
#ifdef UNICORN_HAS_ARM
        case UC_ARCH_ARM:   return true;
#endif
#ifdef UNICORN_HAS_ARM64
        case UC_ARCH_ARM64: return true;
#endif
#ifdef UNICORN_HAS_M68K
        case UC_ARCH_M68K:  return true;
#endif
#ifdef UNICORN_HAS_MIPS
        case UC_ARCH_MIPS:  return true;
#endif
#ifdef UNICORN_HAS_PPC
        case UC_ARCH_PPC:   return true;
#endif
#ifdef UNICORN_HAS_SPARC
        case UC_ARCH_SPARC: return true;
#endif
#ifdef UNICORN_HAS_X86
        case UC_ARCH_X86:   return true;
#endif
        /* 잘못되었거나 지원하지 않는 아키텍처 */
        default:            return false;
    }
}
```

</details>


사용 예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    cout << "UC_ARCH_X86아키텍처 지원 여부：" << uc_arch_supported(UC_ARCH_X86) << endl;
    return 0;
}
```

출력：

![image.png](API_Doc_Pic/1_9.png)



### uc_open

```c
uc_err uc_open(uc_arch arch, uc_mode mode, uc_engine **uc);
```

새로운 Unicorn 인스턴스 생성

```
@arch: 아키텍처 유형 (UC_ARCH_)
@mode: 하드웨어 모드. UC_MODE_ 값들의 조합
@uc: uc_engine 포인터, 반환 시 업데이트됨

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 유형 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_open(uc_arch arch, uc_mode mode, uc_engine **result)
{
    struct uc_struct *uc;

    if (arch < UC_ARCH_MAX) {
        uc = calloc(1, sizeof(*uc));  //메모리 할당
        if (!uc) {
            // 메모리 부족
            return UC_ERR_NOMEM;
        }

        uc->errnum = UC_ERR_OK;
        uc->arch = arch;
        uc->mode = mode;

        // 초기화
        // uc->ram_list = { .blocks = QTAILQ_HEAD_INITIALIZER(ram_list.blocks) };
        uc->ram_list.blocks.tqh_first = NULL;
        uc->ram_list.blocks.tqh_last = &(uc->ram_list.blocks.tqh_first);

        uc->memory_listeners.tqh_first = NULL;
        uc->memory_listeners.tqh_last = &uc->memory_listeners.tqh_first;

        uc->address_spaces.tqh_first = NULL;
        uc->address_spaces.tqh_last = &uc->address_spaces.tqh_first;

        switch(arch) {   // 아키텍처에 따른 전처리
            default:
                break;
#ifdef UNICORN_HAS_M68K
            case UC_ARCH_M68K:
                if ((mode & ~UC_MODE_M68K_MASK) ||
                        !(mode & UC_MODE_BIG_ENDIAN)) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                uc->init_arch = m68k_uc_init;
                break;
#endif
#ifdef UNICORN_HAS_X86
            case UC_ARCH_X86:
                if ((mode & ~UC_MODE_X86_MASK) ||
                        (mode & UC_MODE_BIG_ENDIAN) ||
                        !(mode & (UC_MODE_16|UC_MODE_32|UC_MODE_64))) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                uc->init_arch = x86_uc_init;
                break;
#endif
#ifdef UNICORN_HAS_ARM
            case UC_ARCH_ARM:
                if ((mode & ~UC_MODE_ARM_MASK)) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                if (mode & UC_MODE_BIG_ENDIAN) {
                    uc->init_arch = armeb_uc_init;
                } else {
                    uc->init_arch = arm_uc_init;
                }

                if (mode & UC_MODE_THUMB)
                    uc->thumb = 1;
                break;
#endif
#ifdef UNICORN_HAS_ARM64
            case UC_ARCH_ARM64:
                if (mode & ~UC_MODE_ARM_MASK) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                if (mode & UC_MODE_BIG_ENDIAN) {
                    uc->init_arch = arm64eb_uc_init;
                } else {
                    uc->init_arch = arm64_uc_init;
                }
                break;
#endif

#if defined(UNICORN_HAS_MIPS) || defined(UNICORN_HAS_MIPSEL) || defined(UNICORN_HAS_MIPS64) || defined(UNICORN_HAS_MIPS64EL)
            case UC_ARCH_MIPS:
                if ((mode & ~UC_MODE_MIPS_MASK) ||
                        !(mode & (UC_MODE_MIPS32|UC_MODE_MIPS64))) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                if (mode & UC_MODE_BIG_ENDIAN) {
#ifdef UNICORN_HAS_MIPS
                    if (mode & UC_MODE_MIPS32)
                        uc->init_arch = mips_uc_init;
#endif
#ifdef UNICORN_HAS_MIPS64
                    if (mode & UC_MODE_MIPS64)
                        uc->init_arch = mips64_uc_init;
#endif
                } else {    // LITTLE ENDIAN
#ifdef UNICORN_HAS_MIPSEL
                    if (mode & UC_MODE_MIPS32)
                        uc->init_arch = mipsel_uc_init;
#endif
#ifdef UNICORN_HAS_MIPS64EL
                    if (mode & UC_MODE_MIPS64)
                        uc->init_arch = mips64el_uc_init;
#endif
                }
                break;
#endif

#ifdef UNICORN_HAS_SPARC
            case UC_ARCH_SPARC:
                if ((mode & ~UC_MODE_SPARC_MASK) ||
                        !(mode & UC_MODE_BIG_ENDIAN) ||
                        !(mode & (UC_MODE_SPARC32|UC_MODE_SPARC64))) {
                    free(uc);
                    return UC_ERR_MODE;
                }
                if (mode & UC_MODE_SPARC64)
                    uc->init_arch = sparc64_uc_init;
                else
                    uc->init_arch = sparc_uc_init;
                break;
#endif
        }

        if (uc->init_arch == NULL) {
            return UC_ERR_ARCH;
        }

        if (machine_initialize(uc))
            return UC_ERR_RESOURCE;

        *result = uc;

        if (uc->reg_reset)
            uc->reg_reset(uc);

        return UC_ERR_OK;
    } else {
        return UC_ERR_ARCH;
    }
}
```

</details>


**주의: uc_open은 힙 메모리를 할당하므로, 사용 후에는 반드시 uc_close로 해제해야 합니다. 그렇지 않으면 메모리 누수가 발생할 수 있습니다.**

사용 예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_engine* uc;
    uc_err err;

    //// X86-32비트 모드 에뮬레이터 초기화
    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
            return -1;
    }

    if (!err)
        cout << "uc엔진 생성 성공" << endl;

    //// uc종료
    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }

    if (!err)
        cout << "uc엔진이 성공적으로 종료됨" << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_10.png)



### uc_close

```c
uc_err uc_close(uc_engine *uc);
```

uc 인스턴스를 종료하면 메모리가 해제됩니다. 종료 후에는 복구할 수 없습니다.

```
@uc: uc_open()이 반환한 포인터

@return 성공시 UC_ERR_OK 반환, 그렇지 않은 경우 uc_err 열거형의 다른 오류 유형 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_close(uc_engine *uc)
{
    int i;
    struct list_item *cur;
    struct hook *hook;

    // 내부 데이터 정리
    if (uc->release)
        uc->release(uc->tcg_ctx);
    g_free(uc->tcg_ctx);

    // CPU 정리.
    g_free(uc->cpu->tcg_as_listener);
    g_free(uc->cpu->thread);

    // 모든 objects 정리.
    OBJECT(uc->machine_state->accelerator)->ref = 1;
    OBJECT(uc->machine_state)->ref = 1;
    OBJECT(uc->owner)->ref = 1;
    OBJECT(uc->root)->ref = 1;

    object_unref(uc, OBJECT(uc->machine_state->accelerator));
    object_unref(uc, OBJECT(uc->machine_state));
    object_unref(uc, OBJECT(uc->cpu));
    object_unref(uc, OBJECT(&uc->io_mem_notdirty));
    object_unref(uc, OBJECT(&uc->io_mem_unassigned));
    object_unref(uc, OBJECT(&uc->io_mem_rom));
    object_unref(uc, OBJECT(uc->root));

    // 메모리 해제
    g_free(uc->system_memory);

    // 관련 스레드 해제
    if (uc->qemu_thread_data)
        g_free(uc->qemu_thread_data);

    // 기타 데이터 해제
    free(uc->l1_map);

    if (uc->bounce.buffer) {
        free(uc->bounce.buffer);
    }

    g_hash_table_foreach(uc->type_table, free_table, uc);
    g_hash_table_destroy(uc->type_table);

    for (i = 0; i < DIRTY_MEMORY_NUM; i++) {
        free(uc->ram_list.dirty_memory[i]);
    }

    // hook과 hook리스트를 해제
    for (i = 0; i < UC_HOOK_MAX; i++) {
        cur = uc->hook[i].head;
        // hook은 여러 리스트에 존재할 수 있으며, 카운트를 통해 해제 시점을 결정할 수 있습니다.
        while (cur) {
            hook = (struct hook *)cur->data;
            if (--hook->refs == 0) {
                free(hook);
            }
            cur = cur->next;
        }
        list_clear(&uc->hook[i]);
    }

    free(uc->mapped_blocks);

    // 마지막으로 uc자체를 해제
    memset(uc, 0, sizeof(*uc));
    free(uc);

    return UC_ERR_OK;
}
```

</details>

사용예제는 [uc_open()](#uc_open)과 같습니다



### uc_query

```c
uc_err uc_query(uc_engine *uc, uc_query_type type, size_t *result);
```

엔진의 내부 상태를 쿼리합니다

```
 @uc: uc_open()에서 반환된 핸들
 @type: uc_query_type 열거형의 타입
 
 @result: 조회된 내부 상태를 저장할 포인터
 
 @return: 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_query(uc_engine *uc, uc_query_type type, size_t *result)
{
    if (type == UC_QUERY_PAGE_SIZE) {
        *result = uc->target_page_size;
        return UC_ERR_OK;
    }

    if (type == UC_QUERY_ARCH) {
        *result = uc->arch;
        return UC_ERR_OK;
    }

    switch(uc->arch) {
#ifdef UNICORN_HAS_ARM
        case UC_ARCH_ARM:
            return uc->query(uc, type, result);
#endif
        default:
            return UC_ERR_ARG;
    }

    return UC_ERR_OK;
}
```

</details>

사용예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;
int main()
{
    uc_engine* uc;
    uc_err err;

    //// Initialize emulator in X86-32bit mode
    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스 생성 성공" << endl;

    size_t result[] = {0};
    err = uc_query(uc, UC_QUERY_ARCH, result);   // 아키텍쳐 조회
    if (!err)
        cout << "조회 성공: " << *result << endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc 인스턴스가 성공적으로 종료되었습니다." << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_11.png)

아키텍처 조회 결과가 4이며, 이는 UC_ARCH_X86에 해당합니다.



### uc_errno

```c
uc_err uc_errno(uc_engine *uc);
```

어떤 API 함수가 실패할 때, 마지막 오류 번호를 보고합니다. 일단 액세스되면 uc_errno는 원래 값을 유지하지 않을 수 있습니다.

```
@uc: uc_open()가 반환한 핸들

@return: 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_errno(uc_engine *uc)
{
    return uc->errnum;
}
```

</details>

사용 예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_engine* uc;
    uc_err err;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스 생성 성공" << endl;

    err = uc_errno(uc);
    cout << "오류 번호： " << err << endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc 인스턴스가 성공적으로 종료 되었습니다" << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_12.png)

오류가 없으므로 출력된 오류 번호는 0입니다.



### uc_strerror

```c
const char *uc_strerror(uc_err code);
```

주어진 오류 번호에 대한 설명을 반환합니다.

```
 @code: 오류 번호

 @return: 주어진 오류 번호에 대한 설명이 담긴 문자열 포인터
```

<details><summary> 소스코드 구현 </summary>

```cpp
const char *uc_strerror(uc_err code)
{
    switch(code) {
        default:
            return "Unknown error code";
        case UC_ERR_OK:
            return "OK (UC_ERR_OK)";
        case UC_ERR_NOMEM:
            return "No memory available or memory not present (UC_ERR_NOMEM)";
        case UC_ERR_ARCH:
            return "Invalid/unsupported architecture (UC_ERR_ARCH)";
        case UC_ERR_HANDLE:
            return "Invalid handle (UC_ERR_HANDLE)";
        case UC_ERR_MODE:
            return "Invalid mode (UC_ERR_MODE)";
        case UC_ERR_VERSION:
            return "Different API version between core & binding (UC_ERR_VERSION)";
        case UC_ERR_READ_UNMAPPED:
            return "Invalid memory read (UC_ERR_READ_UNMAPPED)";
        case UC_ERR_WRITE_UNMAPPED:
            return "Invalid memory write (UC_ERR_WRITE_UNMAPPED)";
        case UC_ERR_FETCH_UNMAPPED:
            return "Invalid memory fetch (UC_ERR_FETCH_UNMAPPED)";
        case UC_ERR_HOOK:
            return "Invalid hook type (UC_ERR_HOOK)";
        case UC_ERR_INSN_INVALID:
            return "Invalid instruction (UC_ERR_INSN_INVALID)";
        case UC_ERR_MAP:
            return "Invalid memory mapping (UC_ERR_MAP)";
        case UC_ERR_WRITE_PROT:
            return "Write to write-protected memory (UC_ERR_WRITE_PROT)";
        case UC_ERR_READ_PROT:
            return "Read from non-readable memory (UC_ERR_READ_PROT)";
        case UC_ERR_FETCH_PROT:
            return "Fetch from non-executable memory (UC_ERR_FETCH_PROT)";
        case UC_ERR_ARG:
            return "Invalid argument (UC_ERR_ARG)";
        case UC_ERR_READ_UNALIGNED:
            return "Read from unaligned memory (UC_ERR_READ_UNALIGNED)";
        case UC_ERR_WRITE_UNALIGNED:
            return "Write to unaligned memory (UC_ERR_WRITE_UNALIGNED)";
        case UC_ERR_FETCH_UNALIGNED:
            return "Fetch from unaligned memory (UC_ERR_FETCH_UNALIGNED)";
        case UC_ERR_RESOURCE:
            return "Insufficient resource (UC_ERR_RESOURCE)";
        case UC_ERR_EXCEPTION:
            return "Unhandled CPU exception (UC_ERR_EXCEPTION)";
        case UC_ERR_TIMEOUT:
            return "Emulation timed out (UC_ERR_TIMEOUT)";
    }
}
```

</details>

사용예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_engine* uc;
    uc_err err;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc 인스턴스 생성 성공" << endl;

    err = uc_errno(uc);
    cout << "오류번호： " << err << "  오류 내용： " << uc_strerror(err) <<endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스를 성공적으로 종료했습니다." << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_13.png)



### uc_reg_write

```c
uc_err uc_reg_write(uc_engine *uc, int regid, const void *value);
```

값을 레지스터에 쓰기

```
@uc: uc_open()에서 반환된 핸들
@regid: 수정될 레지스터의 ID
@value: 레지스터가 수정될 값의 포인터

@return 성공하면 UC_ERR_OK를 반환하고, 그렇지 않으면 uc_err 열거형의 다른 오류 코드를 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_reg_write(uc_engine *uc, int regid, const void *value)
{
    return uc_reg_write_batch(uc, &regid, (void *const *)&value, 1);
}

uc_err uc_reg_write_batch(uc_engine *uc, int *ids, void *const *vals, int count)
{
    int ret = UC_ERR_OK;
    if (uc->reg_write)
        ret = uc->reg_write(uc, (unsigned int *)ids, vals, count);    //结构体中写入
    else
        return UC_ERR_EXCEPTION;

    return ret;
}
```

</details>

사용예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_engine* uc;
    uc_err err;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스 생성 성공" << endl;

    int r_eax = 0x12;
    err = uc_reg_write(uc, UC_X86_REG_ECX, &r_eax);
    if (!err)
        cout << "쓰기 성공: " << r_eax << endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스가 성공적으로 종료되었습니다." << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_14.png)



### uc_reg_read

```c
uc_err uc_reg_read(uc_engine *uc, int regid, void *value);
```

레지스터 값 읽기

```
@uc: uc_open()가 반환한 핸들
@regid:  읽을 레지스터의 ID
@value:  레지스터의 값을 저장할 포인터

@return 성공하면 UC_ERR_OK를 반환하고, 그렇지 않으면 uc_err 열거형의 다른 오류 코드를 반환합니다
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_reg_read(uc_engine *uc, int regid, void *value)
{
    return uc_reg_read_batch(uc, &regid, &value, 1);
}

uc_err uc_reg_read_batch(uc_engine *uc, int *ids, void **vals, int count)
{
    if (uc->reg_read)
        uc->reg_read(uc, (unsigned int *)ids, vals, count);
    else
        return -1;

    return UC_ERR_OK;
}
```

</details>

사용예시：

```cpp
#include <iostream>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_engine* uc;
    uc_err err;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스 생성 성공" << endl;

    int r_eax = 0x12;
    err = uc_reg_write(uc, UC_X86_REG_ECX, &r_eax);
    if (!err)
        cout << "쓰기 성공: " << r_eax << endl;

    int recv_eax;
    err = uc_reg_read(uc, UC_X86_REG_ECX, &recv_eax);
    if (!err)
        cout << "읽기 성공: " << recv_eax << endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    if (!err)
        cout << "uc인스턴스가 성공적으로 종료 되었습니다" << endl;

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_15.png)



### uc_reg_write_batch

```c
uc_err uc_reg_write_batch(uc_engine *uc, int *regs, void *const *vals, int count);
```

여러 레지스터에 동시에 여러 값 쓰기

```
@uc: uc_open()에서 반환된 핸들
@regid: 쓰기 작업이 수행될 여러 레지스터 ID를 저장하는 배열
@value: 여러 값을 저장하는 배열을 가리키는 포인터
@count: *regs와 *vals 배열의 길이

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_reg_write_batch(uc_engine *uc, int *ids, void *const *vals, int count)
{
    int ret = UC_ERR_OK;
    if (uc->reg_write)
        ret = uc->reg_write(uc, (unsigned int *)ids, vals, count);
    else
        return UC_ERR_EXCEPTION;

    return ret;
}
```

</details>

사용예시:

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

int syscall_abi[] = {
    UC_X86_REG_RAX, UC_X86_REG_RDI, UC_X86_REG_RSI, UC_X86_REG_RDX,
    UC_X86_REG_R10, UC_X86_REG_R8, UC_X86_REG_R9
};

uint64_t vals[7] = { 200, 10, 11, 12, 13, 14, 15 };

void* ptrs[7];

int main()
{
    int i;
    uc_err err;
    uc_engine* uc;

    // set up register pointers
    for (i = 0; i < 7; i++) {
        ptrs[i] = &vals[i];
    }

    if ((err = uc_open(UC_ARCH_X86, UC_MODE_64, &uc))) {
        uc_perror("uc_open", err);
        return 1;
    }

    // reg_write_batch
    printf("reg_write_batch({200, 10, 11, 12, 13, 14, 15})\n");
    if ((err = uc_reg_write_batch(uc, syscall_abi, ptrs, 7))) {
        uc_perror("uc_reg_write_batch", err);
        return 1;
    }

    // reg_read_batch
    memset(vals, 0, sizeof(vals));
    if ((err = uc_reg_read_batch(uc, syscall_abi, ptrs, 7))) {
        uc_perror("uc_reg_read_batch", err);
        return 1;
    }

    printf("reg_read_batch = {");

    for (i = 0; i < 7; i++) {
        if (i != 0) printf(", ");
        printf("%" PRIu64, vals[i]);
    }

    printf("}\n");

    uint64_t var[7] = { 0 };
    for (int i = 0; i < 7; i++)
    {
        cout << syscall_abi[i] << "   ";
        printf("%" PRIu64, vals[i]);
        cout << endl;
    }

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_16.png)



### uc_reg_read_batch

```c
uc_err uc_reg_read_batch(uc_engine *uc, int *regs, void **vals, int count);
```

여러 레지스터의 값을 동시에 읽기.

```
@uc: uc_open()에서 반환된 핸들
@regid: 읽기 작업이 수행될 여러 레지스터 ID를 저장하는 배열
@value: 여러 값을 저장할 배열을 가리키는 포인터
@count: *regs와 *vals 배열의 길이

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_reg_read_batch(uc_engine *uc, int *ids, void **vals, int count)
{
    if (uc->reg_read)
        uc->reg_read(uc, (unsigned int *)ids, vals, count);
    else
        return -1;

    return UC_ERR_OK;
}
```

</details>

사용 예시는 uc_reg_write_batch()와 같다.



### uc_mem_write

```c
uc_err uc_mem_write(uc_engine *uc, uint64_t address, const void *bytes, size_t size);
```

메모리에 바이트 코드 한 부분을 씁니다.

```
@uc: uc_open()에서 반환된 핸들
@address: 바이트를 쓰기 시작할 주소
@bytes: 메모리에 쓸 데이터를 포함하는 포인터
@size: 쓰려는 메모리의 크기.

참고: @bytes는 @size 바이트를 포함할 수 있을 만큼 충분히 커야 합니다.

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_write(uc_engine *uc, uint64_t address, const void *_bytes, size_t size)
{
    size_t count = 0, len;
    const uint8_t *bytes = _bytes;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    if (!check_mem_area(uc, address, size))
        return UC_ERR_WRITE_UNMAPPED;

    // 메모리 영역은 인접한 메모리 블록과 겹칠 수 있습니다.
    while(count < size) {
        MemoryRegion *mr = memory_mapping(uc, address);
        if (mr) {
            uint32_t operms = mr->perms;
            if (!(operms & UC_PROT_WRITE)) // 쓰기 보호가 없습니다
                // 쓰기 가능으로 표시합니다
                uc->readonly_mem(mr, false);

            len = (size_t)MIN(size - count, mr->end - address);
            if (uc->write_mem(&uc->as, address, bytes, len) == false)
                break;

            if (!(operms & UC_PROT_WRITE)) // 쓰기 보호가 없습니다
                // 쓰기 보호를 설정합니다
                uc->readonly_mem(mr, true);

            count += len;
            address += len;
            bytes += len;
        } else  // 이 주소는 아직 매핑되지 않았습니다.
            break;
    }

    if (count == size)
        return UC_ERR_OK;
    else
        return UC_ERR_WRITE_UNMAPPED;
}
```

</details>

사용예시:

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

#define X86_CODE32 "\x41\x4a" // INC ecx; DEC edx
#define ADDRESS 0x1000

int main()
{
    uc_engine* uc;
    uc_err err;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }

    uc_mem_map(uc, ADDRESS, 2 * 1024 * 1024, UC_PROT_ALL);

    if (uc_mem_write(uc, ADDRESS, X86_CODE32, sizeof(X86_CODE32) - 1)) {
        printf("Failed to write emulation code to memory, quit!\n");
        return -1;
    }

    uint32_t code;

    if(uc_mem_read(uc,ADDRESS,&code, sizeof(code))) {
        printf("Failed to read emulation code to memory, quit!\n");
        return -1;
    }

    cout << hex << code << endl;

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }
    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_17.png)



### uc_mem_read

```c
uc_err uc_mem_read(uc_engine *uc, uint64_t address, void *bytes, size_t size);
```

메모리에서 바이트를 읽습니다.

```
 @uc: uc_open()에서 반환된 핸들
 @address: 바이트를 읽기 시작할 주소
 @bytes: 메모리에서 읽은 데이터를 포함할 포인터
 @size: 읽으려는 메모리의 크기.
 
 참고: @bytes는 @size 바이트를 포함할 수 있을 만큼 충분히 커야 합니다.
 
 @return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_read(uc_engine *uc, uint64_t address, void *_bytes, size_t size)
{
    size_t count = 0, len;
    uint8_t *bytes = _bytes;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    if (!check_mem_area(uc, address, size))
        return UC_ERR_READ_UNMAPPED;

    // 메모리 영역은 인접한 메모리 블록과 겹칠 수 있습니다.
    while(count < size) {
        MemoryRegion *mr = memory_mapping(uc, address);
        if (mr) {
            len = (size_t)MIN(size - count, mr->end - address);
            if (uc->read_mem(&uc->as, address, bytes, len) == false)
                break;
            count += len;
            address += len;
            bytes += len;
        } else  // 이 주소는 아직 매핑되지 않았습니다.
            break;
    }

    if (count == size)
        return UC_ERR_OK;
    else
        return UC_ERR_READ_UNMAPPED;
}
```

</details>

사용예시는 [uc_mem_write()](#uc_mem_write)와 같습니다



### uc_emu_start

```c
uc_err uc_emu_start(uc_engine *uc, uint64_t begin, uint64_t until, uint64_t timeout, size_t count);
```

지정된 시간 동안 기계어를 에뮬레이션합니다.

```
@uc: uc_open()에서 반환된 핸들
@begin: 에뮬레이션을 시작할 주소
@until: 에뮬레이션이 중단되는 주소 (해당 주소에 도달했을 때)
@timeout: 에뮬레이션 코드의 지속 시간(마이크로초 단위). 이 값이 0이면 에뮬레이션이 완료될 때까지 시간 제한 없이 코드를 에뮬레이션합니다.

@count: 에뮬레이션할 명령어 수. 이 값이 0이면 에뮬레이션이 완료될 때까지 실행 가능한 모든 코드를 에뮬레이션합니다.
@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_emu_start(uc_engine* uc, uint64_t begin, uint64_t until, uint64_t timeout, size_t count)
{
    // 카운터 초기화
    uc->emu_counter = 0;
    uc->invalid_error = UC_ERR_OK;
    uc->block_full = false;
    uc->emulation_done = false;
    uc->timed_out = false;

    switch(uc->arch) {
        default:
            break;
#ifdef UNICORN_HAS_M68K
        case UC_ARCH_M68K:
            uc_reg_write(uc, UC_M68K_REG_PC, &begin);
            break;
#endif
#ifdef UNICORN_HAS_X86
        case UC_ARCH_X86:
            switch(uc->mode) {
                default:
                    break;
                case UC_MODE_16: {
                    uint64_t ip;
                    uint16_t cs;

                    uc_reg_read(uc, UC_X86_REG_CS, &cs);
                    // IP와 CS 증가분 상쇄
                    ip = begin - cs*16;
                    uc_reg_write(uc, UC_X86_REG_IP, &ip);
                    break;
                }
                case UC_MODE_32:
                    uc_reg_write(uc, UC_X86_REG_EIP, &begin);
                    break;
                case UC_MODE_64:
                    uc_reg_write(uc, UC_X86_REG_RIP, &begin);
                    break;
            }
            break;
#endif
#ifdef UNICORN_HAS_ARM
        case UC_ARCH_ARM:
            uc_reg_write(uc, UC_ARM_REG_R15, &begin);
            break;
#endif
#ifdef UNICORN_HAS_ARM64
        case UC_ARCH_ARM64:
            uc_reg_write(uc, UC_ARM64_REG_PC, &begin);
            break;
#endif
#ifdef UNICORN_HAS_MIPS
        case UC_ARCH_MIPS:
            // TODO: MIPS32/MIPS64/BIGENDIAN etc
            uc_reg_write(uc, UC_MIPS_REG_PC, &begin);
            break;
#endif
#ifdef UNICORN_HAS_SPARC
        case UC_ARCH_SPARC:
            // TODO: Sparc/Sparc64
            uc_reg_write(uc, UC_SPARC_REG_PC, &begin);
            break;
#endif
    }

    uc->stop_request = false;

    uc->emu_count = count;
    // 카운트가 필요하지 않은 경우, 카운트 훅을 제거합니다.
    if (count <= 0 && uc->count_hook != 0) {
        uc_hook_del(uc, uc->count_hook);
        uc->count_hook = 0;
    }
    // 명령어 수를 기록하기 위해 카운트 훅을 설정합니다.
    if (count > 0 && uc->count_hook == 0) {
        uc_err err;
        // 카운트 명령어에 대한 콜백은 다른 모든 작업 전에 실행되어야 하므로, 훅을 훅 리스트의 끝에 추가하는 것이 아니라 시작 부분에 삽입해야 합니다.
        uc->hook_insert = 1;
        err = uc_hook_add(uc, &uc->count_hook, UC_HOOK_CODE, hook_count_cb, NULL, 1, 0);
        // uc_hook_add()로 복원합니다
        uc->hook_insert = 0;
        if (err != UC_ERR_OK) {
            return err;
        }
    }

    uc->addr_end = until;

    if (timeout)
        enable_emu_timer(uc, timeout * 1000);   // microseconds -> nanoseconds

    if (uc->vm_start(uc)) {
        return UC_ERR_RESOURCE;
    }

    // 에뮬레이션 완료
    uc->emulation_done = true;

    if (timeout) {
        // 대기 중 타임아웃 발생
        qemu_thread_join(&uc->timer);
    }

    if(uc->timed_out)
        return UC_ERR_TIMEOUT;

    return uc->invalid_error;
}
```

</details>

사용예시:

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

#define X86_CODE32 "\x33\xC0" // xor  eax, eax
#define ADDRESS 0x1000

int main()
{
    uc_engine* uc;
    uc_err err;

    int r_eax = 0x111;

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return -1;
    }

    uc_mem_map(uc, ADDRESS, 2 * 1024 * 1024, UC_PROT_ALL);

    if (uc_mem_write(uc, ADDRESS, X86_CODE32, sizeof(X86_CODE32) - 1)) {
        printf("Failed to write emulation code to memory, quit!\n");
        return -1;
    }

    uc_reg_write(uc, UC_X86_REG_EAX, &r_eax);
    printf(">>> before EAX = 0x%x\n", r_eax);

    err = uc_emu_start(uc, ADDRESS, ADDRESS + sizeof(X86_CODE32) - 1, 0, 0);
    if (err) {
        printf("Failed on uc_emu_start() with error returned %u: %s\n",
        err, uc_strerror(err));
    }

    uc_reg_read(uc, UC_X86_REG_EAX, &r_eax);
    printf(">>> after EAX = 0x%x\n", r_eax);

    err = uc_close(uc);
    if (err != UC_ERR_OK) {
        printf("Failed on uc_close() with error returned: %u\n", err);
        return -1;
    }

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_18.png)



### uc_emu_stop

```c
uc_err uc_emu_stop(uc_engine *uc);
```

에뮬레이션 중지. 

일반적으로 추적 API를 통해 등록된 콜백 함수에서 호출됩니다.


```
@uc: uc_open()에서 반환된 핸들

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_emu_stop(uc_engine *uc)
{
    if (uc->emulation_done)
        return UC_ERR_OK;

    uc->stop_request = true;

    if (uc->current_cpu) {
        // 현재 스레드 종료
        cpu_exit(uc->current_cpu);
    }

    return UC_ERR_OK;
}
```

</details>

사용예시:

```cpp
uc_emu_stop(uc);
```



### uc_hook_add

```c
uc_err uc_hook_add(uc_engine *uc, uc_hook *hh, int type, void *callback,
        void *user_data, uint64_t begin, uint64_t end, ...);
```

hook 이벤트의 콜백을 등록합니다. hook 이벤트가 트리거되면 콜백이 호출됩니다.

```
 @uc: uc_open()에서 반환된 핸들
 @hh: 등록된 hook에서 얻은 핸들. uc_hook_del()에서 사용됨
 @type: hook 유형
 @callback: 명령어가 일치할 때 실행할 콜백 함수
 @user_data: 사용자 정의 데이터. 콜백 함수의 마지막 인자로 전달됨
 @begin: 콜백이 적용되는 영역의 시작 주소(포함)
 @end: 콜백이 적용되는 영역의 끝 주소(포함)  
  주의 1: 콜백은 주소가 [@begin, @end] 범위 내에 있는 경우에만 호출됨
  주의 2: @begin > @end인 경우, 이 hook 유형이 트리거될 때마다 콜백이 호출됨
 @...: 가변 인자 (@type에 따라 다름)
  주의: @type = UC_HOOK_INSN인 경우, 여기에는 명령어 ID(예: UC_X86_INS_OUT)가 옴
 
 @return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_hook_add(uc_engine *uc, uc_hook *hh, int type, void *callback,
        void *user_data, uint64_t begin, uint64_t end, ...)
{
    int ret = UC_ERR_OK;
    int i = 0;

    struct hook *hook = calloc(1, sizeof(struct hook));
    if (hook == NULL) {
        return UC_ERR_NOMEM;
    }

    hook->begin = begin;
    hook->end = end;
    hook->type = type;
    hook->callback = callback;
    hook->user_data = user_data;
    hook->refs = 0;
    *hh = (uc_hook)hook;

    // UC_HOOK_INSN은 추가 인자가 있습니다: 명령어 ID
    if (type & UC_HOOK_INSN) {
        va_list valist;

        va_start(valist, end);
        hook->insn = va_arg(valist, int);
        va_end(valist);

        if (uc->insn_hook_validate) {
            if (! uc->insn_hook_validate(hook->insn)) {
                free(hook);
                return UC_ERR_HOOK;
            }
        }

        if (uc->hook_insert) {
            if (list_insert(&uc->hook[UC_HOOK_INSN_IDX], hook) == NULL) {
                free(hook);
                return UC_ERR_NOMEM;
            }
        } else {
            if (list_append(&uc->hook[UC_HOOK_INSN_IDX], hook) == NULL) {
                free(hook);
                return UC_ERR_NOMEM;
            }
        }

        hook->refs++;
        return UC_ERR_OK;
    }

    while ((type >> i) > 0) {
        if ((type >> i) & 1) {
            if (i < UC_HOOK_MAX) {
                if (uc->hook_insert) {
                    if (list_insert(&uc->hook[i], hook) == NULL) {
                        if (hook->refs == 0) {
                            free(hook);
                        }
                        return UC_ERR_NOMEM;
                    }
                } else {
                    if (list_append(&uc->hook[i], hook) == NULL) {
                        if (hook->refs == 0) {
                            free(hook);
                        }
                        return UC_ERR_NOMEM;
                    }
                }
                hook->refs++;
            }
        }
        i++;
    }

    if (hook->refs == 0) {
        free(hook);
    }

    return ret;
}
```

</details>

사용예시:

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

int syscall_abi[] = {
    UC_X86_REG_RAX, UC_X86_REG_RDI, UC_X86_REG_RSI, UC_X86_REG_RDX,
    UC_X86_REG_R10, UC_X86_REG_R8, UC_X86_REG_R9
};

uint64_t vals[7] = { 200, 10, 11, 12, 13, 14, 15 };

void* ptrs[7];

void uc_perror(const char* func, uc_err err)
{
    fprintf(stderr, "Error in %s(): %s\n", func, uc_strerror(err));
}

#define BASE 0x10000

// mov rax, 100; mov rdi, 1; mov rsi, 2; mov rdx, 3; mov r10, 4; mov r8, 5; mov r9, 6; syscall
#define CODE "\x48\xc7\xc0\x64\x00\x00\x00\x48\xc7\xc7\x01\x00\x00\x00\x48\xc7\xc6\x02\x00\x00\x00\x48\xc7\xc2\x03\x00\x00\x00\x49\xc7\xc2\x04\x00\x00\x00\x49\xc7\xc0\x05\x00\x00\x00\x49\xc7\xc1\x06\x00\x00\x00\x0f\x05"

void hook_syscall(uc_engine* uc, void* user_data)
{
    int i;

    uc_reg_read_batch(uc, syscall_abi, ptrs, 7);

    printf("syscall: {");

    for (i = 0; i < 7; i++) {
        if (i != 0) printf(", ");
        printf("%" PRIu64, vals[i]);
    }

    printf("}\n");
}

void hook_code(uc_engine* uc, uint64_t addr, uint32_t size, void* user_data)
{
    printf("HOOK_CODE: 0x%" PRIx64 ", 0x%x\n", addr, size);
}

int main()
{
    int i;
    uc_hook sys_hook;
    uc_err err;
    uc_engine* uc;

    for (i = 0; i < 7; i++) {
        ptrs[i] = &vals[i];
    }

    if ((err = uc_open(UC_ARCH_X86, UC_MODE_64, &uc))) {
        uc_perror("uc_open", err);
        return 1;
    }

    printf("reg_write_batch({200, 10, 11, 12, 13, 14, 15})\n");
    if ((err = uc_reg_write_batch(uc, syscall_abi, ptrs, 7))) {
        uc_perror("uc_reg_write_batch", err);
        return 1;
    }

    memset(vals, 0, sizeof(vals));
    if ((err = uc_reg_read_batch(uc, syscall_abi, ptrs, 7))) {
        uc_perror("uc_reg_read_batch", err);
        return 1;
    }

    printf("reg_read_batch = {");

    for (i = 0; i < 7; i++) {
        if (i != 0) printf(", ");
        printf("%" PRIu64, vals[i]);
    }

    printf("}\n");

    // syscall
    printf("\n");
    printf("running syscall shellcode\n");

    if ((err = uc_hook_add(uc, &sys_hook, UC_HOOK_CODE, hook_syscall, NULL, 1, 0))) {
        uc_perror("uc_hook_add", err);
        return 1;
    }

    if ((err = uc_mem_map(uc, BASE, 0x1000, UC_PROT_ALL))) {
        uc_perror("uc_mem_map", err);
        return 1;
    }

    if ((err = uc_mem_write(uc, BASE, CODE, sizeof(CODE) - 1))) {
        uc_perror("uc_mem_write", err);
        return 1;
    }

    if ((err = uc_emu_start(uc, BASE, BASE + sizeof(CODE) - 1, 0, 0))) {
        uc_perror("uc_emu_start", err);
        return 1;
    }

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_19.png)

모든 명령어에 대해 hook을 적용합니다



### uc_hook_del

```
uc_err uc_hook_del(uc_engine *uc, uc_hook hh);
```

등록된 hook 이벤트를 제거합니다.
```
@uc: uc_open()에서 반환된 핸들
@hh: uc_hook_add()에서 반환된 핸들

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```


<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_hook_del(uc_engine *uc, uc_hook hh)
{
    int i;
    struct hook *hook = (struct hook *)hh;

    for (i = 0; i < UC_HOOK_MAX; i++) {
        if (list_remove(&uc->hook[i], (void *)hook)) {
            if (--hook->refs == 0) {
                free(hook);
                break;
            }
        }
    }
    return UC_ERR_OK;
}
```

</details>

사용예시:

```cpp
if ((err = uc_hook_add(uc, &sys_hook, UC_HOOK_CODE, hook_syscall, NULL, 1, 0))) {
    uc_perror("uc_hook_add", err);
    return 1;
}

if ((err = uc_hook_del(uc, &sys_hook))) {
    uc_perror("uc_hook_del", err);
    return 1;
}
```



### uc_mem_map

```c
uc_err uc_mem_map(uc_engine *uc, uint64_t address, size_t size, uint32_t perms);
```

에뮬레이션을 위해 메모리 블록을 매핑합니다.

```
@uc: uc_open()에서 반환된 핸들
@address: 매핑할 새로운 메모리 영역의 시작 주소. 이 주소는 4KB로 정렬되어야 합니다. 그렇지 않으면 UC_ERR_ARG 오류가 반환됩니다.
@size: 매핑할 새로운 메모리 영역의 크기. 이 크기는 4KB의 배수여야 합니다. 그렇지 않으면 UC_ERR_ARG 오류가 반환됩니다.
@perms: 새로 매핑된 영역의 권한. 인자는 UC_PROT_READ | UC_PROT_WRITE | UC_PROT_EXEC 또는 이들의 조합이어야 합니다. 그렇지 않으면 UC_ERR_ARG 오류가 반환됩니다.

@return 성공 시 UC_ERR_OK 반환, 그렇지 않으면 uc_err 열거형의 다른 오류 타입 반환
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_map(uc_engine *uc, uint64_t address, size_t size, uint32_t perms)
{
    uc_err res;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    res = mem_map_check(uc, address, size, perms);    //메모리 안전성 검사
    if (res)
        return res;

    return mem_map(uc, address, size, perms, uc->memory_map(uc, address, size, perms));
}
```

</details>

상용예시는 [uc_hook_add()](#uc_hook_add)와 같습니다



### uc_mem_map_ptr

```c
uc_err uc_mem_map_ptr(uc_engine *uc, uint64_t address, size_t size, uint32_t perms, void *ptr);
```

기존 호스트 메모리를 시뮬레이션에서 매핑합니다.

```
@uc: `uc_open()`에서 반환된 핸들
@address: 새 메모리 영역으로 매핑할 시작 주소. 이 주소는 4KB로 정렬되어야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@size: 새 메모리 영역으로 매핑할 크기. 이 크기는 4KB의 배수여야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@perms: 새로 매핑된 영역의 권한. 이 매개변수는 `UC_PROT_READ | UC_PROT_WRITE | UC_PROT_EXEC` 또는 이들의 조합이어야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@ptr: 새로 매핑된 메모리를 지원하는 호스트 메모리를 가리키는 포인터. 매핑되는 호스트 메모리의 크기는 `size`와 동일하거나 더 커야 하며, 최소한 `PROT_READ | PROT_WRITE`로 매핑되어야 합니다. 그렇지 않으면 매핑이 정의되지 않습니다.

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_map_ptr(uc_engine *uc, uint64_t address, size_t size, uint32_t perms, void *ptr)
{
    uc_err res;

    if (ptr == NULL)
        return UC_ERR_ARG;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    res = mem_map_check(uc, address, size, perms);    //메모리 안전 검사
    if (res)
        return res;

    return mem_map(uc, address, size, UC_PROT_ALL, uc->memory_map_ptr(uc, address, size, perms, ptr));
}
```

</details>

사용예시는 [uc_mem_map()](#uc_mem_map)와 같다



### uc_mem_unmap

```c
uc_err uc_mem_unmap(uc_engine *uc, uint64_t address, size_t size);
```

시뮬레이션 메모리 영역의 매핑을 취소합니다

```
@uc: `uc_open()`에서 반환된 핸들
@address: 새 메모리 영역으로 매핑할 시작 주소. 이 주소는 4KB로 정렬되어야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@size: 새 메모리 영역으로 매핑할 크기. 이 크기는 4KB의 배수여야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_unmap(struct uc_struct *uc, uint64_t address, size_t size)
{
    MemoryRegion *mr;
    uint64_t addr;
    size_t count, len;

    if (size == 0)
        // 취소할 매핑된 영역이 없습니다.
        return UC_ERR_OK;

    // 주소는 `uc->target_page_size`에 맞춰 정렬되어야 합니다.
    if ((address & uc->target_page_align) != 0)
        return UC_ERR_ARG;

    // 크기는 `uc->target_page_size`의 배수여야 합니다.
    if ((size & uc->target_page_align) != 0)
        return UC_ERR_ARG;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    // 사용자가 요청한 전체 블록이 매핑되었는지 확인합니다.
    if (!check_mem_area(uc, address, size))
        return UC_ERR_NOMEM;

    // 이 영역이 인접한 영역을 가로지르는 경우, 영역을 분할해야 할 수도 있습니다.
    addr = address;
    count = 0;
    while(count < size) {
        mr = memory_mapping(uc, addr);
        len = (size_t)MIN(size - count, mr->end - addr);
        if (!split_region(uc, mr, addr, len, true))
            return UC_ERR_NOMEM;

        // 매핑 취소
        mr = memory_mapping(uc, addr);
        if (mr != NULL)
           uc->memory_unmap(uc, mr);
        count += len;
        addr += len;
    }

    return UC_ERR_OK;
}
```

</details>

사용예시:

```cpp
if ((err = uc_mem_map(uc, BASE, 0x1000, UC_PROT_ALL))) {
    uc_perror("uc_mem_map", err);
    return 1;
}

if ((err = uc_mem_unmap(uc, BASE, 0x1000))) {
    uc_perror("uc_mem_unmap", err);
    return 1;
}
```



### uc_mem_protect

```c
uc_err uc_mem_protect(uc_engine *uc, uint64_t address, size_t size, uint32_t perms);
```

시뮬레이션 메모리의 권한 설정

```
@uc: `uc_open()`에서 반환된 핸들
@address: 새 메모리 영역으로 매핑할 시작 주소. 이 주소는 4KB로 정렬되어야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@size: 새 메모리 영역으로 매핑할 크기. 이 크기는 4KB의 배수여야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.
@perms: 매핑된 영역의 새로운 권한. 이 매개변수는 `UC_PROT_READ | UC_PROT_WRITE | UC_PROT_EXEC` 또는 이들의 조합이어야 하며, 그렇지 않으면 `UC_ERR_ARG` 오류가 반환됩니다.

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_mem_protect(struct uc_struct *uc, uint64_t address, size_t size, uint32_t perms)
{
    MemoryRegion *mr;
    uint64_t addr = address;
    size_t count, len;
    bool remove_exec = false;

    if (size == 0)
        // trivial case, no change
        return UC_ERR_OK;

    // address must be aligned to uc->target_page_size
    if ((address & uc->target_page_align) != 0)
        return UC_ERR_ARG;

    // size must be multiple of uc->target_page_size
    if ((size & uc->target_page_align) != 0)
        return UC_ERR_ARG;

    // check for only valid permissions
    if ((perms & ~UC_PROT_ALL) != 0)
        return UC_ERR_ARG;

    if (uc->mem_redirect) {
        address = uc->mem_redirect(address);
    }

    // check that user's entire requested block is mapped
    if (!check_mem_area(uc, address, size))
        return UC_ERR_NOMEM;

    // Now we know entire region is mapped, so change permissions
    // We may need to split regions if this area spans adjacent regions
    addr = address;
    count = 0;
    while(count < size) {
        mr = memory_mapping(uc, addr);
        len = (size_t)MIN(size - count, mr->end - addr);
        if (!split_region(uc, mr, addr, len, false))
            return UC_ERR_NOMEM;

        mr = memory_mapping(uc, addr);
        // will this remove EXEC permission?
        if (((mr->perms & UC_PROT_EXEC) != 0) && ((perms & UC_PROT_EXEC) == 0))
            remove_exec = true;
        mr->perms = perms;
        uc->readonly_mem(mr, (perms & UC_PROT_WRITE) == 0);

        count += len;
        addr += len;
    }

    // if EXEC permission is removed, then quit TB and continue at the same place
    if (remove_exec) {
        uc->quit_request = true;
        uc_emu_stop(uc);
    }

    return UC_ERR_OK;
}
```

</details>

사용예시:

```cpp
if ((err = uc_mem_protect(uc, BASE, 0x1000, UC_PROT_ALL))) {  //읽기, 쓰기, 실행 가능
    uc_perror("uc_mem_protect", err);
    return 1;
}
```



### uc_mem_regions

```c
uc_err uc_mem_regions(uc_engine *uc, uc_mem_region **regions, uint32_t *count);
```

`uc_mem_map()` 및 `uc_mem_map_ptr()`로 매핑된 메모리 정보를 검색합니다.

이 API는 @regions에 메모리를 할당하며, 사용자는 메모리 누수를 방지하기 위해 나중에 이 메모리를 `free()`로 해제해야 합니다.

```
@uc: `uc_open()`에서 반환된 핸들
@regions: `uc_mem_region` 구조체 배열을 가리키는 포인터. Unicorn에 의해 할당되며, 이 메모리는 `uc_free()`를 통해 해제해야 합니다.
@count: `@regions`에 포함된 `uc_mem_region` 구조체의 수를 가리키는 포인터

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

소스코드 분석

<details><summary> Code </summary>

```c
uint32_t uc_mem_regions(uc_engine *uc, uc_mem_region **regions, uint32_t *count)
{
    uint32_t i;
    uc_mem_region *r = NULL;

    *count = uc->mapped_block_count;

    if (*count) {
        r = g_malloc0(*count * sizeof(uc_mem_region));
        if (r == NULL) {
            // 메모리 부족
            return UC_ERR_NOMEM;
        }
    }

    for (i = 0; i < *count; i++) {
        r[i].begin = uc->mapped_blocks[i]->addr;
        r[i].end = uc->mapped_blocks[i]->end - 1;
        r[i].perms = uc->mapped_blocks[i]->perms;
    }

    *regions = r;

    return UC_ERR_OK;
}
```

</details>

사용예시:

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

int main()
{
    uc_err err;
    uc_engine* uc;

    if ((err = uc_open(UC_ARCH_X86, UC_MODE_64, &uc))) {
        uc_perror("uc_open", err);
        return 1;
    }

    if ((err = uc_mem_map(uc, BASE, 0x1000, UC_PROT_ALL))) {
        uc_perror("uc_mem_map", err);
        return 1;
    }

    uc_mem_region *region;
    uint32_t count;

    if ((err = uc_mem_regions(uc, &region, &count))) {
        uc_perror("uc_mem_regions", err);
        return 1;
    }

    cout << "起始地址： 0x" << hex << region->begin << "  结束地址： 0x" << hex << region->end << "  内存权限：  " <<region->perms << "  已申请内存块数： " << count << endl;

    if ((err = uc_free(region))) {    ////메모리 해제에 유의하세요
        uc_perror("uc_free", err);
        return 1;
    }

    return 0;
}
```

출력

![image.png](API_Doc_Pic/1_20.png)



### uc_free

```c
uc_err uc_free(void *mem);
```

[uc_mem_regions()](#uc_mem_regions)에서 할당한 메모리를 해제합니다.

```
@mem: `uc_mem_regions`에서 할당한 메모리(`*regions` 반환)

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_free(void *mem)
{
    g_free(mem);
    return UC_ERR_OK;
}

void g_free(gpointer ptr)
{
   free(ptr);
}
```

</details>

사용예시는 [uc_mem_regions()](#uc_mem_regions)와 같습니다.



### uc_context_alloc

```c
uc_err uc_context_alloc(uc_engine *uc, uc_context **context);
```

CPU 컨텍스트의 빠른 저장/복원을 수행하기 위해 `uc_context_{save,restore}`와 함께 사용할 수 있는 영역을 할당합니다. 여기에는 레지스터와 내부 메타데이터가 포함됩니다. 컨텍스트는 다른 아키텍처 또는 모드를 가진 엔진 인스턴스 간에 공유할 수 없습니다.

```
@uc: `uc_open()`에서 반환된 핸들
@context: `uc_engine*`의 포인터를 가리키는 포인터. 이 함수가 성공적으로 반환되면, 새로운 컨텍스트를 가리키는 포인터로 업데이트됩니다. 이후 할당된 이 메모리는 `uc_context_free()`를 사용하여 해제해야 합니다.

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_context_alloc(uc_engine *uc, uc_context **context)
{
    struct uc_context **_context = context;
    size_t size = uc->cpu_context_size;

    *_context = g_malloc(size);
    if (*_context) {
        (*_context)->jmp_env_size = sizeof(*uc->cpu->jmp_env);
        (*_context)->context_size = size - sizeof(uc_context) - (*_context)->jmp_env_size;
        return UC_ERR_OK;
    } else {
        return UC_ERR_NOMEM;
    }
}
```

</details>

사용예시

```cpp
#include <iostream>
#include <string>
#include "unicorn/unicorn.h"
using namespace std;

#define ADDRESS 0x1000
#define X86_CODE32_INC "\x40"   // INC eax

int main()
{
    uc_engine* uc;
    uc_context* context;
    uc_err err;

    int r_eax = 0x1;    // EAX 레지스터

    printf("===================================\n");
    printf("Save/restore CPU context in opaque blob\n");

    err = uc_open(UC_ARCH_X86, UC_MODE_32, &uc);
    if (err) {
        printf("Failed on uc_open() with error returned: %u\n", err);
        return 0;
    }

    uc_mem_map(uc, ADDRESS, 8 * 1024, UC_PROT_ALL);

    if (uc_mem_write(uc, ADDRESS, X86_CODE32_INC, sizeof(X86_CODE32_INC) - 1)) {
        printf("Failed to write emulation code to memory, quit!\n");
        return 0;
    }

    // 레지스터 초기화
    uc_reg_write(uc, UC_X86_REG_EAX, &r_eax);

    printf(">>> Running emulation for the first time\n");

    err = uc_emu_start(uc, ADDRESS, ADDRESS + sizeof(X86_CODE32_INC) - 1, 0, 0);
    if (err) {
        printf("Failed on uc_emu_start() with error returned %u: %s\n",
            err, uc_strerror(err));
    }

    printf(">>> Emulation done. Below is the CPU context\n");

    uc_reg_read(uc, UC_X86_REG_EAX, &r_eax);
    printf(">>> EAX = 0x%x\n", r_eax);

    // CPU 컨텍스트를 할당하고 저장합니다.
    printf(">>> Saving CPU context\n");

    err = uc_context_alloc(uc, &context);
    if (err) {
        printf("Failed on uc_context_alloc() with error returned: %u\n", err);
        return 0;
    }

    err = uc_context_save(uc, context);
    if (err) {
        printf("Failed on uc_context_save() with error returned: %u\n", err);
        return 0;
    }

    printf(">>> Running emulation for the second time\n");

    err = uc_emu_start(uc, ADDRESS, ADDRESS + sizeof(X86_CODE32_INC) - 1, 0, 0);
    if (err) {
        printf("Failed on uc_emu_start() with error returned %u: %s\n",
            err, uc_strerror(err));
    }

    printf(">>> Emulation done. Below is the CPU context\n");

    uc_reg_read(uc, UC_X86_REG_EAX, &r_eax);
    printf(">>> EAX = 0x%x\n", r_eax);

    // CPU 컨텍스트를 복원합니다.
    err = uc_context_restore(uc, context);
    if (err) {
        printf("Failed on uc_context_restore() with error returned: %u\n", err);
        return 0;
    }

    printf(">>> CPU context restored. Below is the CPU context\n");

    uc_reg_read(uc, UC_X86_REG_EAX, &r_eax);
    printf(">>> EAX = 0x%x\n", r_eax);

    // CPU 컨텍스트를 해제합니다.
    err = uc_context_free(context);
    if (err) {
        printf("Failed on uc_free() with error returned: %u\n", err);
        return;
    }

    uc_close(uc);
}
```

출력

![image.png](API_Doc_Pic/1_21.png)



### uc_context_save

```c
uc_err uc_context_save(uc_engine *uc, uc_context *context);
```

현재 CPU 컨텍스트를 저장합니다.

```
@uc: `uc_open()`에서 반환된 핸들
@context: `uc_context_alloc()`에서 반환된 핸들

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_context_save(uc_engine *uc, uc_context *context)
{
    struct uc_context *_context = context;
    memcpy(_context->data, uc->cpu->env_ptr, _context->size);
    return UC_ERR_OK;
}
```

</details>

사용예시는 [uc_context_alloc()](#uc_context_alloc)와 동일합니다.



### uc_context_restore

```c
uc_err uc_context_restore(uc_engine *uc, uc_context *context);
```

저장된 CPU 컨텍스트를 복원합니다.

```
@uc: `uc_open()`에서 반환된 핸들
@context: `uc_context_alloc()`에서 반환되고 `uc_context_save`를 사용하여 저장된 핸들

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

<details><summary> 소스코드 구현 </summary>

```c
uc_err uc_context_restore(uc_engine *uc, uc_context *context)
{
    struct uc_context *_context = context;
    memcpy(uc->cpu->env_ptr, _context->data, _context->size);
    return UC_ERR_OK;
}
```

</details>

사용 예시는 [uc_context_alloc()](#uc_context_alloc)와 동일합니다



### uc_context_size

```c
size_t uc_context_size(uc_engine *uc);
```

CPU 컨텍스트를 저장하는 데 필요한 크기를 반환합니다. 이를 사용하여 CPU 컨텍스트를 포함할 버퍼를 할당하고, 직접 `uc_context_save`를 호출할 수 있습니다.

```
@uc: `uc_open()`에서 반환된 핸들

@return CPU 컨텍스트를 저장하는 데 필요한 크기. 반환 유형은 `size_t`입니다.
```


<details><summary> 소스코드 구현 </summary>

```c
size_t uc_context_size(uc_engine *uc)
{
    return sizeof(uc_context) + uc->cpu_context_size + sizeof(*uc->cpu->jmp_env);
}
```

</details>

사용 예시는 [uc_context_alloc()](#uc_context_alloc)와 동일합니다



### uc_context_free

```c
uc_err uc_context_free(uc_context *context);
```

[uc_context_alloc()](#uc_context_alloc)에서 할당한 메모리를 해제합니다.

```
@context: `uc_context_alloc`에서 생성된 `uc_context`

@return 성공 시 `UC_ERR_OK`를 반환하고, 그렇지 않으면 `uc_err` 열거형의 다른 오류 유형을 반환합니다.
```

사용예시는 [uc_context_alloc()](#uc_context_alloc)와 동일합니다.