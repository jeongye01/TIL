### 1. **의존성 주입이란?**

- **필요한 객체(의존성)를 직접 생성하지 않고, 외부에서 주입받는 설계 방식**
- 예: 자동차가 엔진을 직접 만들지 않고, 외부에서 엔진을 받아 사용
- 장점:
    1. 코드 재사용성 : 공통 로직(페이징, 인증 등)을 의존성 함수로 만들고 Depends로 재사용, 코드 간결화 및 중복 감소.
    2. 관심사 분리 (SoC): 경로 작동 함수는 핵심 비즈니스 로직에 집중. 부가 작업(인증, DB준비 등)은 의존성 함수에 위임하여 코드 가독성 개선 
    3. 테스트 용이성 : 실제 의존성 대신 테스트용 가짜(Mock) 의존성을 쉽게 주입하여 빠르고 격리된 테스트 가능.
    4. 유지보수 용이성 : 공통 로직이 한 곳에 모여있어 수정이 용이.

---

### 2. **왜 쓰는가?**

- 객체/함수의 **구체 구현에 대한 결합도를 낮추기 위해**
- 코드 재사용성 향상
- 테스트 환경에서 독립적인 검증 가능

---

### 3. **Python 예시**

### ❌ 의존성을 직접 생성

```python
class Engine:
    def start(self):
        print("엔진 시동!")

class Car:
    def __init__(self):
        self.engine = Engine()  # 직접 생성

    def run(self):
        self.engine.start()

car = Car()
car.run()

```

- `Car`가 `Engine` 구현체에 강하게 묶여 있음 → 교체 어려움

### ✅ 의존성 주입

```python
class Engine:
    def start(self):
        print("엔진 시동!")

class Car:
    def __init__(self, engine: Engine):  # 외부에서 주입
        self.engine = engine

    def run(self):
        self.engine.start()

engine = Engine()
car = Car(engine)  # 주입
car.run()

```

- 다른 구현체(`ElectricEngine`)로 교체가 쉬움

---

### 4. **FastAPI에서의 DI**

- `Depends`를 사용해 의존성 주입 구현
- 의존성 주입이 필요한 **일반적인 시나리오**
    - 데이터베이스 세션 관리, 사용자 인증 및 권한 검사, 공통 파라미터 처리 (페이징, 필터링 등), 설정값 또는 공유 자원 로딩, 로깅 또는 모니터링
- 주입받는 대상은 **함수, 클래스, 제너레이터** 모두 가능

```python
from fastapi import Depends, FastAPI

app = FastAPI()

def get_db():
    db = "데이터베이스 연결 객체"
    try:
        yield db
    finally:
        print("DB 연결 종료")

@app.get("/items")
def read_items(db=Depends(get_db)):
    return {"db": db}

```

- `read_items`는 `db`를 직접 생성하지 않고 `get_db`로부터 주입받음
- FastAPI가 실행 시점에 `get_db` 실행 → 반환값을 `read_items`에 넣어줌

---

### 5. **의존성 주입 흐름 (FastAPI)**

1. 요청이 들어옴
2. FastAPI가 엔드포인트 실행 전, `Depends`로 지정된 함수를 실행
3. 반환값을 엔드포인트 파라미터에 주입
4. 엔드포인트 실행
5. `yield` 사용 시 종료 단계에서 cleanup 처리 수행

### 6. **의존성 캐싱 (Scope: Per Request)**

- **핵심 규칙:** **하나의 요청(Single Request) 내에서는 각 의존성 함수가 최대 한 번만 실행됨.**
- **동작:** 여러 곳에서 동일한 의존성을 사용해도, FastAPI는 해당 의존성 함수의 결과를 **캐싱**하여 재사용.
- **이유:**
    - **효율성:** DB 연결 등 비용이 비싼 작업을 요청마다 반복하지 않도록 방지.
    - **일관성:** 요청 처리 중 동일한 의존성 값이 필요할 때 항상 같은 값을 보장.
- **예시:** `dependency_a`와 `dependency_b`가 모두 필요한 경우에도 `dependency_a` 함수는 **한 번만** 실행되고, 그 결과가 양쪽에 재사용됨.
