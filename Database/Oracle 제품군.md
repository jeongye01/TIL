# 목록

- Oracle
- Oracle RAC
- ASM - (Oracle) Automatic Storage Management
- ExaData
- Data Guard

### (Single) DB

- 서버 1대에서 하나의 DB만 구동되는 경우

단점 : Scale Up만 가능(시스템 성능 한계), 서버 장애 시 서비스 불가

### RAC - (Oracle) **Real Application Clusters**

*클러스터 : 두 개 이상의 독립된 서버들과 Disk를 하나로 연결하는 기법. 사용자가 Cluster로 구성된 서버들 중 어느 서버에 접속해도 동일한 Disk를 엑세스하게 되므로 하나의 서버 또는 하나의 Disk에 연결하는 것처럼 인식

![image](https://github.com/jeongye01/TIL/assets/74299317/9a7a6d92-aca2-4c37-aaf6-99035465cbc5)


- 여러 서버(노드)가 하나의 데이터베이스 인스턴스를 공유하여 동작하는 클러스터 데이터베이스 시스템
- Shared-Disk 기반 기술로 전체 노드가 하나의 동일한 DB를 서비스 함
- 특징
    - 서버의 수평 확장에 따른 CPU 처리 능력 향상(서버 간에 부하를 분산)
    - 고가용성 - DBMS 장애시 가용성 보장(Failover)
- 단점
    - 디스크 속도의 한계
    - Disk 장애 문제(One Point Failure)
    - 서버간 통신 Overhead(Global Lock)

### ASM - (Oracle) Automatic Storage Management

- Oracle 데이터베이스 파일의 저장 관리를 자동화하는 기술
- Disk 관리 시스템으로 Disk 간 Balance가 유지될 수 있도록 분산 저장 및 Mirroring을 지원한다.
- Disk Load Balancing - ASM이 관리하는 모든 Disk에 대해 Load Balancing 작업을 자동으로 처리한다. 그러므로 특정 Disk에 Load가 집중되는 현상을 최소화
- ASM이 관리하는 영역에서 새로운 Disk를 추가/삭제할 경우 기존 Data들에 대해 재구성 작업이 자동으로 발생

![image](https://github.com/jeongye01/TIL/assets/74299317/b8675969-58c8-4822-a4d0-4d2339eb52f6)

### ExaData

- 고성능 데이터베이스 시스템으로, 하드웨어와 소프트웨어가 통합된 솔루션
- OLTP(Online Transaction Processing), 데이터 웨어하우징, 그리고 빅데이터 분석 등에 최적화된 성능을 제공
- RAC 구조에서 I/O 성능과 가용성을 극대화 하기 위해 **스토리지 서버**를 도입
    - 공유디스크 대신 local disk를 가진 서버가 공유 디스크 기능을 수행하도록 함
    

### Data Guard

- 주 데이터베이스에 문제가 발생했을 때 데이터를 복구할 수 있는 기능을 제공
- **데이터 복제 및 동기화**: 주 데이터베이스(Primary Database)의 모든 트랜잭션과 데이터 변경 사항을 하나 이상의 대기 데이터베이스(Standby Database)로 실시간으로 복제하고 동기화
- **자동 장애 전환(Failover) 및 장애 복구(Failback)**: 주 데이터베이스에 장애가 발생하면, Data Guard는 자동으로 대기 데이터베이스 중 하나를 새로운 주 데이터베이스로 승격시켜 서비스를 지속할 수 있게 한다.
- **읽기 전용 쿼리 및 보고**:대기 데이터베이스를 읽기 전용 모드로 설정하여 보고서 생성이나 쿼리 작업에 활용할 수 있다.
