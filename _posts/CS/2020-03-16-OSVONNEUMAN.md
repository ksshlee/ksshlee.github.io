---
title: \[OS\] 폰 노이만 아키텍처(Von Neumann Architecture)
categories : [OS, Computer Science]
tags: [Von Neumann, Architecture]
toc: true
toc_sticky: true
toc_label: "목차"
---


폰 노이만(Von Neumann Architecture)
--

- 현재 우리가 사용하는 컴퓨터는 기본적으로 폰 노이만 구조다.
- 하지만 기본적으로 Alan Turing이 먼저 아이디어를 냈었다.


> Before 폰 노이만

- Program = Hw
- Data = Memory
- 당시에는 sw라는 개념이 없었다.
  - Debugging이 벌레 빼는 작업이였다.
- 한번에 한가지 작업만 가능


> After 폰 노이만

- **⭐️Program, Data in memory⭐️**
- 즉 Program과 Data가 메모리에 같이 저장되어 있다.
- 다른 작업을 할 경우 하드웨어 재배치가 필요없다.
- 즉 SW만 교체하면 되기 때문에 범용성이 향상됬다.
- 한번에 여러가지 작업이 가능하다.

### Architecture

![pon](/assets/img/cs/os/2020-03-16/pon.png)

- Bus는 간단하게 통로라고 생각하면 된다.
  - CPU와 Memory를 이어준다.
  - 16bit, 32bit 몇 비트에 따라 얼마나 많은 양의 데이터가 얼마나 빨리 전송되는지 결정된다.
- Accumulator는 누산기로도 불린다.
  - 연산 결과를 임시로 저장하는 레지스터이다.
  - Cpu안에는 Register를 포함하고 있다.


### Cpu Regitster

- 레지스터는 Cpu안에 있다.
  - Why?
    - Cpu는 메모리에 **직접 접근 할 수 없다.**

![pon](/assets/img/cs/os/2020-03-16/pon3.png)

- MBR(Memory Buffer Register) : 메모리의 버퍼 즉 값을 넣는 레지스터
- MAR(Memory Address Register) : 메모리의 **주소**를 넣는 레지스터
- PC(Program Counter) : 다음에 실행될 명령어의 메모리의 주소를 저장
- IR(Instruction Register) : 현재 실행되는 명령어를 저장

<br>

**CPU는 레지스터의 도움으로 메모리에 접근할 수 있다.**

<br><br>


폰 노이만 구조의 단점
--

### 병목 현상(Bottle neck)

> 병목 현상이란?

- 병의 목처럼 입구보다 빠져나오는 데이터가 더 많을 때 성능이 저하됨

> 해결 방법

- Cache 사용

특징

1. 임시로 저장한다.
2. 무조건 빨라야한다.
3. 가장 최근 데이터를 갖고 있어야한다.

### 프로그램, 데이터 같은 메모리 사용

- 프로그램과 데이터가 같은 메모리를 사용함으로써 데이터가 꼬일 가능성이 존재한다.
- 같은 **Bus**를 사용함으로써 병목현상이 일어난다.

> 해결 방법

- Harvard Architecture 사용


<br><br>


Harvard Architecture
--

![pon](/assets/img/cs/os/2020-03-16/pon4.png)

- 폰 노이만 구조와 달리 Data, Program 각각 다른 메모리 영역을 사용한다.
- 각각의 서로 다른 Bus를 사용한다.


>단점

- 금액적인 비용이 증가한다.




<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
