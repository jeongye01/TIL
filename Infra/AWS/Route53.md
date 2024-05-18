# Route53

- **도메인 구입 및 관리**
    
    <img width="669" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/2e766659-2ac5-4103-be2e-d072a750b195">
    <img width="677" alt="image" src="https://github.com/jeongye01/TIL/assets/74299317/f6b0cd02-021c-4b79-8994-276f28162f6f">

    
- **도메인 이름을 IP 주소로 변환하는 DNS 기능을 제공**
- **트래픽 관리: Route 53은 지리적 라우팅, 지연 시간 기반 라우팅, 가중치 기반 라우팅 등 다양한 트래픽 라우팅 정책을 지원하여 사용자 트래픽을 최적의 리소스로 유도**
- **헬스 체크 및 모니터링:Route 53은 헬스 체크 기능을 제공하여 서버의 상태를 모니터링하고, 문제가 발생하면 자동으로 트래픽을 다른 건강한 리소스로 리다이렉트할 수 있음**

### 도메인 Routing Policy

1. NS- 네임서버
2. SOA - Start of Authority
3. TXT - text → 도메인 권한 확인할 때 많이 사용
4. A - Address
    1. IP 주소 입력해서 routing
    2. CloudFront,ALB,API Gateway 사용 시 alias 활용 가능
5. CNAME - Canonical Name
    1. 도메인 이름을 활용해서 routing
6. MX - Mail Exchange
    
    a. 이메일 활용
