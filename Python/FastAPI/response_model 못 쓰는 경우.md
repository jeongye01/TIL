## 1. `response_model`의 역할

- 엔드포인트가 Python 객체(`dict`, `list`, `BaseModel`)를 반환하면:
    1. **Pydantic 검증** (타입/스키마 확인)
    2. **JSON 직렬화**
    3. OpenAPI 문서 자동 반영

👉 따라서 정상 반환(해피 케이스)에서 데이터 일관성과 문서화 보장.

---

## 2. Response 객체를 직접 반환할 때

- `Response`, `JSONResponse`, `RedirectResponse` 등 **이미 완성된 응답**을 반환하면
    
    → FastAPI는 더 이상 `response_model` 검증을 하지 않음.
    

👉 즉, `response_model`은 무시됨.

---

## 3. 언제 Response 직접 반환이 필요한가?

- 커스텀 HTTP 헤더 추가
- 쿠키 설정
- 파일 다운로드 / 스트리밍
- 리디렉션 처리
- 상태 코드 및 본문을 세밀하게 제어해야 할 때

---

## 4. 일반적인 패턴

```python
@app.get("/users/{user_id}", response_model=User)
async def get_user(user_id: int):
    if user_id == 1:
        return User(id=1, name="Alice")  # ✅ response_model 검증 적용
    elif user_id == 2:
        return Response(content="No permission", status_code=403)  # ⚠️ response_model 무시
    else:
        raise HTTPException(status_code=404, detail="User not found")  # 권장 패턴

```

---

## 5. 정리

- `response_model` → **성공 케이스(해피 케이스)** 검증 & 문서화 용도로 활용
- 예외/에러/특수 케이스 → `HTTPException` 또는 `Response` 직접 반환
- FastAPI 설계 철학상 `response_model`은 **"성공 응답 스키마" 보장**에 집중
