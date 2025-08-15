> Starlette는 ASGI(Asynchronous Server Gateway Interface) 표준을 구현한, 가볍고 고성능인 웹 프레임워크로,
> 
> 
> 라우팅, 미들웨어, 요청/응답 객체, 세션, WebSocket, 백그라운드 작업 같은 **웹 서버 핵심 기능**을 제공.
> 

---

## 📦 FastAPI와 관계

- **FastAPI = Starlette + Pydantic**
    - **Starlette**: HTTP/WebSocket 처리, 라우팅, 미들웨어, 세션, ASGI 이벤트 루프 관리
    - **Pydantic**: 요청/응답 데이터 검증, 직렬화·역직렬화
- FastAPI가 Starlette를 **상속**해서 만든 거라, FastAPI 앱은 Starlette 기능을 그대로 사용 가능

---

## 🛠 Starlette의 주요 기능

1. **ASGI 지원** → `uvicorn` 같은 ASGI 서버와 함께 사용
2. **라우팅 시스템** → URL 경로와 핸들러 함수 매핑
3. **미들웨어** → 요청/응답 전후 공통 로직 실행
4. **요청·응답 객체** → `Request`, `Response` 클래스 제공
5. **WebSocket 지원** → 실시간 양방향 통신
6. **백그라운드 작업** → 응답 후 실행되는 비동기 태스크
7. **세션/쿠키 관리**
8. **테스트 클라이언트** → 내장 `TestClient`로 단위 테스트 작성 가능

---

## 🔍 간단 예시

```python
from starlette.applications import Starlette
from starlette.responses import JSONResponse
from starlette.routing import Route

async def homepage(request):
    return JSONResponse({'hello': 'world'})

routes = [
    Route("/", homepage)
]

app = Starlette(debug=True, routes=routes)

```

- `uvicorn main:app --reload`로 실행 가능
- 위 코드는 FastAPI 없이 Starlette만 사용한 예시

---

> ASGI는 **Python 진영의 비동기 웹서버·프레임워크를 연결하는 표준 규약
ASGI 덕분에 FastAPI 같은 프레임워크가 uvicorn 같은 서버를 바로 쓸 수 있는 것,**
>
