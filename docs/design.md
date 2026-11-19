# SV_UVM_AI 설계 문서

- 상태: 초안 v0.1 (2026-11-19)
- 표기: **결정**은 확정된 사항, **제안**은 아직 확정하지 않은 사항이다.

---

## 1. 목적과 범위

| 구분 | 내용 |
|---|---|
| 결정 | 개인 학습용 SystemVerilog·UVM 튜터를 Claude Code 위에서 동작하도록 만든다. |
| 결정 | 개념 설명에서 시작하고, 실습 문제 출제, 채점, 모르는 부분 보충 설명까지 제공한다. |
| 결정 | 학습 체계의 중심은 약점 집중, 스스로 떠올리기(인출 연습), 간격 복습, 직접 프로젝트 수행이다. |
| 결정 | 실무에서 가장 도움이 되는 지식을 우선한다. |
| 결정 | 구조적으로 설계한다 (문제·엔진·튜터 규칙·기록 분리, 8절). |
| 이후 검토 | Git으로 공유해 다른 Claude 사용자도 같은 환경을 쓰게 한다. |

---

## 2. 학습 원칙과 튜터 구현 (제안)

| 원칙 | 튜터에서 구현하는 방법 |
|---|---|
| 약점 집중 | 개념별 숙련도(0~4, 6.2절)와 오답 유형 태그를 기록한다. 다음 문제는 숙련도가 낮은 개념에서 출제한다. |
| 인출 연습 | 설명을 먼저 보여주지 않는다. 노트 없이 "설명해 보기, 출력 예측, 빈칸 채우기, 버그 찾기"로 시작하고, 채점 후 틀린 부분만 보충한다. |
| 간격 복습 | 문항마다 다음 복습일을 1 → 3 → 7 → 14 → 30일로 늘린다. 맞히면 다음 간격, 틀리면 1일로 되돌린다. |
| 프로젝트 | 개념 진도와 맞물린 4단계 프로젝트(5절)를 진행한다. |

---

## 3. 학습 범위와 실무 우선순위 (제안)

### 3.1 우선순위 기준

| 단계 | 기준 | 학습 깊이 |
|---|---|---|
| 1순위 | 실무에서 매일 읽고 고치는 코드 | 동작 원리와 함정까지 깊게 |
| 2순위 | 자주 쓰지만 패턴이 정해져 있는 것 | 사용법 중심 |
| 3순위 | 필요할 때 찾아보는 것 | 개념과 사용 시점만 |

### 3.2 SystemVerilog 개념 목록

