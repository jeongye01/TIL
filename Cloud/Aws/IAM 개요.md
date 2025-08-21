# IAM (Identity and Access Management)

## 개요

- AWS 리소스에 대한 접근을 **인증(Authentication)과 권한 부여(Authorization)** 로 관리하는 서비스
- **루트 사용자**
    - 계정 생성 시 만들어지는 최초 사용자
    - **청구, 계정 관리 등 일부 작업은 루트 사용자만 가능**
    - **일상적 운영에는 사용하지 않고 IAM 사용자/역할을 만들어 사용**하는 게 보안 모범 사례

## 사용자와 그룹

- **사용자(User)**: 한 사람이나 애플리케이션을 대표하는 개별 계정
- **그룹(Group)**: 여러 사용자에게 공통 권한을 부여하기 위한 묶음

# IAM 정책

## IAM 정책 (Policy)

- 사용자, 그룹, 역할(Role)에 **권한(permissions)** 을 부여하는 방법
- **JSON 형식**으로 작성 (`Effect`, `Action`, `Resource`, `Condition` 구조)
- 기본적으로 모든 권한은 **명시적으로 허용(Allow)해야 함**
    - 즉, **기본은 Deny**
    - “명시적 Deny”가 있으면 Allow보다 우선 적용
- 그룹 레벨에 연결된 정책은 모든 구성원에게 적용됨
- 인라인 정책
    - 특정 사용자에게 적용되는 정책

### 정책 구조 요소

```json
{
   "Version": "2012-10-17",
   "Id": "S3-Account-Permissions", // 선택 사항
   "Statement":[
      {
         "Sid": "1", // statement 식별, 선택 사항
         "Effect": "Allow", // 접근 허용 여부, 거부 -> Deny
         "Principal":{ // 정책 적용 범위 (사용자,계정 , 혹은 역할)(버킷 정책 등 리소스 기반 정책에서만 쓰인)
            "AWS": ["arn:aws:iam::123456789012:root"]
         },
         "Action":[ // 허용 혹은 거부 될 API 목록
            "s3:GetObject",
            "s3:PutObject"
         ],
         "Resource":["arn:aws:s3:::mybucket/*"] // 리소스 
      }
   ]
}
```


# IAM Role

- **AWS 리소스나 사용자에게 일시적으로 권한을 위임하기 위한 “임시 권한 세트**

## User vs Role 비교

| 구분 | IAM User | IAM Role |
| --- | --- | --- |
| 자격 증명 | Access Key / Password 가 있음 | 없음 (필요 시 STS로 임시 토큰 발급) |
| 사용 방식 | 특정 사용자에게 직접 로그인/자격 증명 부여 | 다른 사용자/서비스가 **AssumeRole** 해서 사용 |
| 주 사용처 | 사람(개발자, 운영자) 계정 | 서비스/애플리케이션 권한 위임, 임시 권한 부여 |
| 보안 위험 | 장기 키 유출 가능성 있음 | 단기 세션, 만료되면 무효 → 보안에 더 안전 |
