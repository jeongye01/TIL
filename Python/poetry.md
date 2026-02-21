## 1. Poetry 란

**Python 프로젝트의 의존성 관리 + 가상환경 관리 + 빌드 관리 도구**다.

`pyproject.toml` 파일을 사용하여 프로젝트 설정과 의존성을 선언적으로 관리 한다.

---

## 2. Poetry가 해주는 것

### 1) 가상환경 자동 생성

- `.venv` 생성 (또는 Poetry 캐시 위치에 생성)
- 해당 프로젝트에 자동 연결
- `poetry run python` 하면 그 환경의 Python 실행

venv 생성 + activate + 관리 를 자동화

### 2) 의존성 관리

```
poetry add fastapi
poetry add sqlalchemy
```

**내부 동작**

Poetry는:

1. fastapi가 요구하는 하위 의존성 확인
2. sqlalchemy가 요구하는 하위 의존성 확인
3. 겹치는 라이브러리의 호환 가능한 버전 계산
4. 충돌 나면 에러

예:

- A는 `anyio >=3`
- B는 `anyio <4`

→ 3.x 중에서 가능한 최신 버전 선택 (Resolver)

### 3) lock 기반 환경 재현

`pyproject.toml`:

```toml
fastapi = "^0.110.0"
```

실제 설치는 `poetry.lock`에 고정됨:

```
fastapi 0.110.2
starlette 0.36.3
anyio 4.2.0
```

### 4) 프로젝트 메타정보 관리

`pyproject.toml` 안에서 프로젝트 자체의 정보를 정의함.

```bash
[tool.poetry]
name = "rag-ux-system"
version = "0.1.0"
description = "RAG 기반 UX 리서치 도구"
authors = ["Yewon <email@example.com>"]
license = "MIT"
readme = "README.md"
```

### 5) 프로젝트 빌드 시스템

Poetry는 `setup.py`를 대체함.

```bash
poetry build
```

### 6) 패키지 배포 관리

```
poetry publish
```

또는:

```
poetry publish--build
```

→ 빌드 후 PyPI 업로드
