### 1. **Pydantic이란?**

- **Python 데이터 검증·변환 라이브러리**
- FastAPI의 **요청(Request) / 응답(Response) 모델**을 정의하는 핵심 도구
- **`BaseModel`** 상속을 통해 데이터 구조, 타입, 검증 규칙을 선언
- `BaseModel`을 사용하여 데이터 **스키마(구조)**를 정의하는 **모델**을 만들 수 있다.
- 잘못된 데이터 → **422 Unprocessable Entity** 자동 반환

---

### 2. **주요 기능**

1. **데이터 검증 (Validation)**
    - 타입 검사 (`int`, `str`, `EmailStr`, `UUID` 등)
    - 범위·조건 검사 (`min_length`, `ge`, `le` 등)
2. **타입 변환 (Parsing)**
    - `"123"` → `int`
    - `"2025-08-14"` → `datetime`
3. **스키마 자동 생성**
    - `/docs` (Swagger UI)와 `/openapi.json`에 반영
4. **직렬화/역직렬화**
    - Python 객체 ↔ JSON 변환 자동 처리

---

### 3. **사용 예시**

```python

from pydantic import BaseModel, EmailStr
from fastapi import FastAPI

app = FastAPI()

class User(BaseModel):
    name: str
    age: int
    email: EmailStr

@app.post("/users")
async def create_user(user: User):
    return {"data": user}

```

- 요청 JSON → `User` 모델로 검증·변환 후 함수에 전달
- 실패 시 422 응답 자동 반환

---

### 4. **검증 위치별 활용**

| 위치 | 예시 코드 | 설명 |
| --- | --- | --- |
| **Body** | `user: User` | JSON Body 검증 |
| **Query** | `q: int = Query(..., ge=0)` | 쿼리스트링 검증 |
| **Path** | `id: int = Path(..., gt=0)` | Path 파라미터 검증 |
| **Response** | `response_model=User` | 응답 데이터 구조 보장 |

## 📌 Pydantic 동작 순서

1. **JSON → Python 객체**
    - FastAPI가 HTTP 요청의 JSON Body를 읽음
    - Pydantic 모델(`BaseModel`)로 **파싱(Parsing)**
        
        → 타입 변환 + 데이터 검증 수행
        
        예: `"123"` → `int`, `"2025-08-14"` → `datetime`
        
2. **데이터 검증**
    - 선언된 타입과 조건(`min_length`, `ge`, `regex` 등)에 맞는지 검사
    - 맞지 않으면 **422 Unprocessable Entity** 에러 자동 발생
3. **Python 객체 사용**
    - 검증된 데이터가 Python 객체 형태로 엔드포인트 함수에 전달
    - `.name`, `.age` 처럼 속성으로 접근 가능
4. **Python 객체 → JSON**
    - 응답 시 `response_model`을 지정하면 Pydantic이 자동 직렬화
    - `model_instance.json()` 메서드로도 직접 변환 가능

### 5. **`Field` — 필드 제약/메타데이터 선언**

- 역할: **타입 힌트 + 제약조건 + 문서화 메타데이터**를 한 번에 붙인다.
- 주로 `typing.Annotated`와 함께 사용 권장.

```python
from typing import Annotated
from pydantic import BaseModel, Field, EmailStr

class User(BaseModel):
    # 숫자 범위 제약
    age: Annotated[int, Field(ge=0, le=150)]
    # 문자열 길이/패턴 제약 + 문서화
    name: Annotated[str, Field(min_length=1, max_length=30, description="표시명")]
    # 기본값 팩토리 (빈 리스트 기본)
    tags: list[str] = Field(default_factory=list)
    # OpenAPI 스키마에 예시/설명 추가
    email: Annotated[EmailStr, Field(example="alice@example.com", description="로그인 이메일")]

```

자주 쓰는 파라미터:

- `ge`, `gt`, `le`, `lt`: 숫자 범위
- `min_length`, `max_length`, `pattern`: 문자열 제약
- `default_factory`: 가변 기본값(list/dict) 안전하게 제공
- `description`, `example`: OpenAPI 문서화
- `alias`: 들어오는 키 이름 바꾸기 (예: `userId` → `user_id`)
    - 필요 시 `model_config = ConfigDict(populate_by_name=True)`로 **이름/별칭 모두 허용**

```python
from pydantic import ConfigDict

class InUser(BaseModel):
    model_config = ConfigDict(populate_by_name=True)  # alias로도, 필드명으로도 바인딩
    user_id: Annotated[int, Field(alias="userId")]

```

---

### 6. **`field_validator` — 필드 단위 커스텀 검증**

- 역할: 기본 제약으로 부족할 때 **필드별 추가 검증/정규화** 수행
- 모드:
    - `mode='before'`: **파싱 전에** 실행 (전처리/정규화)
    - `mode='after'`: **파싱 후** 실행 (값 확정 뒤 검증/후처리)

```python
from typing import Annotated
from pydantic import BaseModel, Field, field_validator

class User(BaseModel):
    name: Annotated[str, Field(min_length=1, max_length=30)]
    tags: list[str] = Field(default_factory=list)

    # 입력 전처리: 공백 제거, 빈 문자열 방지
    @field_validator('name', mode='before')
    @classmethod
    def strip_and_require_name(cls, v: str):
        if isinstance(v, str):
            v = v.strip()
        if not v:
            raise ValueError("name은 비어 있을 수 없습니다.")
        return v

    # 파싱 후 처리: 태그 중복 제거(순서 보존)
    @field_validator('tags', mode='after')
    @classmethod
    def dedup_tags(cls, v: list[str]):
        return list(dict.fromkeys(v))

```

팁:

- v2에서는 예전의 `@validator` 대신 **`@field_validator`** 사용.
- 여러 필드 관계 검증(교차 검증)이 필요하면 `@model_validator`(옛 `root_validator`) 사용을 고려.

```python
from pydantic import BaseModel, model_validator

class Period(BaseModel):
    start: int
    end: int

    @model_validator(mode='after')
    def check_range(self):
        if self.start >= self.end:
            raise ValueError("start는 end보다 작아야 합니다.")
        return self

```
