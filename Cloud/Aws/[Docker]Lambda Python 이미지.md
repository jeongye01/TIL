# 일반 파이썬 이미지와 람다용 파이썬 이미지의 차이?

## 📌 공통점

- 둘 다 Python 3.10 런타임 환경이 들어있음
- 둘 다 `pip install` 해서 라이브러리 추가 가능

---

## 📌 차이점

### 1. 실행 환경 (Entrypoint)

- **일반 Python 이미지 (`python:3.10`)**
    - 단순히 Python 인터프리터가 들어있음
    - `CMD ["python", "app.py"]` 같은 식으로 실행해야 함
- **Lambda Python 이미지 (`lambda/python:3.10`)**
    - AWS Lambda가 기대하는 실행 환경이 이미 세팅되어 있음
    - 기본 Entrypoint가 `lambda-entrypoint.sh` → `bootstrap` → Lambda 핸들러 호출
    - `CMD ["app.lambda_handler"]` 이렇게 쓰면 자동으로 Lambda 형식으로 실행됨

👉 즉, 일반 Python 이미지에서는 "직접 Python 실행", Lambda 이미지에서는 "Lambda 핸들러 호출" 구조가 내장돼 있음

---

### 2. 파일 시스템/제약 조건 반영

- **일반 Python 이미지**
    - Ubuntu/Debian 기반으로, 패키지가 다양하게 깔려 있음
    - 로컬 서버 개발용에 최적화
- **Lambda Python 이미지**
    - Lambda 실행 환경과 동일하게 경량화
    - `/var/task`, `/var/runtime`, `/tmp (512MB~10GB)` 등 Lambda의 특수 파일시스템 구조가 그대로 반영됨

---

## 📌 정리

- `python:3.10` = 그냥 Python 환경 (서버, 배치, 개발용)
- `lambda/python:3.10` = Lambda가 요구하는 실행환경까지 포함 (Entrypoint, 파일시스템, 핸들러 구조 내장)

👉 **Lambda 전용 이미지**는 사실상 "AWS가 쓰는 Lambda 런타임 그대로 가져와서, 내 코드랑 라이브러리만 얹는 버전"
