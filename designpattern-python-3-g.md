# Python에서의 디자인 패턴 적용

## 1. OOP 정적 언어 vs. Python: 디자인 패턴 적용 차이점

Python은 Java나 C++ 같은 **정적 객체지향 언어**와 달리 **동적 타이핑**과 **일급 함수**를 지원하여, GoF(Gang of Four) 디자인 패턴의 구현 방식에 큰 차이가 있습니다. 많은 디자인 패턴은 원래 **자바/C++의 한계**를 보완하려고 고안되었는데, Python은 이러한 한계가 없어서 패턴의 필요성이 줄어드는 경우가 많습니다[^1].

예를 들어 GoF의 23개 패턴 중 16개는 **동적 언어에서는 언어 자체 기능으로 해결되거나 구현이 훨씬 단순**해진다는 보고도 있습니다.

Python은 다음과 같은 점에서 디자인 패턴의 구조를 단순화할 수 있습니다:

- **Duck Typing**: 인터페이스 없이도 동작
- **First-Class Function**: 함수 자체를 전달/교체 가능
- **모듈 단위의 싱글톤 구현**
- **런타임 동작 제어**: 조건에 따라 동적으로 클래스나 메서드 구성 가능

결론: Python에서는 패턴의 목적은 유지하되, **Pythonic한 방식으로 간결하게** 구현하는 것이 일반적입니다.

---

## 2. Python에서 자주 사용되는 디자인 패턴과 구현 예시

### 싱글톤 (Singleton)

```python
class Singleton:
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
````

* 또는 **모듈 수준 전역 변수** 사용으로 대체 가능
* 모듈은 최초 1회만 로드되므로 전역 객체처럼 동작

---

### 팩토리 (Factory)

```python
class JapaneseGetter:
    def get(self, msgid):
        return {"dog": "犬", "cat": "猫"}.get(msgid, msgid)

def get_localizer(language="English"):
    return {"English": str, "Japanese": JapaneseGetter}[language]()
```

* 클래스 자체를 딕셔너리로 매핑해 간단한 팩토리 구현
* 추상 클래스나 인터페이스 없이도 구현 가능

---

### 옵저버 (Observer)

```python
class Subject:
    def __init__(self):
        self.observers = []
    def register(self, obs):
        self.observers.append(obs)
    def notify(self, msg):
        for obs in self.observers:
            obs.update(msg)
```

* Python에서는 콜백 함수로도 옵저버 등록 가능
* `Observer`는 인터페이스 구현 대신 `update()`만 있으면 됨

---

### 전략 (Strategy)

```python
def add(a, b): return a + b
def sub(a, b): return a - b

def compute(strategy, x, y): return strategy(x, y)
```

* 클래스를 사용하지 않고 함수 전달로 전략 구현
* 동적 교체도 매우 간단

---

### 데코레이터 (Decorator)

```python
def make_bold(func):
    def wrapper(*args, **kwargs):
        return f"<b>{func(*args, **kwargs)}</b>"
    return wrapper

@make_bold
def greet(name): return f"Hello, {name}"
```

* 함수 기반 데코레이터로 부가기능을 래핑
* 클래스 데코레이터나 객체 데코레이터도 구현 가능

---

### 템플릿 메서드 (Template Method)

```python
from abc import ABC, abstractmethod

class DataParser(ABC):
    def parse(self, filepath):
        data = self.load_data(filepath)
        return self.process_data(data)

    @abstractmethod
    def load_data(self, filepath): pass

    @abstractmethod
    def process_data(self, data): pass
```

* `abc.ABC`와 `@abstractmethod`로 추상 메서드 구현 가능

---

### 상태 (State)

```python
class AmState:
    def toggle_band(self): print("FM으로 전환")

class FmState:
    def toggle_band(self): print("AM으로 전환")

class Radio:
    def __init__(self): self.state = AmState()
    def toggle(self): self.state.toggle_band()
```

* 상태 클래스 교체로 동작 전환
* 런타임에 객체 속성 변경이 자유로움

---

## 3. Python에서 패턴 구현에 도움 되는 라이브러리

| 라이브러리                  | 설명                                           |
| ---------------------- | -------------------------------------------- |
| `abc`                  | 추상 클래스 구현 (`@abstractmethod`)                |
| `dataclasses`          | 빌더 패턴 대체, 간단한 데이터 구조 선언                      |
| `typing`               | 타입 힌트 제공 (`Protocol`, `Callable`, `TypeVar`) |
| `dependency_injector`  | 의존성 주입 패턴 구현 프레임워크                           |
| `PyPattyrn`            | 다양한 디자인 패턴 메타클래스 제공 (교육용)                    |
| `faif/python-patterns` | GitHub 오픈소스 패턴 예시 모음                         |

---

## 4. Python 커뮤니티의 베스트 프랙티스

* 패턴을 **남용하지 말 것**: Java처럼 억지로 구조 만들지 말고, 간결한 해결법 선호
* **Pythonic한 방식 우선**: 데코레이터 문법, 일급 함수, 모듈 활용
* **조합(Composition)** 선호: 상속보다 객체 조합 위주로 설계
* 패턴보다는 **문제 해결 중심**의 구현 강조
* 언어의 **표현력**을 활용해 패턴 목적을 자연스럽게 달성

---

## 5. Python 언어 특성이 디자인 패턴에 미치는 영향

| 언어 특성   | 패턴 영향                             |
| ------- | --------------------------------- |
| 동적 타이핑  | 인터페이스, 추상화 없이도 패턴 구현 가능           |
| 일급 함수   | 전략, 커맨드, 옵저버 등을 함수로 간단히 구현        |
| 모듈      | 싱글톤 구현을 모듈로 대체 가능                 |
| 덕 타이핑   | Visitor, Composite 등에서 인터페이스 불필요  |
| 메타프로그래밍 | 리플렉션 및 동적 메서드/속성 조작으로 패턴 응용 확장 가능 |

---

## 참고 자료

* Peter Norvig: [Design Patterns in Dynamic Languages](http://norvig.com/design-patterns/)
* Brandon Rhodes: [Python Design Patterns Guide](https://python-patterns.guide/)
* faif/python-patterns: [https://github.com/faif/python-patterns](https://github.com/faif/python-patterns)

[^1]: Peter Norvig, *Design Patterns in Dynamic Languages*

