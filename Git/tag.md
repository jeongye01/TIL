`git tag` 명령어는 Git에서 특정 커밋에 **태그(tag)**를 붙여서 쉽게 참조할 수 있게 해주는데(커밋의 스냅샷을 저장) 보통 소프트웨어 릴리스(버전)을 표시할 때 사용한다. 

### 기본적인 `git tag` 명령어 사용법

### 1. **태그 목록 확인**

```bash
git tag
v0.1
v1.3

// 패턴 검색
git tag -l "v0*"
v0.2.0
v0.2.1
v0.2.2
v0.2.3
```

- 현재 저장소에 있는 모든 태그를 나열한다.

### 2. **태그 생성**

Git의 태그는 ***Lightweight*** 태그와 ***Annotated*** 태그로 두 종류가 있다.

- **Annotated Tag** (주석이 있는 태그)
    
    ```bash
    git tag -a <tag_name> -m "tag message"
    ```
    
    - `a` 옵션은 태그를 작성자 정보, 날짜, 메시지를 포함한 **Annotated Tag**로 만든다.
    - `m` 옵션으로 태그에 대한 설명을 추가할 수 있다.
    
    예:
    
    ```bash
    git tag -a v1.0.0 -m "Release version 1.0.0"
    ```
    
- **Lightweight Tag** (간단한 태그)
    
    ```bash
    git tag <tag_name>
    ```
    
    - 간단하게 태그를 커밋에 붙일 수 있지만, 주석이나 메타데이터가 없다.
    - 단순히 특정 커밋에 대한 포인터일 뿐이다.
    
    예:
    
    ```bash
    git tag v1.0.0
    ```
    

### 3. **특정 커밋에 태그 추가**

```bash
git tag -a <tag_name> <commit_id> -m "tag message"
```

- `<commit_id>` 자리에 특정 커밋 해시를 넣으면 그 커밋에 태그를 붙일 수 있다.

예:

```bash
git tag -a v1.0.0 9fceb02 -m "Tagging version 1.0.0"
```

### 4. **태그 삭제**

- 로컬에서 태그 삭제
    
    ```bash
    git tag -d <tag_name>
    ```
    
    예:
    
    ```bash
    git tag -d v1.0.0
    ```
    
- 원격에서 태그 삭제
    
    ```bash
    git push origin --delete tag <tag_name>
    ```
    
    예:
    
    ```bash
    git push origin --delete tag v1.0.0
    ```
    

### 5. **태그 푸시 (원격 저장소에 태그 전송)**

`git push` 명령은 자동으로 리모트 서버에 태그를 전송하지 않는다. 태그를 만들었으면 서버에 별도로 Push 해야 한다

- 모든 태그를 푸시
    
    ```bash
    git push origin --tags
    ```
    
- 특정 태그를 푸시
    
    ```bash
    git push origin <tag_name>
    ```
    
    예:
    
    ```bash
    git push origin v1.0.0
    ```
    

### 6. **태그로 체크아웃**

```bash
git checkout <tag_name>

git checkout -b version2 v2.0.0
Switched to a new branch 'version2'
```

- 해당 태그가 붙은 시점의 커밋으로 체크아웃할 수 있다. 단, “detached HEAD”(떨어져나온 HEAD) 상태가 되며 일부 Git 관련 작업이 브랜치에서 작업하는 것과 다르게 동작할 수 있다.
- 특정 태그의 상태에서 새로 작성한 커밋이 버그 픽스와 같이 의미있도록 하려면 반드시 브랜치를 만들어서 작업하는 것이 좋다.

### 정리

- **태그 목록 확인:** `git tag`
- **태그 생성:** `git tag -a <tag_name> -m "message"`
- **태그 삭제:** `git tag -d <tag_name>`
- **태그 푸시:** `git push origin --tags`
- **특정 커밋에 태그:** `git tag -a <tag_name> <commit_id> -m "message"`
