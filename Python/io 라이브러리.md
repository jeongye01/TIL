## 1. `io`란?

- Python의 **입출력(I/O) 표준 라이브러리**
- 파일/메모리/네트워크 데이터 스트림을 다룰 수 있게 해줌
- “파일처럼 읽고 쓰기” 인터페이스 제공 (`read`, `write` 등)

---

## 2. 주요 클래스

### (1) `io.BytesIO`

- **바이트 데이터를 메모리에서 파일처럼 다루기**
- 네트워크/압축 파일/바이너리 데이터 처리에 유용

```python
import io

data = b"hello world"
buffer = io.BytesIO(data)
print(buffer.read())   # b'hello world'

```

---

### (2) `io.StringIO`

- **문자열 데이터를 메모리에서 파일처럼 다루기**

```python
import io

text = "hello\nworld"
buffer = io.StringIO(text)
for line in buffer:
    print(line.strip())

```

---

### (3) `io.TextIOWrapper`

- **바이너리 스트림에 텍스트 디코딩 입히기**
- 예: 네트워크에서 받은 `bytes`를 UTF-8 문자열로 다룰 때 사용

---

## 3. 실제 활용 예시

### (1) S3에서 파일 읽기

```python
obj = s3.get_object(Bucket="my-bucket", Key="file.zip")
bytestream = io.BytesIO(obj["Body"].read())  # 메모리에서 zip 열기

```

---

### (2) DataFrame을 Parquet으로 저장 후 S3 업로드

```python
buffer = io.BytesIO()
df.to_parquet(buffer, index=False, engine="pyarrow")

s3.put_object(
    Bucket="my-bucket",
    Key="output.parquet",
    Body=buffer.getvalue()
)

```

---

## 4. BytesIO vs StringIO 비교

| 구분 | BytesIO 🪣 | StringIO ✍️ |
| --- | --- | --- |
| 데이터 타입 | `bytes` (바이너리) | `str` (문자열) |
| 사용 사례 | 이미지, zip, parquet, 네트워크 데이터 | 로그 문자열, 텍스트 가공, 임시 버퍼 |
| 입출력 방식 | 바이너리 스트림 (b"…") | 텍스트 스트림 ("…") |
| 주요 활용 | 파일 업로드/다운로드, 압축 해제 | 테스트용 텍스트 I/O, 문자열 가공 |

---

## 5. 요약

- `io.BytesIO`: **바이너리 데이터 → 파일처럼**
- `io.StringIO`: **문자열 데이터 → 파일처럼**
- 공통점: **실제 디스크 파일 없이, 메모리에서 파일처럼 읽고 쓰기 가능**

---

👉 요약 한 줄:

> BytesIO는 S3/네트워크/바이너리 데이터 처리, StringIO는 텍스트 처리에 최적화된 메모리 파일 객체다.
>
