### 1. **`response_model`이란?**

- FastAPI 엔드포인트의 **응답(Response) 데이터 구조**를 **Pydantic 모델**로 정의하는 옵션
- 기능:
    1. **응답 데이터 검증** — 반환 데이터가 정의한 스키마에 맞는지 검사
    2. **직렬화(Serialization)** — Python 객체 → JSON 변환 시 스키마에 맞게 변환/필터링
    3. **문서화 자동화** — OpenAPI(`/docs`)에 응답 구조 표시

---

### 2. **사용 예시**

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    id: int
    name: str

@app.get("/users/{user_id}", response_model=User)
async def get_user(user_id: int):
    return {"id": user_id, "name": "Alice", "extra_field": "ignored"}

```

- `extra_field`는 스키마에 없으므로 응답에서 제외됨
- 반환 값이 스키마에 맞지 않으면 FastAPI가 에러 발생

---

### 3. **자주 쓰는 옵션**

| 옵션 | 설명 |
| --- | --- |
| `response_model_exclude_unset=True` | 기본값만 있는 필드 제외 |
| `response_model_exclude_defaults=True` | 기본값 필드 제외 |
| `response_model_exclude_none=True` | `None` 값 필드 제외 |

---

### 4. **장점**

- **데이터 무결성 보장**: 의도치 않은 필드나 잘못된 형식의 데이터 방지
- **자동 문서화**: API 응답 구조를 Swagger UI에 자동 반영
- **보안성 향상**: 내부 정보 노출 방지(스키마 외 필드 제거)

### 5. FastAPI의 자동 JSON 응답 변환

- **자동 변환 대상:** 경로 작동 함수에서 반환되는 아래 타입의 데이터.
    - Python `dict`, `list`
    - Pydantic `BaseModel` 객체
    - JSON 변환 가능 기본 타입 (`str`, `int`, `float`, `bool` 등)
- **처리 과정:**
    - FastAPI가 위 데이터를 **자동으로 JSON 형식으로 변환**.
    - `Content-Type: application/json` 헤더 설정.
    - `JSONResponse` 객체로 만들어 클라이언트에 전송.
