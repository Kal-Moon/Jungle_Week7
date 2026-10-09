---
date: 2026-10-09
tags: [pintos, thread, os, quiz, 복습]
topic: 스레드 개념 퀴즈
progress: 3 / 10
---

# 🧵 스레드 개념 퀴즈 (2026-10-09)

## 📊 결과 요약

| 문제 | 주제 | 결과 |
|---|---|---|
| [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것\|Q1]] | 공유 자원 | 🔺 부분 정답 (6개 중 4개) |
| [[#Q2. 컨텍스트 스위치\|Q2]] | 컨텍스트 스위치 | 🔺 절반 정답 |
| [[#Q3. 스레드 상태 전이\|Q3]] | 상태 전이 | 🔺 부분 정답 (4개 중 2개, 상태 설명 없음) |

### ❌ 틀린 것 / 모른 것 (복습 우선순위)
- [ ] **PC는 스레드마다 따로** 가진다 (공유한다고 답함) → [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것|Q1]]
- [ ] **힙은 공유**한다 (따로 갖는다고 답함) → [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것|Q1]]
- [ ] 스레드 전환이 프로세스 전환보다 싼 이유 = **주소 공간 교체 없음 (TLB flush, 캐시 손실 없음)** (모름) → [[#Q2. 컨텍스트 스위치|Q2]]
- [ ] `thread_yield()`는 **RUNNING → READY** (READY → RUNNING이라고 답함) → [[#Q3. 스레드 상태 전이|Q3]]
- [ ] time slice 종료는 **RUNNING → READY** (DYING이라고 답함) → [[#Q3. 스레드 상태 전이|Q3]]
- [ ] 네 가지 상태의 뜻 설명 (답하지 않음) → [[#Q3. 스레드 상태 전이|Q3]]

### ✅ 맞은 것
- 코드, 데이터는 공유 / 스택, 레지스터는 따로
- 컨텍스트 스위치 때 레지스터를 저장하고 복원한다
- `sema_down()`에서 기다리면 RUNNING → BLOCKED
- `sema_up()`으로 깨어나면 BLOCKED → READY

---

## Q1. 스레드가 공유하는 것 vs 따로 갖는 것

> [!question] 문제
> 같은 프로세스 안의 스레드들은 무엇을 공유하고, 무엇을 각자 따로 가지나요? (코드, 데이터, 힙, 스택, 레지스터, PC)

> [!quote] 내 답
> 공유: 코드, 데이터, PC / 따로: 힙, 스택, 레지스터

| 항목 | 내 답 | 정답 | |
|---|---|---|---|
| 코드 | 공유 | 공유 | ✅ |
| 데이터(전역 변수) | 공유 | 공유 | ✅ |
| 힙 | 따로 | **공유** | ❌ |
| PC | 공유 | **따로** | ❌ |
| 스택 | 따로 | 따로 | ✅ |
| 레지스터 | 따로 | 따로 | ✅ |

> [!success] 정답 핵심
> 스레드마다 따로 갖는 건 **"실행 흐름"에 필요한 것**뿐이다. 바로 **스택 + 레지스터(PC, SP 포함)**.
> 나머지(코드, 데이터, 힙, 열린 파일)는 전부 공유한다.

> [!tip] 기억법
> 같은 책(코드)을 여러 명이 읽어도 **책갈피(PC)는 사람마다** 있다.

### 추가 질문 ① PC도 레지스터인데 왜 따로 이야기하나?
- PC는 레지스터가 맞다. x86-64에서는 `rip`라고 부른다. "레지스터는 따로"면 PC도 따로다.
- 교과서가 따로 쓰는 이유:
  1. **역할이 특별하다.** 범용 레지스터는 계산값을, PC는 "다음에 실행할 명령어 주소"를 담는다. PC가 곧 실행 흐름이다.
  2. **다루는 방법이 다르다.** `mov`로 직접 못 쓰고 `jmp`, `call`, `ret`, 인터럽트로만 바뀐다.
- Pintos `include/threads/interrupt.h`의 `struct intr_frame`:
  ```c
  struct gp_registers R;   // 범용 레지스터
  uintptr_t rip;           // PC
  uintptr_t rsp;           // 스택 포인터
  ```

### 추가 질문 ② 힙은 포인터로 쓰니까 공유되는 건가?
> [!warning] 원인과 결과가 반대
> 공유의 원인은 포인터가 아니라 **같은 주소 공간(같은 페이지 테이블)**이다. 포인터는 접근 수단일 뿐이다.

- 다른 **프로세스**에게 주소 `0x5000`을 넘겨줘도 그 프로세스의 `0x5000`은 자기 메모리다. 페이지 테이블이 다르기 때문이다.
- 🤯 같은 주소 공간이므로 **다른 스레드의 스택도 포인터만 있으면 접근 가능**하다. 스택이 "따로"라는 건 **관례상 따로**이지 **보호받는 따로**가 아니다.

| | 스레드끼리 | 프로세스끼리 |
|---|---|---|
| 주소 공간(페이지 테이블) | **같음** | 다름 |
| 힙/데이터 | 공유 | 분리 (보호됨) |
| 스택 | 영역은 따로, **보호는 안 됨** | 분리 (보호됨) |
| 레지스터(PC, SP 포함) | 진짜로 따로 | 따로 |

---

## Q2. 컨텍스트 스위치

> [!question] 문제
> 컨텍스트 스위치 때 무엇을 저장/복원하나요? 스레드 전환이 프로세스 전환보다 싼 이유는?

> [!quote] 내 답
> 레지스터를 저장 및 복원한다. 두 번째는 모르겠다.

> [!success] 정답
> - **저장/복원:** 레지스터 (범용 레지스터, **PC(`rip`)**, **SP(`rsp`)**, 플래그). SP가 바뀌면 스택이, PC가 바뀌면 실행 위치가 바뀐다.
> - **싼 이유:** 프로세스 전환은 **주소 공간(페이지 테이블)까지 바꿔야** 한다 (x86에서는 `CR3`). 스레드끼리는 주소 공간이 같아서 안 바꾼다.

진짜 비싼 건 페이지 테이블 교체의 **부작용**이다.
1. **TLB flush:** 가상→물리 주소 변환 캐시를 비워야 해서 한동안 느려진다.
2. **Cold cache:** 새 프로세스는 다른 메모리를 써서 CPU 캐시가 쓸모없어진다.

> [!example] Pintos 코드
> - `threads/thread.c`의 `schedule()`: `#ifdef USERPROG` 안에서 `process_activate(next)` 호출 → `userprog/process.c`에서 `pml4_activate(next->pml4)`로 페이지 테이블 교체.
> - Project 1에는 유저 프로세스가 없으니 이 부분이 컴파일되지 않는다. 레지스터 교체만 한다: `thread_launch()`, `do_iret()`.

> [!tip] 한 줄 요약
> 컨텍스트 스위치 = 레지스터 저장/복원 (+ 프로세스면 **주소 공간 교체 → TLB flush, 캐시 손실**)

---

## Q3. 스레드 상태 전이

> [!question] 문제
> `THREAD_RUNNING`, `THREAD_READY`, `THREAD_BLOCKED`, `THREAD_DYING`의 뜻을 설명하고, 아래 상황의 상태 변화를 말해 보세요.
> (a) `thread_yield()` 호출 (b) `sema_down()`에서 기다림 (c) `sema_up()`으로 깨어남 (d) time slice(4 tick) 종료

> [!quote] 내 답
> 상태 설명 없음 / a: ready → running / b: running → blocked / c: blocked → ready / d: dying

### 상태의 뜻
| 상태 | 뜻 | 들어가는 곳 |
|---|---|---|
| `RUNNING` | 지금 CPU에서 실행 중 (CPU당 딱 하나) | — |
| `READY` | **CPU만 주면 바로 실행 가능**, 차례를 기다림 | `ready_list` |
| `BLOCKED` | **어떤 사건을 기다림** (세마포어, 락, sleep 등). CPU를 줘도 못 돈다 | 세마포어의 `waiters` 등 |
| `DYING` | 종료됨, 곧 메모리 해제 | — |

### 상태 전이
| | 상황 | 내 답 | 정답 | |
|---|---|---|---|---|
| a | `thread_yield()` | READY → RUNNING | **RUNNING → READY** | ❌ |
| b | `sema_down()`에서 대기 | RUNNING → BLOCKED | RUNNING → BLOCKED | ✅ |
| c | `sema_up()`으로 깨어남 | BLOCKED → READY | BLOCKED → READY | ✅ |
| d | time slice 종료 | DYING | **RUNNING → READY** | ❌ |

> [!warning] 틀린 이유
> - **(a)** `yield` = "양보하다". **실행 중인 스레드가** 스스로 CPU를 내려놓고 READY로 돌아가는 것이다. 실행 중이어야 함수를 호출할 수 있으니 출발점은 항상 RUNNING이다.
> - **(d)** time slice가 끝난 건 **"네 차례 끝, 줄 뒤로 가"**이지 "죽어"가 아니다. 스레드는 아직 할 일이 남았으니 READY로 간다. DYING은 `thread_exit()`을 불렀을 때뿐이다.
> - **(c) 주의:** 깨어나면 바로 RUNNING이 아니라 **READY**로 간다. (맞힌 부분!)

> [!example] Pintos 코드 (`threads/thread.c`)
> - `thread_tick()`: `if (++thread_ticks >= TIME_SLICE) intr_yield_on_return();` → 인터럽트가 끝날 때 `thread_yield()` 호출
> - `thread_yield()`: `do_schedule(THREAD_READY)` → **RUNNING → READY**
> - `thread_exit()`: `do_schedule(THREAD_DYING)` → **RUNNING → DYING**
> - `thread_block()`: RUNNING → BLOCKED / `thread_unblock()`: BLOCKED → READY

```mermaid
stateDiagram-v2
    [*] --> READY: thread_create
    READY --> RUNNING: 스케줄러가 선택
    RUNNING --> READY: thread_yield / time slice 종료
    RUNNING --> BLOCKED: thread_block (sema_down, lock 대기)
    BLOCKED --> READY: thread_unblock (sema_up)
    RUNNING --> DYING: thread_exit
    DYING --> [*]
```

> [!tip] 기억법
> **BLOCKED에서 바로 RUNNING으로 가는 화살표는 없다.** 깨어나도 READY 줄에 먼저 서야 한다.

---

## 📝 아직 안 푼 문제
- [ ] Q4. Race condition, `count++`가 왜 위험한가 (어셈블리 수준)
- [ ] Q5. Semaphore vs Lock vs Condition Variable
- [ ] Q6. `cond_wait()`을 `if`가 아니라 `while`로 감싸는 이유
- [ ] Q7. Alarm Clock: busy waiting 문제와 sleep list
- [ ] Q8. 인터럽트 핸들러에서 잠들면 안 되는 이유
- [ ] Q9. Priority inversion과 priority donation
- [ ] Q10. Nested donation에 필요한 `struct thread` 필드
