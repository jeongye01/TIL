## 1. TOML이란?

TOML은 **Tom’s Obvious, Minimal Language**의 약자다.

설정(configuration) 파일을 작성하기 위한 포맷으로,

사람이 읽기 쉽고 명확하게 구조화된 문법을 가진다.

주로 프로젝트 설정 관리에 사용된다.

---

## 2. 왜 TOML을 쓰는가?

기존 설정 파일 포맷의 문제점:

- JSON → 주석 불가
- YAML → 들여쓰기 오류에 취약
- INI → 구조 표현이 제한적

TOML의 장점:

- 직관적인 문법을 가지고 있다.
- 문자열, 숫자, 불리언, 배열, 날짜 등 다양한 자료형 지원
- 중첩 구조 표현이 직관적
- 주석 지원

---

## 3. 기본 문법

### 1) 키-값

```
name = "yewon"
age = 26
is_developer = true
```

---

### 2) 배열

```
skills = ["python", "react", "docker"]
```

---

### 3) 테이블 (섹션)

```
[database]
host = "localhost"
port = 5432
```

---

### 4) 중첩 테이블

```
[server]
[server.production]
url = "https://example.com"
```

---

## 4. Python에서 TOML은 어디에 쓰이나?

### 1) Poetry

```
# pyproject.toml

[tool.poetry]
name = "my-project"
version = "0.1.0"
description = "My Python project"

[tool.poetry.dependencies]
python = "^3.11"
fastapi = "^0.110.0"
```

Poetry는:

- 패키지 의존성 관리
- 프로젝트 메타 정보 관리
- 빌드 설정 관리

을 전부 `pyproject.toml` 하나로 처리한다.

---

.toml 확장자는 특정 언어에 한정된 것이 아니라, **범용 설정 파일 포맷**이다. Python 프로젝트에서 자주 보이지만, Rust나 다른 도구에서도 널리 사용된다.

다양한 언어에서의 활용

- **Pythonpyproject.toml** 파일을 통해 프로젝트 메타데이터, 빌드 시스템, 의존성 등을 관리한다. Poetry, Black, Ruff 같은 도구들이 TOML을 채택하고 있다.
- **Rust** Rust 프로젝트의 핵심은 **Cargo.toml**이다. 패키지 이름, 버전, 의존성 등이 이 파일에 정의된다.
- **Go, Node.js, Hugo 등** 일부 빌드 도구, 정적 사이트 생성기, 서버 애플리케이션에서도 TOML을 설정 파일 포맷으로 활용한다.