| # | 개념 | 꼭 짚을 점 | 순위 |
|---|---|---|---|
| SV1 | Data type | 2-state/4-state(bit·logic·int·integer), enum, packed/unpacked struct, typedef, X 전파 | 1 |
| SV2 | Array | packed/unpacked, dynamic array, queue, associative array, array method(find, sort, sum with), sum 결과 폭 함정 | 1 |
| SV3 | Procedural·Scheduling | always_comb/ff, blocking/nonblocking, event region(Active/NBA/Reactive), TB–DUT race 원인 | 1 |
| SV4 | Task·Function | automatic/static, ref 인자, void function, static 변수 공유 함정 | 1 |
| SV5 | Interface | modport, clocking block(input/output skew), virtual interface, sampling·driving 시점 | 1 |
| SV6 | Class 기초 | handle과 object, new(), shallow/deep copy, static member, null handle | 1 |
| SV7 | OOP | 상속, super, virtual method, polymorphism, $cast, virtual class·pure virtual | 1 |
| SV8 | Package·Parameterized class | package/import와 `include 차이, parameterized class, typedef class, compile 순서 | 2 |
| SV9 | Randomization | rand/randc, inside, dist(:=와 :/), implication(->), solve-before, soft, foreach constraint, pre/post_randomize, randomize() with, rand_mode/constraint_mode, randomize() 실패 확인 | 1 |
| SV10 | Thread·IPC | fork-join/join_any/join_none, disable fork, wait fork, event(.triggered), mailbox, semaphore, join_none 루프 변수 캡처 | 1 |
| SV11 | Functional coverage | covergroup, coverpoint, bins(explicit/illegal/ignore), cross, sample 시점, per_instance, code coverage와 차이 | 2 |
| SV12 | SVA | immediate/concurrent, sequence/property, \|-> 와 \|=>, ##, [*]·[->]·[=], $rose·$stable·$past, disable iff, assert/assume/cover, bind, vacuous pass | 2 |
| SV13 | DPI-C | import/export, C reference model 연결 | 3 |

### 3.3 UVM 개념 목록

| # | 개념 | 꼭 짚을 점 | 순위 |
|---|---|---|---|
| U1 | TB 구조 | test → env → agent(sequencer·driver·monitor) → scoreboard·coverage, active/passive agent | 1 |
| U2 | Base class | uvm_object/uvm_component, sequence_item, do_copy·do_compare·convert2string과 field macro 비교 | 1 |
| U3 | Factory | `uvm_*_utils, type_id::create, type/instance override, new() 대신 create를 쓰는 이유 | 1 |
| U4 | Phase | build(top-down), connect(bottom-up), run, extract/check/report, function phase와 task phase | 1 |
| U5 | Objection·End of test | raise/drop_objection, drain time, 0 time 종료와 hang 원인 | 1 |
| U6 | Configuration | uvm_config_db set/get(context, path, wildcard, 우선순위), virtual interface 전달, config object 패턴 | 1 |
| U7 | TLM | port/export/imp, analysis port, tlm_analysis_fifo, `uvm_analysis_imp_decl | 1 |
| U8 | Sequence–Driver handshake | start_item/finish_item, get_next_item/item_done, try_next_item, response·set_id_info | 1 |
| U9 | Sequence 심화 | virtual sequence/sequencer, p_sequencer/m_sequencer (2순위), lock/grab·arbitration·sequence library (3순위) | 2 |
| U10 | Monitor·Scoreboard | passive sampling, transaction 재구성, predictor·reference model, in-order/out-of-order 비교, check_phase 보고 | 1 |
| U11 | Coverage collector | uvm_subscriber, transaction 기준 sampling | 2 |
| U12 | Reporting | `uvm_info/error/fatal, verbosity, severity action, report catcher, quit count | 1 |
| U13 | RAL | uvm_reg/field/block/map, adapter(reg2bus/bus2reg), implicit/explicit prediction, frontdoor/backdoor, mirror/desired, built-in sequence | 2 |
| U14 | 실행·Debug | +UVM_TESTNAME, seed, print_topology, factory.print, +UVM_CONFIG_DB_TRACE, +UVM_OBJECTION_TRACE, +UVM_PHASE_TRACE | 1 |

### 3.4 선수 관계

- SV5(Interface), SV6·SV7(Class·OOP), SV9(Randomization), SV10(Thread·IPC)이 UVM의 전제다. 이 개념이 숙련도 3 미만이면 UVM 신규 개념으로 넘어가지 않는다.
- 세부 선수 관계는 curriculum.yaml에 정의한다.

---

## 4. 실무 버그 패턴 (버그 찾기 문항 소재, 제안)

| # | 패턴 | 증상 | 관련 개념 |
|---|---|---|---|
| B1 | randomize() 반환값 미확인 | constraint 충돌 시 이전 값으로 진행 | SV9 |
| B2 | monitor가 같은 handle 재사용 (copy 누락) | scoreboard에 쌓인 값이 마지막 값으로 덮어써짐 | SV6, U10 |
| B3 | config_db set/get 경로·타입 불일치 | get 실패, virtual interface null | U6 |
| B4 | objection 누락 / drop 누락 | 0 time 종료 / 시뮬레이션 hang | U5 |
| B5 | super.build_phase 누락, factory 등록 누락 | 하위 component 미생성, override 미적용 | U3, U4 |
| B6 | analysis port 미연결 | scoreboard가 비교 없이 PASS | U7, U10 |
| B7 | clocking block 없이 driving | TB–DUT race, 시뮬레이터마다 결과 다름 | SV3, SV5 |
| B8 | join_none 루프 변수 캡처 | 모든 thread가 마지막 루프 값 사용 | SV10 |
| B9 | X가 섞인 값을 `!=`로 비교 | 에러를 놓침 (`!==` 사용 필요) | SV1 |

