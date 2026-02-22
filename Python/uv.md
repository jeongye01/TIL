## 1. uv 한 줄 정의

uv는

> Python 패키지 설치 + 가상환경 관리 + 의존성 해결을
> 
> 
> 초고속으로 수행하는 Rust 기반 도구다.
> 

pip + venv를 대체하는 차세대 패키지 매니저라고 보면 된다.

---

## 2. 왜 uv가 등장했는가?

기존 문제점:

- pip 느림
- Poetry install 느림
- Docker build 시간 증가
- CI에서 dependency resolution 오래 걸림

uv의 목표:

> Python 패키지 설치를 npm 수준 속도로 만들자.
> 

---

## 3. uv가 해결하는 문제

### 1) 설치 속도 문제

Rust로 작성되어

패키지 다운로드 + dependency resolution 속도가 매우 빠름.

Docker build / CI 환경에서 특히 체감됨.

---

### 2) 의존성 해결 (Resolver)

uv는 단순 설치가 아니라:

- 의존성 그래프 계산
- 충돌 탐지
- 호환 가능한 버전 자동 선택
- lock 파일 생성

을 수행한다.

---

### 3) 가상환경 관리

```
uv venv
```

→ 빠르게 가상환경 생성

```
uv run python main.py
```

→ 해당 환경에서 실행

venv 생성 + 활성화 + 실행을 단순화함.

---

## 4. 기본 사용 예시

### 패키지 설치

```
uv pip install fastapi
```

또는

```
uv add fastapi
```

---

### lock 기반 설치

```
uv sync
```

→ lock 파일 기준으로 정확한 버전 설치

### uv run

가상환경 활성화 없이, uv가 관리하는 Python 환경에서 바로 명령을 실행하는 커맨드.

```
# Python 스크립트 실행
uv run python script.py

# 모듈 실행
uv run python -m pytest

# CLI 도구 직접 실행
uv run pytest
uv run flask run
uv run uvicorn main:app --reload
```

---

## 5. uv vs Poetry

| 항목 | Poetry | uv |
| --- | --- | --- |
| 작성 언어 | Python | Rust |
| 속도 | 상대적으로 느림 | 매우 빠름 |
| 프로젝트 메타정보 | 강함 | 약함 |
| 빌드 시스템 | 포함 | 없음 |
| 패키지 배포 | 가능 | 별도 필요 |
| 목적 | 프로젝트 관리 플랫폼 | 고속 설치 엔진 |

---

## 6. 기존 방식과의 비교

| 역할 | 기존 | uv |
| --- | --- | --- |
| Python 버전 관리 | pyenv | `uv python install 3.12` |
| 가상환경 생성 | venv, virtualenv | `uv venv` |
| 패키지 설치 | pip | `uv pip install`, `uv add` |
| 의존성 잠금 | pip-freeze, pip-tools | `uv lock` (uv.lock 자동 생성) |
| 프로젝트 초기화 | 수동 세팅 | `uv init` |
| 스크립트 실행 | activate + python | `uv run` |
