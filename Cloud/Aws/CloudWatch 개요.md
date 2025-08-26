## 1. 개념

- AWS 리소스와 애플리케이션에서 나오는 **로그, 지표, 이벤트**를 수집하고 모니터링하는 서비스
- EC2, Lambda, RDS, DynamoDB 같은 서비스들의 상태와 성능을 확인할 수 있음
- 알람을 걸어서 이상 징후를 감지하거나 자동 대응(예: Auto Scaling) 가능

## 2. 주요 기능

1. **CloudWatch Metrics (지표)**
    - 모니터링할 변수
        - 예시
            - EC2 : CPUUtilization, NetworIn
            - S3: 버킷 크기
    - CPU 사용량, 메모리, 디스크 I/O, 네트워크 트래픽 등 리소스 상태 모니터링
    - Dimension : 지표 측정 기준
    - 커스텀 메트릭도 애플리케이션에서 전송 가능
    - 지표는 외부로 전송 될 수 있음
        - Datadog, Dynatrace New Relic Splunk, Sumo Logic 같은 타사 서비스들
2. **CloudWatch Logs (로그)**
    - 애플리케이션 로그, Lambda 로그, API Gateway 로그 등을 중앙에서 수집/검색
    - 로그 기반 지표 생성도 가능
3. **CloudWatch Alarms (알람)**
    - 특정 임계치(CPU > 80%) 초과 시 알람 발생
    - SNS, 이메일, Slack 같은 채널로 알림 전송 가능
    - Auto Scaling, Lambda 실행 같은 자동 액션도 가능
4. **CloudWatch Events / EventBridge**
    - 일정 주기(스케줄링) 혹은 이벤트 발생 시 자동으로 다른 서비스 트리거
    - 예: 매일 자정마다 Lambda 실행
5. **CloudWatch Dashboards**
    - 여러 메트릭과 로그를 시각화해서 모니터링 대시보드 구성 가능

---

## 3. 사용 예시

- EC2 CPU 사용률 80% 넘으면 알람 발송 + Auto Scaling 실행
- Lambda 로그를 자동으로 수집해서 에러율 모니터링
- RDS 쿼리 지연 시간 추적
- S3에 데이터가 업로드될 때 이벤트 발생 → CloudWatch Events → Lambda 트리거

---

## ✅ 요약

👉 **AWS CloudWatch = AWS 전용 모니터링/로깅 서비스**

- **Metrics** (지표 모니터링)
- **Logs** (로그 수집/분석)
- **Alarms** (알림/자동 대응)
- **Events** (스케줄/트리거)
- **Dashboards** (시각화)