---

## 5. 프로젝트 단계 (제안)

| 단계 | 내용 | 연결 개념 |
|---|---|---|
| P1 | SV만으로 FIFO 계층형 TB (generator·driver·monitor·scoreboard·coverage) | SV 1순위 전부 |
| P2 | AXI4-Lite 레지스터 블록 UVM 환경 + RAL | UVM 1순위, U13 |
| P3 | AXI4 master agent + memory slave (burst, outstanding, scoreboard) | U9, SV12 |
| P4 | traffic generator(대역폭·패턴 설정) + performance monitor(latency 분포, 구간 bandwidth) + 리포트 | 성능 검증 |

- DUT는 오픈소스 모델을 쓴다. 선택 시 라이선스를 확인한다 (12절 미정 사항).

---

## 6. 학습 진행 (제안)

### 6.1 하루 루틴 (40분 예시)

| 시간 | 내용 |
|---|---|
| 10분 | 오늘 복습 기한이 된 문항 (노트 없이 인출) |
| 20분 | 약점 개념 훈련 또는 신규 개념 (설명 → 예제 실행 → 확인 질문) |
| 10분 | 프로젝트 진행 또는 버그 찾기 1문제 |
| 주 1회 | 여러 주제를 섞은 종합 테스트 |

### 6.2 숙련도 모델

| 숙련도 | 상태 | 조건 |
|---|---|---|
| 0 | 미학습 | - |
| 1 | 설명 들음 | 개념 설명과 예제 실행 완료 |
| 2 | 확인 통과 | 당일 확인 질문 통과 |
| 3 | 기억 정착 | 7일 이상 간격 복습에서 연속 정답 |
| 4 | 적용 | 관련 실습 문제 또는 프로젝트 단계 통과 |

- 개념 완료 기준: 숙련도 4.
- 프로젝트 단계 완료 기준: 해당 프로젝트 완료.

### 6.3 출제 우선순위

1. 복습 기한이 된 문항
2. 숙련도가 낮은 1순위 개념 (오답 유형 태그가 많은 순)
3. 선수 관계를 만족한 신규 개념

### 6.4 문항 유형

| 유형 | 채점 방법 |
|---|---|
| 설명해 보기 | Claude가 card.md의 "꼭 짚을 점" 기준으로 채점 |
| 출력 예측 | 예제를 실제로 실행한 결과와 비교 |
| 빈칸 채우기·코드 작성 | 시뮬레이션 결과 + Claude 코드 리뷰 |
| 버그 찾기 | 수정본이 TB를 통과하는지 + 원인 설명 채점 |

---

## 7. 채점 (제안)

| 항목 | 방법 |
|---|---|
| 기능 점수 | self-checking TB의 시뮬레이션 결과 (예: constraint 문제는 다수 randomize 후 위반·분포 검사) |
| 품질 점수 | Claude가 problem.md의 채점 기준표로 코드 스타일, 함정, 개선점을 리뷰 |
| 문항 등록 전 검증 | 모범답안은 pass, 의도적으로 틀린 답안은 fail인지 확인. 통과하지 못한 문항은 등록하지 않는다. |

---

## 8. 저장소 구조 (제안)

```
SV_UVM_AI/
├─ CLAUDE.md                 # 튜터 역할·공통 규칙 (짧게)
├─ .claude/skills/           # teach, quiz, grade, review 절차
├─ docs/design.md            # 이 문서 (결정 사항의 단일 출처)
├─ curriculum.yaml           # 개념 목록·선수 관계·순위
├─ concepts/<sv|uvm>/<id_name>/
│   ├─ card.md               # 학습 목표, 꼭 짚을 점, 흔한 실수
│   ├─ examples/             # 실제로 실행해 확인한 예제
│   └─ checks.md             # 확인 질문과 정답
├─ problems/<topic>/<id>/
│   ├─ problem.md            # 문제, 배점, 채점 기준
│   ├─ template.sv           # 답안 틀
│   ├─ tb/                   # 채점용 testbench
│   └─ solution/             # 모범답안
├─ projects/                 # P1~P4 사양
├─ scripts/
│   ├─ run_sim.sh            # 시뮬레이터 실행 (툴 교체 가능)
│   └─ check.py              # 로그 → result.json
├─ workspace/                # 내 답안 (gitignore)
└─ progress/                 # 숙련도·복습 일정·오답 기록 (gitignore)
```

