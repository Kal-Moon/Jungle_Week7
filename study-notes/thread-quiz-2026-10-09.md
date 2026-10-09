---
date: 2026-10-09
tags: [pintos, thread, os, quiz, 복습]
topic: 스레드 개념 퀴즈
progress: 5 / 10 (보충 문제 포함)
---

# 🧵 스레드 개념 퀴즈 (2026-10-09)

## 📊 결과 요약

| 문제 | 주제 | 결과 |
|---|---|---|
| [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것\|Q1]] | 공유 자원 | 🔺 부분 정답 (6개 중 4개) |
| [[#Q2. 컨텍스트 스위치\|Q2]] | 컨텍스트 스위치 | 🔺 절반 정답 |
| [[#Q3. 스레드 상태 전이\|Q3]] | 상태 전이 | 🔺 부분 정답 (4개 중 2개, 상태 설명 없음) |
| [[#Q4. Race condition (`count++`)\|Q4]] | Race condition | ❌ 오답 |
| [[#Q4-1. 한 번씩만 더하면?\|Q4-1]] | Race condition | 🔺 절반 정답 |
| [[#Q4-2. 이해 확인\|Q4-2]] | Race condition | ✅ 정답 (설명 후 이해 완료) |
| [[#Q5. Lock으로 race condition 막기\|Q5]] | Lock | ❌ 오답 |
| [[#Q5-1. 이해 확인\|Q5-1]] | ready_list | ✅ 정답 |
| [[#Q5-2. Lock은 왜 `holder`를 기록하나?\|Q5-2]] | Lock vs 세마포어 | 🔺 부분 정답 |
| [[#Q5-3. 세마포어로 신호 보내기\|Q5-3]] | 세마포어 신호 | ❌ 오답 |
| [[#Q5-4. 신호를 먼저 보내면?\|Q5-4]] | 세마포어 신호 | 🔺 절반 정답 |
| [[#Q5-6. 상태 판단 스피드 퀴즈\|Q5-6]] | 상태 판단 | ✅ 4개 모두 정답 |
| [[#Q7. Alarm Clock (busy waiting)\|Q7]] | Alarm Clock | 🔺 부분 정답 |
| [[#Q7-2. Alarm Clock 고치기: 누가 깨우고, 시각은 어디에 저장?\|Q7-2]] | Alarm Clock | 🔺 절반 정답 |
| [[#Q7-3. 새 `timer_sleep` 흐름 빈칸 채우기\|Q7-3]] | Alarm Clock | 🔺 3/6 (상태 전이는 정답) |

### ❌ 틀린 것 / 모른 것 (복습 우선순위)
- [ ] **PC는 스레드마다 따로** 가진다 (공유한다고 답함) → [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것|Q1]]
- [ ] **힙은 공유**한다 (따로 갖는다고 답함) → [[#Q1. 스레드가 공유하는 것 vs 따로 갖는 것|Q1]]
- [ ] 스레드 전환이 프로세스 전환보다 싼 이유 = **주소 공간 교체 없음 (TLB flush, 캐시 손실 없음)** (모름) → [[#Q2. 컨텍스트 스위치|Q2]]
- [ ] `thread_yield()`는 **RUNNING → READY** (READY → RUNNING이라고 답함) → [[#Q3. 스레드 상태 전이|Q3]]
- [ ] time slice 종료는 **RUNNING → READY** (DYING이라고 답함) → [[#Q3. 스레드 상태 전이|Q3]]
- [ ] 네 가지 상태의 뜻 설명 (답하지 않음) → [[#Q3. 스레드 상태 전이|Q3]]
- [ ] **READY → BLOCKED 전이는 없다.** 코드를 실행할 수 있는 건 RUNNING뿐이다 (yield가 READY → BLOCKED라고 생각함) → [[#추가 질문 ① yield는 왜 READY → BLOCKED가 아니라 RUNNING → READY인가?|Q3 추가 ①]]
- [ ] **타이머 인터럽트는 "작업 끝" 알림이 아니라 강제로 CPU를 뺏는 주기적 신호**다 (끝났다는 신호로 생각함) → [[#추가 질문 ② 타이머 인터럽트는 "작업이 끝났다"는 신호인가?|Q3 추가 ②]]
- [ ] **전역 변수 `count`는 공유**된다. 스레드마다 1000씩 따로 세는 게 아니다 → [[#Q4. Race condition (`count++`)|Q4]]
- [ ] **`count++`는 CPU에서 읽기 → 더하기 → 쓰기 세 단계**이고, 그 사이에 인터럽트가 끼면 값이 사라진다 (lost update) → [[#Q4. Race condition (`count++`)|Q4]]
- [ ] 한 번씩 더하면 결과는 **1 또는 2**. 0은 불가능하다 (0과 1이라고 답함) → [[#Q4-1. 한 번씩만 더하면?|Q4-1]]
- [ ] lock이 잡혀 있을 때 `lock_acquire()`한 스레드는 **RUNNING → BLOCKED**, `lock_release()`로 깨어나면 **BLOCKED → READY** → [[#Q5. Lock으로 race condition 막기|Q5]]
- [ ] 인터럽트로 멈춘 스레드는 "멈춤"이 아니라 **READY** → [[#Q5. Lock으로 race condition 막기|Q5]]
- [ ] **Lock은 인터럽트를 막지 않는다.** 다른 스레드가 중간 값을 베끼는 걸 막는다 → 결과 12 → [[#Q5. Lock으로 race condition 막기|Q5]]
- [ ] `holder`가 없으면 남이 열쇠를 반납해서 **두 스레드가 동시에 임계 구역에 들어가고 race condition이 다시 생긴다** → [[#Q5-2. Lock은 왜 `holder`를 기록하나?|Q5-2]]
- [ ] value 0인 세마포어에 `sema_down` → **RUNNING → BLOCKED** (타이머 인터럽트 → READY라고 답함) → [[#Q5-3. 세마포어로 신호 보내기|Q5-3]]
- [ ] `sema_up`으로 깨어나면 **BLOCKED → READY** (READY → RUNNING이라고 답함) → [[#Q5-3. 세마포어로 신호 보내기|Q5-3]]
- [ ] 열쇠가 있으면 `sema_down`은 **기다리지 않고 통과** → RUNNING 그대로. DYING은 `thread_exit()`일 때만 (DYING이라고 답함) → [[#Q5-4. 신호를 먼저 보내면?|Q5-4]]
- [ ] 스케줄러가 READY 스레드를 고르면 **RUNNING**이 된다 (READY가 된다고 답함) → [[#Q7. Alarm Clock (busy waiting)|Q7]]
- [ ] busy waiting의 근본 원인: 시간을 **기다리는** 스레드가 BLOCKED가 아니라 **READY**에 있다 → [[#Q7. Alarm Clock (busy waiting)|Q7]]
- [ ] 잠든 스레드가 깨어날 시각은 `elem`이 아니라 **`wakeup_tick` 필드**에 저장, 잠든 스레드는 **`sleep_list`**에 → [[#Q7-2. Alarm Clock 고치기: 누가 깨우고, 시각은 어디에 저장?|Q7-2]]
- [ ] 직접 쓰는 함수는 **`thread_block()` / `thread_unblock()`** (`sema_down`/`sema_up` 아님), 깨우는 조건은 **`wakeup_tick <= ticks`** → [[#Q7-3. 새 `timer_sleep` 흐름 빈칸 채우기|Q7-3]]
- [ ] 신호 보내기에 lock을 못 쓰는 이유: **down/up 스레드가 다름 + lock은 열린 채로 시작** → [[#Q5-3. 세마포어로 신호 보내기|Q5-3]]

### ✅ 맞은 것
- 코드, 데이터는 공유 / 스택, 레지스터는 따로
- 컨텍스트 스위치 때 레지스터를 저장하고 복원한다
- `sema_down()`에서 기다리면 RUNNING → BLOCKED
- `sema_up()`으로 깨어나면 BLOCKED → READY
- 중간에 끼어들면 더하기 하나가 사라진다 (10 → 11, 원래 12) — Q4-2
- 스케줄러는 `ready_list`에서만 고른다. BLOCKED 스레드는 선택되지 않는다 — Q5-1
- `holder`가 없으면 누구든 lock을 release할 수 있다 — Q5-2
- B가 먼저 `sema_up` 하면 value는 1 — Q5-4
- 상태 판단 스피드 퀴즈 4문제 전부 — Q5-6
- `thread_yield()`는 RUNNING → READY — Q7
- 타이머 인터럽트가 `sleep_list`의 스레드를 깨운다 — Q7-2
- 잠들기 BLOCKED, 깨어나기 BLOCKED → READY 상태 전이 — Q7-3

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

### 추가 질문 ① yield는 왜 READY → BLOCKED가 아니라 RUNNING → READY인가?
> [!warning] 내가 헷갈린 부분
> "양보하니까 READY에서 BLOCKED로 가야 하지 않나?"라고 생각했다.

- **코드를 실행할 수 있는 건 RUNNING 스레드 하나뿐이다.** READY 스레드는 줄에 서 있을 뿐 아무 코드도 실행하지 못한다. 그래서 `thread_yield()`든 `sema_down()`이든 **함수를 호출한 스레드는 반드시 RUNNING**이다.
- 그래서 **READY → BLOCKED 화살표는 존재하지 않는다.** BLOCKED가 되려면 `sema_down()` 같은 함수를 직접 불러야 하는데, READY 스레드는 함수를 부를 수 없다.
- yield한 스레드는 **기다리는 사건이 없다.** 할 일이 남았고 CPU만 다시 받으면 바로 이어서 할 수 있다. 이게 READY의 정의다.

| | yield (→ READY) | sema_down 대기 (→ BLOCKED) |
|---|---|---|
| 왜 CPU를 놓나 | 남에게 차례를 양보 | 필요한 자원이 없어서 |
| 다시 CPU를 받으면 | 바로 실행 가능 | 아직 못 돈다 (자원이 없으니까) |
| 누가 깨워 주나 | 필요 없음, 스케줄러가 고르면 실행 | 다른 스레드가 `sema_up()` 해 줘야 함 |

> [!tip] 비유: 화장실 줄
> - RUNNING = 화장실 안에 있는 사람
> - READY = 줄 서 있는 사람
> - yield = 볼일이 남았지만 뒷사람에게 양보하고 **다시 줄 맨 뒤로** 감 → READY
> - BLOCKED = 휴지가 없어서 **휴지가 올 때까지** 대기실에 감. 차례가 와도 들어갈 수 없다
> - 줄에 서 있는 사람(READY)은 대기실(BLOCKED)로 갈 이유도 방법도 없다

### 추가 질문 ② 타이머 인터럽트는 "작업이 끝났다"는 신호인가?
> [!warning] 오해
> "READY 쪽에서 RUNNING의 작업이 끝났는지 모르니, 끝났다고 알려 주는 신호가 타이머 인터럽트다"라고 생각했다.

> [!success] 정답
> 타이머 인터럽트는 **작업이 끝났든 말든 일정 시간마다 하드웨어가 무조건 보내는 신호**다. 목적은 **아직 안 끝난 스레드에게서도 CPU를 강제로 빼앗는 것(선점, preemption)**이다.

- **작업이 끝난 경우:** 스레드가 스스로 `thread_exit()`을 불러 DYING이 되고, 그 안에서 다음 스레드를 스케줄한다. 알림이 필요 없다.
- **작업이 안 끝난 경우:** 스레드가 무한 루프를 돌면 영원히 CPU를 안 내놓는다. 그래서 하드웨어 시계가 끼어들어 강제로 뺏는다.
- **READY 스레드는 아무것도 확인하지 못한다.** 실행 중이 아니니까. 확인과 결정은 타이머 인터럽트 핸들러(커널 코드)가 **현재 RUNNING 스레드의 CPU를 빌려서** 한다.

> [!example] Pintos 코드
> - `include/devices/timer.h`: `#define TIMER_FREQ 100` → 1초에 100번 = **10ms마다** 인터럽트
> - `devices/timer.c`의 `timer_interrupt()`: `ticks++; thread_tick();`
> - `threads/thread.c`의 `thread_tick()`: 4 tick(=40ms) 지나면 `intr_yield_on_return()` → 인터럽트가 끝날 때 `thread_yield()` → RUNNING → READY

> [!tip] 비유: PC방 타이머
> 타이머 인터럽트는 "게임 끝났어요?"라고 묻는 게 아니라, **게임 중이든 아니든 40분마다 자리를 바꾸게 하는 알람**이다.

---

## Q4. Race condition (`count++`)

> [!question] 문제
> 전역 변수 `count = 0`. 두 스레드가 동시에 `for (i = 0; i < 1000; i++) count++;`를 실행한다. 끝난 뒤 `count`는 항상 2000인가?

> [!quote] 내 답
> 합이 2000이면 모르겠는데, 아니라면 아니다. 1000, 1000이니까. 힌트의 "세 단계"는 READY - RUNNING - BLOCKED를 뜻하는 것 같다.

> [!failure] 결과: ❌ 오답
> - "1000, 1000"은 스레드마다 `count`를 따로 갖는 것처럼 생각한 답이다. `count`는 **전역 변수 → 데이터 영역 → 공유**된다 (Q1). 두 스레드가 **같은 변수 하나**를 올린다.
> - 힌트의 세 단계는 스레드 상태가 아니라 **`count++`를 CPU가 실행하는 세 단계**였다.

> [!success] 정답
> **항상 2000은 아니다. 2000 이하의 값이 나올 수 있다.** `count++`는 CPU에서 세 개의 명령어로 나뉘고, 그 사이에 타이머 인터럽트가 끼어들 수 있기 때문이다.

```asm
mov  eax, [count]   ; ① 읽기: 메모리 → 레지스터
add  eax, 1         ; ② 더하기: 레지스터 안에서
mov  [count], eax   ; ③ 쓰기: 레지스터 → 메모리
```

| 순서 | 스레드 A | 스레드 B | 메모리 `count` |
|---|---|---|---|
| 1 | ① 읽기: eax = 5 | | 5 |
| 2 | ⏰ 타이머 인터럽트! A의 레지스터(eax=5) 저장 | | 5 |
| 3 | | ① 읽기: eax = 5 | 5 |
| 4 | | ② eax = 6 | 5 |
| 5 | | ③ 쓰기 | **6** |
| 6 | 레지스터 복원 (eax = 5 그대로!) | | 6 |
| 7 | ② eax = 6 | | 6 |
| 8 | ③ 쓰기 | | **6** ← 7이어야 함 |

→ 두 번 더했는데 1만 올랐다. B의 결과를 A가 덮어썼다 (**lost update**).

> [!tip] 앞의 문제들이 전부 연결된다
> - **Q1:** `count`(데이터)는 공유, `eax`(레지스터)는 스레드마다 따로 → A는 옛날 값 5를 자기 레지스터에 들고 있다
> - **Q2:** 컨텍스트 스위치 때 레지스터를 저장/복원 → A의 eax=5가 그대로 돌아온다
> - **Q3:** 타이머 인터럽트는 작업 중간에도 언제든 끼어든다 → ①과 ③ 사이에 끼어들 수 있다

> [!note] 용어
> - **Race condition:** 실행 순서(타이밍)에 따라 결과가 달라지는 상황
> - **Critical section(임계 구역):** 공유 자원에 접근해서 한 번에 한 스레드만 실행해야 하는 코드 구간 (여기서는 `count++`)
> - **해결:** 세 단계를 쪼갤 수 없게(atomic) 만든다 → lock, semaphore, 인터럽트 끄기 (→ Q5)

### Q4-1. 한 번씩만 더하면?

> [!question] 문제
> `count = 0`에서 두 스레드가 `count++`를 **한 번씩만** 실행한다. 가능한 결과를 모두 말하라.

> [!quote] 내 답
> 0과 1

> [!failure] 결과: 🔺 절반 정답 (1은 맞음, 0은 틀림, 2가 빠짐)

> [!success] 정답: **1 또는 2**

#### 칠판과 메모장 비유
- **칠판** = 메모리의 `count` (모두가 같이 봄, 공유)
- **메모장** = 각 스레드의 레지스터 `eax` (자기만 봄, 따로)
- `count++` = ① 칠판 숫자를 **메모장에 베낀다** → ② 메모장 숫자에 **+1** → ③ 메모장 숫자로 **칠판을 덮어쓴다**

**경우 1: 한 명씩 차례대로 → 2**

| | A 메모장 | B 메모장 | 칠판 |
|---|---|---|---|
| A ① 베끼기 | 0 | | 0 |
| A ② +1 | 1 | | 0 |
| A ③ 덮어쓰기 | 1 | | **1** |
| B ① 베끼기 | | 1 | 1 |
| B ② +1 | | 2 | 1 |
| B ③ 덮어쓰기 | | 2 | **2** ✅ |

**경우 2: 중간에 끼어듦 → 1**

| | A 메모장 | B 메모장 | 칠판 |
|---|---|---|---|
| A ① 베끼기 | 0 | | 0 |
| ⏰ 인터럽트, A 멈춤 | 0 (보관) | | 0 |
| B ① 베끼기 | 0 | 0 | 0 |
| B ② +1 | 0 | 1 | 0 |
| B ③ 덮어쓰기 | 0 | 1 | **1** |
| A 다시 시작, A ② +1 | 1 | | 1 |
| A ③ 덮어쓰기 | 1 | | **1** ❌ |

→ A는 칠판이 이미 1로 바뀐 걸 모른다. **옛날에 베껴 둔 0**에 1을 더해서 덮어썼다.

#### 왜 0은 불가능한가?
- 두 스레드 모두 **마지막 단계에서 반드시 칠판에 쓴다.**
- 쓰는 값 = 베낀 값 + 1. 베낀 값은 0 이상이니 **쓰는 값은 최소 1**이다.
- 최종 값은 마지막에 쓴 스레드의 값 → **최소 1**.
- Race condition은 더하기를 **잃어버리게** 할 뿐, 아예 안 한 것처럼 만들 수는 없다.

### Q4-2. 이해 확인

> [!question] 문제
> `count = 10`. A가 ①(베끼기)까지 하고 멈춤 → B가 ①②③ 모두 끝냄 → A가 ②③을 마저 함.
> (a) B가 끝난 직후 칠판 값 (b) A가 끝난 뒤 칠판 값 (c) 원래 나와야 했던 값

> [!quote] 내 답
> a: 11 / b: 11 / c: 12

> [!success] 결과: ✅ 모두 정답
> A는 메모장에 10을 쥐고 있다가 10 + 1 = 11로 덮어쓴다. B의 더하기 하나가 사라져서 12가 아니라 11이 된다.

---

## Q5. Lock으로 race condition 막기

> [!question] 문제
> `lock_acquire(&count_lock); count++; lock_release(&count_lock);`, `count = 10`.
> A가 lock을 잡고 ①(베끼기)까지 하고 타이머 인터럽트로 멈춤 → B가 `lock_acquire()` 호출.
> (a) B의 상태 변화는? (b) A가 ②③ 후 `lock_release()` 하면 B는? (c) 최종 `count`는?

> [!quote] 내 답
> a: ready → running을 하다가, 원래 running → ready로 타이머 인터럽트를 통해 바뀌어야 하는데, A 스레드가 멈춘 상태가 되었다
> b: 11 / c: 11

> [!failure] 결과: ❌ 오답 (3개 모두)
> - (a)는 B가 아니라 A에 대한 설명이 섞였다. 그리고 인터럽트로 멈춘 A는 "멈춤"이 아니라 **READY**다 (Q3-d).
> - (b)는 상태를 묻는 문제인데 숫자로 답했다.
> - (c) lock이 있으면 더하기가 사라지지 않으므로 **12**다.

> [!success] 정답
> - (a) B: **RUNNING → BLOCKED**. 열쇠(lock)를 A가 갖고 있어서 들어갈 수 없다. CPU를 줘도 실행할 수 없으니 BLOCKED.
> - (b) B: **BLOCKED → READY**. `lock_release()`가 기다리던 B를 깨운다. 깨어나면 RUNNING이 아니라 READY (Q3-c).
> - (c) **12**

| 순서 | A 상태 | B 상태 | 열쇠 주인 | A 메모장 | B 메모장 | 칠판 |
|---|---|---|---|---|---|---|
| A: `lock_acquire()` 🔒 | RUNNING | READY | **A** | | | 10 |
| A: ① 베끼기 | RUNNING | READY | A | 10 | | 10 |
| ⏰ 인터럽트 | **READY** | **RUNNING** | A | 10 (보관) | | 10 |
| B: `lock_acquire()` → 열쇠 없음! | READY | **BLOCKED** 💤 | A | 10 | | 10 |
| 실행 가능한 건 A뿐 → A 실행 | **RUNNING** | BLOCKED | A | 10 | | 10 |
| A: ② +1, ③ 덮어쓰기 | RUNNING | BLOCKED | A | 11 | | **11** |
| A: `lock_release()` 🔓 → B 깨움 | RUNNING | **READY** | 없음 | | | 11 |
| B 실행, `lock_acquire()` 🔒 | | RUNNING | **B** | | | 11 |
| B: ① 베끼기 (**11**을 베낌!) | | RUNNING | B | | 11 | 11 |
| B: ② +1, ③ 덮어쓰기, 🔓 | | RUNNING | 없음 | | 12 | **12** ✅ |

> [!tip] 핵심
> - **Lock은 인터럽트를 막지 않는다.** A는 여전히 중간에 멈출 수 있다.
> - Lock이 막는 건 **B가 칠판을 베끼는 것**이다. B는 A가 다 쓴 **뒤에야** 베끼므로 최신 값(11)을 가져간다.
> - 즉 ①②③이 **다른 스레드 입장에서는 한 덩어리**처럼 보이게 만든다.

> [!example] Pintos 코드 (`threads/synch.c`)
> - `lock_acquire()` → `sema_down(&lock->semaphore)` → `while (sema->value == 0) { waiters에 넣기; thread_block(); }` → **RUNNING → BLOCKED**
> - `lock_release()` → `lock->holder = NULL; sema_up(&lock->semaphore)` → `thread_unblock(waiters 맨 앞)` → **BLOCKED → READY**

### Q5-1. 이해 확인

> [!question] 문제
> A: READY (열쇠 보유), B: BLOCKED (열쇠 대기). 스케줄러는 누구를 고르나?

> [!quote] 내 답
> A. ready_list에 있는 건 A니까

> [!success] 결과: ✅ 정답
> 스케줄러(`next_thread_to_run()`)는 **`ready_list`에서만** 고른다. B는 `ready_list`가 아니라 **lock 안 세마포어의 `waiters` 리스트**에 들어가 있다.
> B는 `ready_list`에 "아직" 없는 게 아니라, `lock_release()`가 `thread_unblock()`으로 옮겨 줄 때까지 **절대** 들어가지 못한다.

> [!tip] 리스트 두 개로 기억하기
> | 상태 | 들어가 있는 리스트 | 꺼내 주는 것 |
> |---|---|---|
> | READY | `ready_list` | 스케줄러 |
> | BLOCKED | `sema->waiters` (또는 sleep list 등) | `sema_up()` → `thread_unblock()` |

### Q5-2. Lock은 왜 `holder`를 기록하나?

> [!question] 문제
> `struct lock`은 value 1인 세마포어 + `holder`(주인). 왜 굳이 `holder`를 기록할까? (A가 열쇠를 가졌는데 B가 `lock_release()`를 부르면?)

> [!quote] 내 답
> holder가 없으면 누구든 호출할 수 있다. 그러면 기록이 없거나, 무한 반복되거나, 높은 값을 가진 스레드가 CPU를 독점하는 문제가 생길 것 같다.

> [!failure] 결과: 🔺 부분 정답
> - ✅ "holder가 없으면 **누구든** release할 수 있다"는 정확하다.
> - ❌ 그 결과로 생기는 문제를 짚지 못했다. 진짜 문제는 **lock이 쓸모없어져서 race condition이 다시 생기는 것**이다.

> [!success] 정답
> **열쇠를 가진 스레드만 반납할 수 있게 하려고.** 주인이 아닌 스레드가 열쇠를 반납하면, A가 아직 칠판 작업 중인데 다른 스레드가 들어온다.

| 순서 | A | B | C | 🔑 주인 | 칠판 |
|---|---|---|---|---|---|
| A 열쇠 받음 🔒, ① 베끼기 (10) | RUNNING | | | A | 10 |
| ⏰ 인터럽트 → B 실행 | READY | RUNNING | | A | 10 |
| B가 **남의 열쇠를 반납** 🔓 (holder 없으면 가능!) | READY | RUNNING | | **없음** | 10 |
| C가 열쇠 받음 🔒, ①②③ (10 → 11) | READY | | RUNNING | C | **11** |
| A 다시 실행, ②③ (메모장 10 + 1) | RUNNING | | | C?! | **11** ❌ |

→ A와 C가 **동시에** 임계 구역 안에 있었다. 두 번 더했는데 12가 아니라 11. **Q4의 lost update가 그대로 돌아온다.**

> [!example] Pintos 코드 (`threads/synch.c`)
> - `lock_release()`: `ASSERT (lock_held_by_current_thread (lock));` → 주인이 아니면 **커널 패닉**
> - `lock_acquire()`: `ASSERT (!lock_held_by_current_thread (lock));` → 이미 가진 열쇠를 또 달라고 하면 패닉 (자기 자신을 영원히 기다리는 것 방지)
> - `lock_held_by_current_thread()`: `return lock->holder == thread_current ();`

> [!tip] 세마포어 vs Lock
> | | 세마포어 | Lock |
> |---|---|---|
> | 열쇠 개수 | `value`개 (0 이상 아무 수) | 1개 |
> | 주인 | 없음. **누구든** up/down 가능 | `holder`. **잡은 스레드만** release |
> | 주 용도 | 신호 보내기, 개수 세기 (예: A가 끝나면 B에게 알림) | 임계 구역 보호 |

> [!note] 내 답의 세 번째 추측에 대해
> "높은 우선순위 스레드가 CPU를 독점"은 이 문제의 답은 아니지만, **`holder`가 필요한 두 번째 이유**와 연결된다. Priority donation(Q9)에서 "누구에게 우선순위를 빌려줄지" 알려면 **열쇠 주인(`holder`)**을 알아야 한다.

### Q5-3. 세마포어로 신호 보내기

> [!question] 문제
> `sema_init(&done, 0)`. A: `sema_down(&done)` ⓐ 후 결과 사용. B: 파일 읽고 `sema_up(&done)` ⓑ.
> (a) A가 먼저 ⓐ에 도착하면? (b) 나중에 B가 ⓑ를 하면 A는? (c) 왜 lock으로 바꿀 수 없나?

> [!quote] 내 답
> a: 타이머 인터럽트 상태가 되어 READY / b: READY → RUNNING / c: lock은 yield하는 게 아니라서?

> [!failure] 결과: ❌ 오답 (3개 모두)
> **Q3 (b)(c)에서는 맞혔던 내용이다.** 코드 모양이 바뀌자 "기다림 = BLOCKED"를 연결하지 못했다.

> [!success] 정답
> - (a) **RUNNING → BLOCKED**. value가 0이라 꺼낼 열쇠가 없다. `sema_down()`의 `while (sema->value == 0) { ...; thread_block(); }`에 걸린다. 타이머 인터럽트와는 상관없다. A가 **스스로** 잠든다.
> - (b) **BLOCKED → READY**. `sema_up()`이 value를 1로 만들고 `thread_unblock(A)` → A는 `ready_list`로. **바로 RUNNING이 되지 않는다.** 스케줄러가 고를 때 RUNNING이 된다.
> - (c) 두 가지 이유:
>   1. **down 하는 스레드(A)와 up 하는 스레드(B)가 다르다.** lock은 잡은 스레드만 release 가능 → B가 `lock_release()` 하면 `ASSERT` 실패 (Q5-2).
>   2. lock은 **열린 상태(value 1)로 시작**한다. A가 `lock_acquire()` 하면 기다리지 않고 바로 통과해 버린다. "B가 끝날 때까지 기다리기"가 안 된다.

| | lock | 세마포어 (value 0 시작) |
|---|---|---|
| 처음 상태 | 열림 (바로 통과) | 닫힘 (누가 up 해 줄 때까지 대기) |
| 잠그는/기다리는 스레드 | A | A |
| 푸는/신호 주는 스레드 | **A 자신만** | **B (다른 스레드)** ✅ |
| 비유 | 화장실 열쇠 | 택배 도착 알림 |

> [!warning] 반복되는 약점: 상태 판단
> 상태를 물으면 "타이머 인터럽트"를 먼저 떠올리는 경향이 있다. 아래 순서로 판단하자.
> 1. 이 스레드가 **지금 코드를 실행 중**인가? → **RUNNING**
> 2. 할 일이 있고 **CPU만 받으면** 바로 실행 가능한가? → **READY**
> 3. **무언가(열쇠, 신호, 시간)를 기다리는** 중인가? → **BLOCKED**
>
> - 타이머 인터럽트는 **RUNNING → READY** 한 가지만 만든다.
> - `sema_down`/`lock_acquire`에서 못 들어가면 **스스로** BLOCKED.
> - 깨어나면(`sema_up`, `lock_release`) **항상 READY 먼저.** BLOCKED → RUNNING 직행은 없다.

### Q5-4. 신호를 먼저 보내면?

> [!question] 문제
> `sema_init(&done, 0)`. B가 먼저 ⓑ `sema_up` → 그 뒤 A가 ⓐ `sema_down`에 도착.
> (a) B가 up 한 뒤 value는? (b) A는 BLOCKED가 되나, RUNNING 그대로인가?

> [!quote] 내 답
> a: 1 / b: A는 이미 끝난 상태라 DYING인가?

> [!failure] 결과: 🔺 절반 정답 ((a) ✅, (b) ❌)

> [!success] 정답
> - (a) **1** ✅
> - (b) **RUNNING 그대로.** `sema_down()`이 value 1을 보고 0으로 줄인 뒤 **기다리지 않고 통과**한다. A는 이어서 `use_result()`를 실행한다.

> [!warning] 왜 DYING이 아닌가
> - 끝난 건 **B의 파일 읽기**지 **A가 아니다.** A는 아직 `use_result()`가 남았다.
> - DYING은 스레드가 `thread_exit()`을 부를 때만 된다. 함수 하나(`sema_down`)를 통과한 건 스레드의 끝이 아니다.

> [!tip] 세마포어는 신호를 "기억"한다
> | 순서 | value 변화 | A 상태 |
> |---|---|---|
> | A 먼저 down → B가 up (Q5-3) | 0 → (A 대기) → up이 A 깨움 | RUNNING → **BLOCKED** → READY → RUNNING |
> | B 먼저 up → A가 down (Q5-4) | 0 → **1** → 0 | **RUNNING 그대로** |
> 어느 순서든 **A는 B가 끝난 뒤에 `use_result()`를 실행한다.** 먼저 온 신호는 value에 저장되어 있다가 나중에 온 A가 꺼내 간다.

### Q5-6. 상태 판단 스피드 퀴즈

| | 상황 | 내 답 | 정답 | |
|---|---|---|---|---|
| 1 | C가 `lock_acquire()`, 아무도 lock 없음 | RUNNING | RUNNING 그대로 (바로 통과) | ✅ |
| 2 | D가 `lock_acquire()`, E가 lock 보유 | BLOCKED | RUNNING → BLOCKED | ✅ |
| 3 | F 계산 중, time slice 종료 | READY | RUNNING → READY | ✅ |
| 4 | G가 `thread_exit()` | DYING | RUNNING → DYING | ✅ |

> [!success] 결과: ✅ 4개 모두 정답
> 판단 순서(실행 중? → CPU만 있으면 됨? → 기다림?)를 적용하자 전부 맞혔다. Q3, Q5-3에서 틀린 상태 판단을 극복!

---

## Q7. Alarm Clock (busy waiting)

> [!question] 문제
> `timer_sleep(ticks)` 현재 구현:
> ```c
> int64_t start = timer_ticks ();
> while (timer_elapsed (start) < ticks)
>     thread_yield ();
> ```
> S가 `timer_sleep(100)`(1초) 호출. (a) `thread_yield()` 후 S의 상태는? (b) 스케줄러가 S를 다시 고를 수 있나? 고르면 S는 뭘 하나? (c) 1초 동안 반복되면 무엇이 문제인가?

> [!quote] 내 답
> a: RUNNING → READY / b: 고를 수 있고, 고르면 READY가 된다 / c: S만 다른 스레드보다 더 많이 사용되는 문제

> [!failure] 결과: 🔺 부분 정답 ((a) ✅, (b) 🔺, (c) 🔺)

> [!success] 정답
> - (a) **RUNNING → READY** ✅
> - (b) 고를 수 있다 ✅. 고르면 **RUNNING**이 된다 (READY가 아님). 그리고 S가 하는 일은 **"시간 됐나?" 확인 → 아직 → 다시 `thread_yield()`** 뿐이다.
> - (c) 방향은 맞다 (CPU 낭비). 정확히는 **busy waiting(바쁜 대기)**: 할 일이 없는 S가 1초 동안 계속 CPU를 받아서 **확인만 하고 양보**하기를 반복한다. 그 시간과 컨텍스트 스위치 비용이 다른 스레드가 쓸 수 있었던 CPU다. S만 있으면 CPU가 1초 동안 헛돈다.

> [!warning] 근본 원인: 상태가 틀렸다
> S는 1초 동안 **시간이 지나기를 기다리는** 중이다 → 판단 순서 ③ → **BLOCKED**여야 한다.
> 그런데 `thread_yield()`는 S를 **READY**로 만든다. READY = "CPU만 주면 할 일이 있다"는 뜻인데 S는 할 일이 없다. 그래서 스케줄러가 쓸데없이 S를 계속 고른다.

---

### Q7-2. Alarm Clock 고치기: 누가 깨우고, 시각은 어디에 저장?

> [!quote] 내 답
> a: 타이머 인터럽트 / b: elem?

> [!success] 결과: (a) ✅, (b) 🔺 절반
> - (a) **타이머 인터럽트**가 10ms마다 `sleep_list`를 확인하고 깨울 스레드를 깨운다.
> - (b) `elem`은 리스트에 끼우는 **고리(위치)**일 뿐 시각을 담지 못한다. 필요한 것:
>   1. `struct thread`에 **`int64_t wakeup_tick;`** 필드 추가 ("이 tick이 되면 깨워 줘")
>   2. BLOCKED 스레드를 모아 둘 **`sleep_list`** 추가 (`ready_list`는 있지만 잠든 스레드용은 없음)
> - `elem`은 한 번에 한 리스트에만 끼워진다. 잠든 S는 `ready_list`에서 빠지고 `sleep_list`로 간다.

### Q7-3. 새 `timer_sleep` 흐름 빈칸 채우기

```
timer_sleep(100):
  1. 지금 tick = 500이라면, wakeup_tick = 600
  2. S를 sleep_list에 넣는다
  3. S의 상태: RUNNING → BLOCKED   (thread_block())

timer_interrupt() (10ms마다):
  4. ticks++
  5. sleep_list를 보며, wakeup_tick <= ticks 인 스레드를 찾는다
  6. 찾으면 S의 상태: BLOCKED → READY   (thread_unblock())
```

| | 내 답 | 정답 | |
|---|---|---|---|
| 1 | 타이머 인터럽트 | **600** (500 + 100) | ❌ |
| 2 | Running | **sleep_list** | ❌ |
| 3 | Blocked (sema_down) | BLOCKED, **`thread_block()`** | 🔺 상태 ✅ 함수 ❌ |
| 5 | (답 없음, 6번 답과 섞임) | **`wakeup_tick <= ticks`** | ❌ |
| 6 | Blocked → Ready (sema_up) | BLOCKED → READY, **`thread_unblock()`** | 🔺 상태 ✅ 함수 ❌ |

> [!warning] 함수 이름
> `sema_down` 안에서 `thread_block()`을, `sema_up` 안에서 `thread_unblock()`을 부른다. 내가 낸 힌트가 "안에 나왔다"였는데 바깥 함수를 답했다. Alarm Clock에서는 세마포어 없이 **`thread_block()` / `thread_unblock()`을 직접** 쓴다.
> 상태 전이(BLOCKED, BLOCKED → READY)는 정확히 맞혔다. 약점이 극복된 부분.

> [!tip] 기억법
> `timer_sleep`: **시각 계산 → 줄 세우기(sleep_list) → 잠들기(thread_block)**
> `timer_interrupt`: **시계 올리기 → 깨울 사람 찾기 → 깨우기(thread_unblock)**

---

## 📝 아직 안 푼 문제
- [ ] Q5-5. Condition Variable
- [ ] Q6. `cond_wait()`을 `if`가 아니라 `while`로 감싸는 이유
- [ ] Q7-4. 인터럽트 핸들러에서 `sleep_list` 보호 (= Q8)
- [ ] Q8. 인터럽트 핸들러에서 잠들면 안 되는 이유
- [ ] Q9. Priority inversion과 priority donation
- [ ] Q10. Nested donation에 필요한 `struct thread` 필드
