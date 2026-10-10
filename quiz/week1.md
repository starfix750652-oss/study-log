# 1주차 문제 기록

제가 한 질문과 생각 변화는 [`notes/week1-questions.md`](../notes/week1-questions.md)에 있습니다.

문제는 Claude가 냈고, 채점도 Claude가 했습니다. **자기 채점**이라 점수는 참고용입니다. 정답은 Claude가 코드를 실제로 실행해서 확인했습니다.

표시: ✅ 맞음 · △ 절반 · ❌ 틀림

## 1일차 — 타입힌트 (2026-10-10)

**1.** 무엇이 출력될까?
```python
def add(a: int, b: int) -> int:
    return a + b
print(add("3", "4"))
```
- 제 답: `7`
- 정답: `34` ❌
- 이유: `"3"`, `"4"`는 문자라서 `+`가 이어 붙이기가 됩니다. 파이썬은 실행할 때 힌트를 검사하지 않습니다.

**2.** 후보 리스트를 받아 가장 긴 문장을, 비면 `None`을 돌려주는 함수의 빈칸
```python
def longest(cands: ___) -> ___:
```
- 제 답: `str`, `str`
- 정답: `list[str]`, `str | None` △
- 이유: 여러 문장을 받으니 리스트이고, 비면 `None`이 나올 수 있습니다.

**3.** 타입힌트를 실제로 검사하는 것은?
- 제 답: 디버깅
- 정답: 별도 도구(`mypy`, `pyright`) ❌
- 이유: 디버깅은 실행 후 원인 찾기이고, `mypy`는 실행 전 검사입니다.

**4.** `longest` 몸통 쓰기
- 제 답: 모르겠음
- 정답:
```python
def longest(cands: list[str]) -> str | None:
    if not cands:
        return None
    return max(cands, key=len)
```

## 2일차 — dataclass (2026-10-10)

**복습 A.** 무엇이 출력될까?
```python
def twice(x: int) -> int:
    return x * 2
print(twice("ab"))
```
- 제 답: ab에 숫자가 없는데?
- 정답: `abab` ❌ → 설명 듣고 이해
- 이유: 문자 * 숫자는 반복입니다.

**복습 B.** 실행 전에 타입 실수를 알려 주는 것은?
- 제 답: `mypy`
- 정답: `mypy` ✅

아래 1~2번은 이 클래스를 씁니다.
```python
from dataclasses import dataclass

@dataclass
class Req:
    rel: str    # 관계
    pack: str   # 분야
    text: str   # 문장
```

**1.** `print(Req("반장", "공장", "그거 빨리 옮겨"))`의 출력은?
- 제 답: 출력
- 정답: `Req(rel='반장', pack='공장', text='그거 빨리 옮겨')` △
- 이유: `@dataclass`가 출력 기능(`__repr__`)을 자동으로 만듭니다.

**2.** `Req("친구", "일상")`을 실행하면?
- 제 답: 출력은 안 되고 저장만 됨
- 정답: `TypeError` (`text` 빠짐) ❌
- 이유: 파이썬은 값의 종류는 안 막지만, 칸 개수가 모자라면 막습니다.

**3.** 아래에서 `r.text = "다른 문장"`을 하면?
```python
@dataclass(frozen=True)
class Req:
    rel: str
    pack: str
    text: str

r = Req("반장", "공장", "그거 빨리 옮겨")
```
- 제 답: 멈춰서 저장 안 됨
- 정답: `FrozenInstanceError`, 값은 그대로 ✅

**4.** 관계·분야·문장을 `Req` 하나로 묶어 넘기면 좋은 점은?
- 제 답: 정의를 안 해도 되고 int·str 오류가 나도 넘어감
- 정답: 칸이 늘어도 클래스 한 곳만 고치면 되고, 이름으로 꺼내서 순서를 헷갈리지 않음 ❌
- 이유: 타입 오류를 넘어가는 건 dataclass가 아니라 파이썬 힌트의 한계입니다.

**추가 질문.** `Req(text="친구", "상자")`는 에러가 안 날까?
- 제 예상: 안 남
- 정답: `SyntaxError` ❌
- 이유: 이름 붙인 값 뒤에 순서로 넣는 값이 올 수 없습니다. 고쳐도 칸이 하나 모자랍니다.

## 합계 (자기 채점)

| | ✅ | △ | ❌ |
|---|---|---|---|
| 1일차 | 0 | 1 | 3 |
| 2일차 | 2 | 1 | 4 |

## 제가 반복해서 틀린 것

"파이썬이 알아서 넘어가 주거나 저장해 줄 것"이라고 믿었습니다. 실제로는 **값의 종류는 안 막고, 칸 개수와 문법은 막습니다.** 3일차 예외처리에서 이 구분을 다시 확인합니다.