- 구성 요소 분리: 문제(데이터) / 엔진(scripts) / 튜터 규칙(CLAUDE.md, skills) / 기록(progress).

---

## 9. 실행 환경과 역할 분담 (제안)

| 환경 | 역할 | 제약 |
|---|---|---|
| claude.ai (Work) | 설계, 문서, 개념 카드·문제 초안, 자료 조사 | 시뮬레이터 실행 불가 |
| PC Claude Code | 시뮬레이터 실행, 예제·TB 검증, 튜터 실사용 | PC 필요 |

- 두 환경은 이 저장소로 동기화한다.
- 시뮬레이터 후보: Verilator, Icarus Verilog. coverage·SVA·UVM 지원 범위가 툴과 버전마다 다르므로 환경 확인 단계에서 직접 확인한다.

---

## 10. 진행 단계 (제안)

1. 환경 확인: Verilator·Icarus 설치, SV class·randomize·UVM hello world 실행, SV11·SV12 지원 여부 확인
2. curriculum.yaml 작성 (3절 기준)
3. 개념 카드 템플릿 작성, 카드 1개 완성 (SV9 Randomization)
4. teach·review skill 작성
5. 숙련도·간격 복습 기록 구현
6. 실습 문제와 시뮬레이션 채점 (run_sim.sh, check.py), 첫 문제는 AXI 4KB boundary constraint
7. 프로젝트 P1~P4
8. (선택) Git 공유용 패키징

---

## 11. 주의 사항

- 튜터 제작에 시간이 몰리지 않게 한다. 최소 기능(카드 + 확인 질문 + 기록) 이후에는 학습 시간을 우선한다.
- 카드 초안은 Claude가 작성해도, 예제는 직접 실행하고 "흔한 실수"는 직접 작성한다.
- 책·사이트의 문제와 본문을 복사하지 않는다. 외부 코드는 라이선스를 확인하고, MIT 등은 원본 LICENSE와 출처를 유지한다.
- 저장소가 현재 public이다. 모범답안·외부 자료를 넣기 전에 공개 범위를 정한다.
- 회사 코드·스크립트·회사 라이선스로 만든 결과물은 넣지 않는다.

---

## 12. 미정 사항

| 항목 | 내용 |
|---|---|
| 개발 환경 | OS(Windows+WSL 등), 시뮬레이터 버전 |
| 공개 범위 | public 유지 또는 private 전환 |
| 학습 시간 | 하루 루틴 시간 (6.1절의 40분은 예시) |
| 프로젝트 DUT | P2~P4에 쓸 오픈소스 모델과 라이선스 |
| 기록 형식 | 숙련도·복습 일정 데이터 형식 (YAML 등) |

---

## 13. 참고 자료

| 자료 | 용도 |
|---|---|
| [hdlbits-tutor](https://skillselion.com/skills/adrioui/hardware/hdlbits-tutor) | Claude Code 튜터 skill 구조 (단계별 힌트, 에러 분류) |
| [universal-verification-methodology](https://github.com/universal-verification-methodology) | Verilator로 UVM 실행하는 학습 모듈 (learn_uvm2017_sv_verilator) |
| [yuri-panchul/systemverilog-homework](https://github.com/yuri-panchul/systemverilog-homework) | SV 과제 구성 참고 (MIT) |
| [Doulos Easier UVM 가이드라인 요약](https://doulos.com/knowhow/systemverilog/uvm/easier-uvm/easier-uvm-coding-guidelines/summary-of-the-easier-uvm-coding-guidelines) | UVM 코드 리뷰 채점 기준 |
| [VLSIVerify](https://vlsiverify.com/) | 주제별 퀴즈·튜토리얼 |
| [EDA Playground](https://edaplayground.com/) | 상용 시뮬레이터로 UVM 실행 |
| [Claude Code 플러그인 마켓플레이스](https://code.claude.com/docs/en/plugin-marketplaces) | Git 공유 시 배포 방식 |
