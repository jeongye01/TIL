## 1. `with` 문법이란?

- **Context Manager**를 이용해 리소스를 자동으로 관리하는 문법
- 파일, 네트워크 연결, DB 커넥션, 락(lock) 등 **열고 닫는 과정이 필요한 자원**에서 주로 사용됨
- 예외가 발생해도 자동으로 정리(`close`)가 보장됨

---

## 2. 기본 구조

```python
with context_manager as var:
    # 이 블록 안에서 var 사용
# 블록이 끝나면 자동으로 정리
```

---

## 3. 예제

### (1) 파일 다루기

```python
with open("test.txt", "r") as f:
    data = f.read()
# 여기 나오면 f.close() 자동 호출됨

```

➡️ try/finally 없이 파일 안전하게 닫을 수 있음

---

### (2) 압축(zip) 파일 다루기

```python
import zipfile
with zipfile.ZipFile("data.zip") as z:
    print(z.namelist())
# 블록 종료 시 자동으로 close

```

---

### (3) S3 객체 메모리에 읽기

```python
with zipfile.ZipFile(bytestream) as z:
    file_name = z.namelist()[0]
    with z.open(file_name) as f:
        df = pd.read_csv(f, sep="\t")

```

➡️ zip 파일 열고, 내부 파일도 열고 → 블록이 끝나면 자동으로 닫힘

---

## 4. 직접 Context Manager 만들기

- `__enter__`와 `__exit__` 메서드를 구현하면 `with` 문에서 사용 가능

```python
class MyContext:
    def __enter__(self):
        print("자원 열기")
        return "리소스"
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("자원 닫기")

with MyContext() as r:
    print("사용:", r)

```

---

## 5. 요약

- `with` = **try/finally 자동화 문법**
- 리소스를 안전하게 관리 (파일, 네트워크, DB 등)
- 블록이 끝나면 자동으로 `close()` 실행
