### Uvicorn

### 1. 정의

- **Python용 ASGI 서버**
- FastAPI, Starlette 등 **비동기 웹 프레임워크** 실행에 사용
- WSGI 서버(Gunicorn, uWSGI)의 **비동기 버전** 역할

---

### 2. 역할

1. HTTP 요청 수신
2. 요청을 **ASGI 이벤트**로 변환
3. FastAPI 앱에 전달하여 처리
4. 응답을 HTTP 형식으로 변환 후 클라이언트에 반환

---

### 3. 특징

- **가볍고 빠름**: `uvloop`(고성능 이벤트 루프) + `httptools` 사용
- HTTP/1.1, HTTP/2, WebSocket 지원
- asyncio 기반 비동기 I/O 처리 가능

---

### 4. 실행 예시

```bash

uvicorn main:app --reload
```

- `main:app` → `main.py`의 `app` 객체(FastAPI 인스턴스)
- `-reload` → 코드 변경 시 자동 재시작 (개발 환경에서 사용)

---

### 5. 운영 시

- 단독 실행 가능하지만, 운영 환경에서는 **Gunicorn + UvicornWorker** 조합 자주 사용

```bash

gunicorn -k uvicorn.workers.UvicornWorker main:ap
```

---

**📌 요약**

> Uvicorn은 ASGI 표준을 구현한 고성능 비동기 서버로,
> 
> 
> FastAPI 같은 현대 Python 웹 프레임워크를 실제로 구동하는 핵심 엔진 역할을 한다.
>
