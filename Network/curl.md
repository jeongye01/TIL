## 📌 `curl`이란?

- **Client URL**의 약자.
- URL을 이용해 데이터를 전송하거나 받는 **범용 네트워크 클라이언트 도구**.
- CLI에서 실행 가능하며, API 테스트, 파일 전송, 서버 디버깅 등에 활용된다.

---

## 🌐 지원 프로토콜

- **HTTP / HTTPS** → API 테스트, 웹 요청
- **FTP / FTPS** → 파일 업로드/다운로드
- **SCP / SFTP** → SSH 기반 파일 전송
- **LDAP** → 디렉토리 서비스 접근
- **SMTP / POP3 / IMAP** → 메일 송수신
- **MQTT** → IoT 메시징
- **FILE** → 로컬 파일 접근

👉 HTTP만 되는 게 아니라, **다양한 네트워크 프로토콜**을 지원한다.

---

## 🛠️ 주요 사용법

### 1. GET 요청

```bash
curl https://example.com
```

### 2. POST 요청 (JSON Body)

```bash
curl -X POST https://api.example.com/items \
  -H "Content-Type: application/json" \
  -d '{"name":"DayDot","type":"Diary"}'
```

### 3. 응답 헤더만 확인

```bash
curl -I https://example.com
```

### 4. 파일 다운로드

```bash
curl -O https://example.com/file.zip
```

### 5. 파일 업로드 (FTP)

```bash
curl -T local.txt ftp://ftp.example.com/ --user user:pass
```
