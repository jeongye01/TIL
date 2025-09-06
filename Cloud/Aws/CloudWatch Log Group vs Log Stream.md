# CloudWatch Log Group vs Log Stream

## 1️⃣ Log Group

- **로그들의 논리적 묶음 (컨테이너)**
- 같은 목적/서비스에 속하는 로그들을 모아두는 단위
- 예: Lambda 함수 하나마다 기본적으로 1개의 Log Group이 생성됨
    - 이름 예시:
        
        ```
        /aws/lambda/gdelt-parser
        /aws/lambda/gdelt-downloader
        
        ```
        

👉 **Log Group = 서비스/애플리케이션 단위로 묶은 로그 폴더**

---

## 2️⃣ Log Stream

- **Log Group 안의 개별 로그 시퀀스 (파일 단위)**
- 시간 순서대로 이벤트 로그가 기록됨
- AWS Lambda의 경우, **컨테이너 인스턴스 단위**로 새로운 Stream이 생성됨
    - 즉, Lambda가 scale-out 되거나 재시작될 때마다 새로운 Log Stream이 생김
- 이름 예시 (자동 생성됨):
    
    ```
    2025/09/06/[$LATEST]abc123def456
    2025/09/06/[$LATEST]xyz789uvw000
    
    ```
    

👉 **Log Stream = 실제 로그 이벤트가 저장되는 개별 파일/세션**

---

## 3️⃣ 예시 구조

```
Log Group: /aws/lambda/gdelt-parser
 ├── Log Stream: 2025/09/06/[$LATEST]abc123...
 ├── Log Stream: 2025/09/06/[$LATEST]def456...
 └── Log Stream: 2025/09/06/[$LATEST]ghi789...

```

- **Log Group**: Lambda 함수 단위
- **Log Stream**: 그 함수 실행 컨테이너 단위의 로그 기록

---

## ✅ 요약

- **Log Group** = 로그들의 상위 묶음 (서비스 단위)
- **Log Stream** = 실제 로그가 기록되는 개별 세션/파일

---
