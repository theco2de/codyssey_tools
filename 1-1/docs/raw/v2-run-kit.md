# v2 재실행 키트 (모델: Claude sonnet 5.5)

규칙: 응답은 편집하지 않고 그대로 붙인다. v2 지침은 **새 프로젝트(실제 이름: `codyssey-1-1-1-2`)**에 넣는다. v1 프로젝트와 섞지 않는다.

## 실행 순서

| 대화 | 보낼 메시지 | 목적 |
|---|---|---|
| 대화 1 (새 대화) | Turn 1, Turn 3(Turn 1과 똑같이), Turn 5 순서대로 | Turn 2/4의 문서 제목 지어내기, 코드 블록 나열, Turn 6의 "같은 에러" 재확인 |
| 대화 1에서 이어서 | Turn 23 | Turn 24의 확인 방법이 전제를 밝히는지 확인 (중간 Turn 7~22는 생략) |

- 새 대화를 열어 같은 순서를 **한 번 더**(총 2회) 실행하면 일정한지 볼 수 있다.
- 응답 확인 지점
  - Turn 2 응답에 공식 문서 항목 제목이 따옴표로 나오는지
  - Turn 4 응답의 코드 블록이 방법마다 나뉘는지
  - Turn 6 응답에서 `@Primary` 두 곳 설명이 "같은 에러"로 쓰이지 않는지
  - Turn 24 응답의 확인 방법에 "(전제: ...)"가 있는지

## A. 지침(프로젝트 지침)에 넣을 v2 전문

`docs/system-design.md` §9-2의 코드 블록 전체를 복사하고, 맨 아래 "# 예시" 줄을 아래 예시 1~4로 바꿔 붙인다 (v1과 동일한 예시).

→ 예시 1~4 원문은 `docs/raw/fewshot-run-kit.md`의 A 블록 "# 예시" 아래 내용을 그대로 쓴다.

## B. 보낼 메시지

Turn 1과 Turn 3: `docs/run-log.md` Turn 1의 코드 블록 전체.
Turn 5: `docs/run-log.md` Turn 5의 코드 블록 전체.
Turn 23: `docs/run-log.md` Turn 23의 코드 블록 전체.

## 결과 원문 붙이는 곳

### 대화 1: Turn 2

```text
→ 결과 원문은 docs/run-log.md §4 Turn 2 참고
```

### 대화 1: Turn 4

```text
→ 결과 원문은 docs/run-log.md §4 Turn 4 참고
```

### 대화 1: Turn 6

```text
→ 결과 원문은 docs/run-log.md §4 Turn 6 참고
```

### 대화 1: Turn 24

```text
→ 결과 원문은 docs/run-log.md §4 Turn 24 참고
```
