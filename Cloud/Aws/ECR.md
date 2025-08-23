## 개념

- ECR (Elastic Container Registry)
- **AWS에서 제공하는 Docker 이미지 저장소(Registry)**
- public , private 옵션 있음
- 이미지의 취약점 스캐닝, 버저닝 태그 및 수명 주기 확인을 지원함.

Docker 이미지를 올려두고, EC2, ECS, EKS, Lambda 같은 AWS 서비스들이 그 이미지를 가져다 쓸 수 있게 해주는 서비스.

---

## 특징

- **완전관리형(Managed)**: 직접 레지스트리 서버 운영 필요 없음
- **보안 통합**: IAM 권한 관리, 이미지 스캔 기능 제공
- **리전 단위**: 각 AWS 리전에 따로 존재
- **비용**: 저장 용량 + 데이터 전송량 기준 과금

---

## 주요 사용 사례

1. **Lambda 컨테이너 배포**
    - pandas 같은 패키지 넣어서 만든 Docker 이미지를 ECR에 올림
    - Lambda 함수에서 ECR 이미지를 실행
2. **ECS / EKS 배포**
    - 마이크로서비스 컨테이너 이미지를 ECR에 보관 후 ECS/EKS 클러스터에서 실행
3. **CI/CD 파이프라인**
    - GitHub Actions, Jenkins 같은 CI 도구에서 빌드한 Docker 이미지를 ECR에 push → 자동 배포

---

## 동작 흐름 (간단 요약)

1. 로컬에서 Docker 이미지 빌드
    
    ```bash
    docker build -t myimage .
    ```
    
2. AWS CLI 로그인 (ECR 인증)
    
    ```bash
    aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-northeast-2.amazonaws.com
    ```
    
3. 태그 후 push
    
    ```bash
    docker tag myimage:latest <account-id>.dkr.ecr.ap-northeast-2.amazonaws.com/my-repo:latest
    docker push <account-id>.dkr.ecr.ap-northeast-2.amazonaws.com/my-repo:latest
    ```
    
4. ECS, Lambda 등에서 ECR 이미지 참조해서 실행
