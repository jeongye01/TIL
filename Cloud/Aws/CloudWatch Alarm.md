## 📌 CloudWatch Alarm이란?

CloudWatch Alarm은 **지표(Metric)** 를 감시하다가 사용자가 정의한 **임계값(Threshold)** 을 넘으면 자동으로 알림을 보내거나 액션을 트리거하는 기능이다.

---

## 🚦 상태 (Alarm States)

- **OK**: 정상 상태 (조건 불충족, 트리거 되지 않음)
- **ALARM**: 문제가 발생한 상태 (조건 충족)
- **INSUFFICIENT_DATA**: 데이터 부족으로 판단 불가

---

## ⚙️ 구성 요소

1. **Metric**
    - 모니터링할 지표 (예: Lambda Errors, CPUUtilization, Invocations)
2. **Threshold**
    - 알람을 발생시킬 조건 (예: Errors ≥ 1, CPU ≥ 80%)
3. **Evaluation period**
    - 특정 지표를 평가하는 시간 간격
4. **Datapoints to alarm**
    - 평가 기간 중 조건을 만족해야 하는 횟수 (예: 5분 중 3분 동안 에러 발생)

---

## 🔔 동작 방식

1. CloudWatch가 Metric을 주기적으로 수집
2. 사용자가 정의한 조건을 만족하는지 평가
3. 조건 충족 시 상태를 **ALARM**으로 변경
4. 연결된 **Action** 실행 (SNS 알림, Auto Scaling, Lambda 실행 등)

---

## 📧 활용 예시

- **SNS 알림**: Errors ≥ 1 → 이메일/SMS 발송
- **Auto Scaling**: CPU ≥ 80% → EC2 인스턴스 추가
- **비용 관리**: DynamoDB 쓰기 초과 → 알람 발송

---

## 🧩 Composite Alarm

- 여러 개의 CloudWatch Alarm을 조합해서 **복합 조건**으로 알람을 트리거
- 예시:
    - CPU ≥ 80% **AND** Memory ≥ 90%일 때만 알람
    - 특정 서비스의 Error Rate가 높고 동시에 Latency가 증가했을 때만 알람

👉 불필요한 알람(노이즈)을 줄이고 **정밀한 조건 기반 알림**을 구현할 수 있다.

---

## 🖥️ EC2 Instance Recovery

CloudWatch Alarm과 연결하여 **EC2 인스턴스 자동 복구** 기능을 제공한다.

### 상태 점검

- **Instance Status Check**: EC2 가상 머신의 상태 확인
- **System Status Check**: 인스턴스가 실행되는 **호스트 하드웨어** 상태 확인
- **EBS Status Check**: 연결된 EBS 볼륨 상태 확인

### Recovery 동작

- 알람이 발동되면 **EC2 Instance Recovery** 실행
- 인스턴스가 **다른 호스트로 이동**하면서 자동 복구됨
- 복구 과정에서 **private/public IP, Elastic IP, 메타데이터** 그대로 유지
- 동일한 placement group 설정 가능
- 복구 이벤트는 **SNS 알람**과 연동 가능

👉 즉, 물리적 호스트 장애가 발생해도 EC2가 자동으로 다른 정상 호스트에서 재시작된다.
