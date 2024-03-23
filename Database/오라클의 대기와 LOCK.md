SQL의 처리를 튜닝한다는 관점에서는 'Non-Idle 대기 + SQL 처리에 걸리는 CPU 시간'이 SQL을 처리하는 시간이다.

Non-Idle 대기 이벤트 중 가장 유명한 것은 Lock이다. 
Lock의 본질은 다중 처리를 구현하기 위해 데이터를 보호한다는 것이다. (고객 A가 데이터를 변경하고 있는 동안에는 고객 A 이외에는 해당 데이터를 변경할 수 없도록 변경하는 데이터에 Lock을 걸어서 보호해야만 한다.)
일반적인 대기 개선 방법은 대기하고 있는 원인을 제거하거나,
Lock을 획득하는 횟수를 줄여 전체 시간을 최소화 하는 형태를 고려해볼만하다. 
또한 Lock이 결러있는 시간을 줄이거나, Lock을 걸고 있는 프로세스의 처리가 진행되지 않는 이유를 조사하고 원인을 제거할 필요가 있다. 


SQL에서는 다음과 같이 SELECT 단계에서 Lock을 거는거도 가능하다.

```sql
SELECT counter FROM counter_table where id = 1
FOR UPDATE; <- LOCK이 걸린다.
UPDATE counter_table SET counter = <새로운 값> WHERE id = 1;
```

첫 SELECT에 의해 id가 1인 로우에 LOCK이 걸린다. 로우 Lock이 걸렸기 때문에 동일한 로우를 대상으로 UPDATE,DELETE,SELECT FORM UPDATE 문을 수행할 수 없어 기다려야 한다. 로우 LOCK은 LOCK을 건 세션에서 커밋 또는 롤백에 수행될 때 해제 된다.

한 개의 UPDATE 문으로도 값을 1을 늘린다 라는 작업은 충분히 처리할 수 있다.

```sql
UPDATE counter_table SET counter = counter + 1 WHERE id = 1;
```

UPDATE문 등의 DML은 자동으로 로우 Lock을 건다.

대량의 DML을 수행하면 LOCK대기가 발생한다. LOCK 대기는 데이터를 보호하기 위해 발생할 수 밖에 없으며, 오라클에서는 이를 대처할 만한 개선 방안이 없으므로 대량의 DML을 실행해야 할  때는 어플리케이션에서 최대한 이런 부분을 감안하고 작업을 개선할 수 밖에 없다

# 대기(Lock 대기 x)

- Idle 대기 : 처리할 것이 없어서 쉬고 있는 대기.일반적으로 성능을 분석할 때는 신경을 쓰지 않아도 된다.
- Non-Idle 대기 : 이유가 있어 어쩔수 없이 하는 대기, 이상 상태 등 쓸데 없이 SQL을 기다리게 하는 대기

### 문제의 Non-Idle 대기

- 이유가 있어 어쩔수 없이 하는 대기
    
    이유가 있어서 어쩔 수 없이 기다리는 정상적인 예로 **디스크 I/O 대기**가 있다. 이것은 SQL 처리에 필요한 대기라 할 수 있다.
    

- 이상 상태 등 쓸데없이 SQL을 기다리게 하는 대기
    - 한 사용자가 어떤 테이블에 Lock을 걸어 버린 후에 식사하러 갔다
    - 평상시에는 I/O 처리에 10밀리초가 걸리지만 디스크 장애 등으로 인해 100밀리초가 걸림

# Lock 대기

Lock을 걸었다는 것 자체만으로는 대기가 발생하지는 않으며, Lock이 걸려있는 대상에 다시 Lock을 걸려고 했을때 대기가 발생한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/9eb05db2-f357-4747-81c4-4d15f86e4784)


‘LOCK TYPE’은 Lock의 종류를 의미한다. 

자주 볼 수 있는 Lock은 ‘TX’와 ‘TM’이다.

- TX : 로우와 관련된 Lock
- TM : 테이블에 거는 Lock

# Deadlock의 구조

Deadlock은 서로가 상대방이 보유하고 있는 Lock을 기다리느라 영원히 작업 처리를 진행할 수 없는 상태를 말한다.

![image](https://github.com/jeongye01/TIL/assets/74299317/52e379fb-6112-4cbb-9731-f468d6ce7b49)


# Latch의 구조

![image](https://github.com/jeongye01/TIL/assets/74299317/ceee9098-9b80-4e70-9e25-69c882b2e643)


Latch도 다중 처리를 구현하기 위한 Lock이다. (오라클 내부의 중요한 것, 특히 메모리를 보호하기 위해 존재한다.)일반적인 Lock과 다른 부분은 Latch는 오라클 내부에서 자동으로 얻으며, SQL을 한 번 실행하기 위해서는 여러 Latch를 얻고 해제하는 것을 반복한다는 것이다. 

Latch 경합을 많은 시스템에서 볼 수 있다.

![image](https://github.com/jeongye01/TIL/assets/74299317/6c405c22-38dc-4e8d-963e-cd54aa6d524a)

![image](https://github.com/jeongye01/TIL/assets/74299317/776fa71b-a699-4823-891d-38e817c13fad)


바람직하지 않은 대기와 Lock경합이 격렬하게 발생하는 상황은 오라클에 국한되지 않고 OS등 IT의 여러 분야에서 많이 볼 수 있다. 테스트나 운영할 때 이런 Latch 경합이 보인다면 우선은 CPU 대기가 발생하지 않도록 개선을 시도 할 수 있다. 

대형 시스템이 아닌데 Latch 경합이 심하다면 CPU 자원이 부족하거나 페이징이 발생하는 등의 바람직하지 않은 상태인지를 확인해볼 필요가 있다.

![image](https://github.com/jeongye01/TIL/assets/74299317/ba3d33e2-7849-4148-9a38-1775c430ca8c)
