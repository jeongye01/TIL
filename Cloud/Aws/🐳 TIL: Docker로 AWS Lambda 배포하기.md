- AWS Lambda는 docker 컨테이너 이미지를 배포 모델로 지원한다.

## 1. 왜 Docker로 배포하나?

- `pandas`, `pyarrow` 같은 **대형/네이티브 패키지**는 ZIP 용량/호환성 한계 존재
    - **.zip 파일 업로드 (콘솔/CLI 직접)**: 50MB (압축된 상태)
    - **.zip 파일 압축 해제 후 크기**: 250MB (모든 코드+라이브러리 합산)
    - **컨테이너 이미지**: 최대 10GB (비압축, ECR에 저장)
- 👉 **컨테이너 이미지**로 패키징해서 AWS ECR에 올린 뒤 Lambda에서 실행
- 용량 문제 외에도, 도커는 **환경 일관성**을 보장하는 중요한 이점이 있다.

---

## 2. Dockerfile 작성

```docker
FROM public.ecr.aws/lambda/python:3.10

# 의존성 설치
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# 핸들러 복사
COPY handler.py ./

# Lambda 진입점
CMD ["handler.lambda_handler"]

```

- `FROM` : Lambda 공식 Python 3.10 베이스 이미지
- `requirements.txt` : pandas, boto3, pyarrow 등 의존성 정의
- `CMD` : Lambda에서 실행할 함수 (모듈.함수명)
- 명령어 자세히 보기
    
    ### 1. `FROM`
    
    ```docker
    FROM public.ecr.aws/lambda/python:3.10
    
    ```
    
    - 베이스 이미지를 지정
    - 여기서는 AWS Lambda 공식 Python 3.10 런타임 이미지 기반
    
    ---
    
    ### 2. `COPY`
    
    ```docker
    COPY requirements.txt ./
    
    ```
    
    - 로컬 파일을 컨테이너 내부로 복사
    - `requirements.txt`를 컨테이너의 현재 경로(`./`)로 복사
    
    ```docker
    COPY handler.py ./
    
    ```
    
    - `handler.py` 파일을 컨테이너 내부에 복사
    
    ---
    
    ### 3. `RUN`
    
    ```docker
    RUN pip install --no-cache-dir -r requirements.txt
    
    ```
    
    - 컨테이너 빌드 과정에서 실행할 명령어
    - 여기서는 Python 패키지 의존성 설치
    
    ---
    
    ### 4. `CMD`
    
    ```docker
    CMD ["handler.lambda_handler"]
    
    ```
    
    - 컨테이너 실행 시 기본으로 실행할 명령어 지정
    - AWS Lambda 컨테이너 이미지에서는 **핸들러 위치** (`파일.함수명`)을 지정
    
    ---
    
    ## 요약
    
    - `FROM`: 어떤 환경에서 시작할지 정한다
    - `COPY`: 로컬 → 컨테이너로 파일 복사
    - `RUN`: 컨테이너 빌드 중 실행할 명령어
    - `CMD`: 컨테이너 실행 시 기본 실행 명령어

---

## 3. 도커 빌드 & 실행

```bash
docker build -t lambda-app .
```

- 현재 디렉토리(`.`)에 있는 Dockerfile로 이미지를 빌드
- `t lambda-app` : 빌드된 이미지에 `lambda-app` 이라는 이름(태그)을 붙임

로컬 테스트:

```bash
docker run -it lambda-app
```

- `lambda-app` 이미지를 기반으로 컨테이너 실행
- `it` :
    - `i` → interactive (표준 입력 유지)
    - `t` → 터미널 할당
- 즉, 실행된 컨테이너 안에서 직접 명령어를 입력할 수 있게 됨

---

## 4. AWS ECR (Elastic Container Registry) 푸시

1. **ECR 리포지토리 생성**
2. **ECR 로그인**
    
    ```bash
    aws ecr get-login-password --region ap-northeast-2 \
    | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com
    
    ```
    
3. **이미지 태깅**
    
    ```bash
    docker tag lambda-app:latest <ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com/lambda-app:latest
    ```
    
4. **이미지 푸시**
    
    ```bash
    docker push <ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com/lambda-app:latest
    ```
    

---

## 5. Lambda에서 컨테이너 이미지 연결

- 함수 생성 시 **컨테이너 이미지** 선택
- `ECR 이미지 URI` 입력
- Lambda 실행 역할(IAM Role)에 **S3 권한** 등 필요한 권한 부여

---

## 6. 트러블슈팅 (삽질 메모 🛠️)

- ❌ **버킷 이름 Invalid** → 공백 제거 필수
- ❌ **타임아웃/메모리 부족** → Lambda 메모리·타임아웃 늘려서 해결
