## 1️⃣ CloudWatch Agent

- **EC2, 온프레미스 서버, 컨테이너 환경에서 실행되는 소프트웨어 에이전트**
- 서버의 **로그**와 **메트릭**(CPU, 메모리, 디스크, 네트워크 등)을 수집해서 **Amazon CloudWatch 서비스로 전송**
- 기본적으로 설정이 없으면 EC2에서 CloudWatch로 옮겨지지 않음

---

## 2️⃣ 역할

- **시스템 메트릭 수집**
    - CPU 사용률, 메모리 사용량, 디스크 I/O, 네트워크 트래픽 등
- **로그 수집**
    - `/var/log/messages`, `/var/log/nginx/access.log` 같은 애플리케이션 로그를 CloudWatch Logs로 전송
- **커스텀 메트릭 수집**
    - 예: 애플리케이션 상태, 특정 파일 크기, 사용자 정의 지표

---

## 3️⃣ 설치 위치

- **Amazon EC2 인스턴스** (리눅스/윈도우)
- **온프레미스 서버**
- **컨테이너 환경** (도커/쿠버네티스)

---

## 4️⃣ 설정 방식

- JSON 설정 파일 (`amazon-cloudwatch-agent.json`) 을 작성해서 어떤 메트릭/로그를 수집할지 정의
- AWS Systems Manager (SSM)과 연동해서 중앙 관리도 가능

---

## 5️⃣ 예시

```json
{
  "metrics": {
    "append_dimensions": {
      "InstanceId": "${aws:InstanceId}"
    },
    "metrics_collected": {
      "cpu": {
        "measurement": ["usage_idle", "usage_iowait"],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": ["mem_used_percent"],
        "metrics_collection_interval": 60
      }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/nginx/access.log",
            "log_group_name": "nginx-access-logs",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}

```
