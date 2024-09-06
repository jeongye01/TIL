https://engineering.linecorp.com/ko/blog/pm2-nodejs

# 사용 예시

##  내 로컬에서 특정 브랜치를 배포

`yarn`을 사용하는 경우에도 `PM2`로 특정 브랜치를 로컬에서 지속적으로 배포할 수 있습니다. `npm` 대신 `yarn` 명령어를 사용하는 방법을 설명하겠습니다.

### 1. PM2 설치

우선, PM2가 설치되어 있지 않다면 글로벌로 설치합니다.

```bash
bash코드 복사
yarn global add pm2

```

혹은 `npm`으로도 설치할 수 있습니다.

```bash
bash코드 복사
npm install pm2 -g

```

### 2. 특정 브랜치로 이동

배포하고자 하는 브랜치로 이동합니다.

```bash
bash코드 복사
git checkout <브랜치명>

```

### 3. 의존성 설치

해당 브랜치에서 필요한 패키지를 설치합니다.

```bash
bash코드 복사
yarn install

```

### 4. PM2로 `yarn` 명령어를 사용하여 서버 실행

이제 `PM2`를 사용해 `yarn`으로 애플리케이션을 데몬화하여 실행할 수 있습니다.

```bash
bash코드 복사
pm2 start yarn --name "<프로세스_이름>" -- start

```

예시:

```bash
bash코드 복사
pm2 start yarn --name "my-react-app" -- start

```

이 명령어는 `my-react-app`이라는 이름으로 PM2가 백그라운드에서 애플리케이션을 실행하도록 설정합니다. 이후에도 애플리케이션은 계속 백그라운드에서 실행됩니다.

### 5. 다른 브랜치로 전환해도 배포 유지

이제 터미널에서 다른 브랜치로 전환하더라도 `PM2`에 의해 애플리케이션이 계속 실행됩니다.

```bash
bash코드 복사
git checkout <다른_브랜치명>

```

이 작업을 해도 이전 브랜치의 애플리케이션은 백그라운드에서 계속 실행됩니다.

### 6. PM2에서 프로세스 확인 및 관리

실행 중인 PM2 프로세스를 확인하려면 다음 명령어를 사용합니다.

```bash
bash코드 복사
pm2 list

```

특정 프로세스를 중지하고 싶다면:

```bash
bash코드 복사
pm2 stop <프로세스_이름>

```

프로세스를 삭제하려면:

```bash
bash코드 복사
pm2 delete <프로세스_이름>

```

### 7. PM2 자동 재시작 (옵션)

PM2는 기본적으로 오류가 발생하거나 서버가 재시작되면 애플리케이션을 자동으로 재시작하는 기능이 있습니다. 로그를 확인하려면 아래 명령어를 사용합니다.

```bash
bash코드 복사
pm2 logs <프로세스_이름>

```

### 요약

1. **브랜치 체크아웃**: `git checkout <브랜치명>`
2. **의존성 설치**: `yarn install`
3. **PM2로 서버 실행**: `pm2 start yarn --name "my-app" -- start`
4. **프로세스 관리**: `pm2 list`, `pm2 stop`, `pm2 delete`

이렇게 하면 `yarn`으로 특정 브랜치를 배포하고, PM2를 통해 애플리케이션이 백그라운드에서 지속적으로 실행될 수 있습니다.




`PATH`에 등록하는 방법은 시스템 환경 설정 파일을 수정하여, 필요한 경로를 추가하는 것입니다. 아래는 `yarn` 또는 `npm`의 글로벌 바이너리 경로를 `PATH`에 등록하는 방법을 단계별로 설명합니다.

### 1. **`yarn global bin` 경로 확인**

먼저, `yarn` 글로벌 바이너리 파일들이 어디에 설치되어 있는지 확인합니다.

```bash
bash코드 복사
yarn global bin

```

이 명령어를 실행하면, 출력된 경로가 `yarn`의 글로벌 바이너리 파일들이 위치한 경로입니다. 예를 들어, `/Users/username/.yarn/bin`와 같은 경로가 출력될 수 있습니다.

### 2. **`npm bin -g` 경로 확인 (npm을 사용하는 경우)**

만약 `npm`으로 설치했다면, 글로벌 경로를 확인하는 명령어는 다음과 같습니다.

```bash
bash코드 복사
npm bin -g

```

`/usr/local/bin`과 같은 경로가 출력될 수 있습니다. 이 경로를 기억해 두세요.

### 3. **환경 설정 파일 편집**

이제 이 경로를 `PATH`에 추가하기 위해, 사용 중인 셸에 따라 적절한 환경 설정 파일을 수정해야 합니다. 일반적으로 사용하는 셸에 따라 다르게 설정 파일을 수정합니다.

- `bash` 셸을 사용하는 경우: `~/.bashrc`
- `zsh` 셸을 사용하는 경우: `~/.zshrc`

터미널에서 다음 명령어로 설정 파일을 엽니다.

- **bash**의 경우:
    
    ```bash
    bash코드 복사
    nano ~/.bashrc
    
    ```
    
- **zsh**의 경우:
    
    ```bash
    bash코드 복사
    nano ~/.zshrc
    
    ```
    

이 파일을 열면, 가장 마지막 줄에 `yarn global bin` 또는 `npm bin -g` 경로를 `PATH`에 추가하는 명령어를 입력합니다.

예시:

```bash
bash코드 복사
export PATH="$PATH:$(yarn global bin)"

```

또는 `npm`을 사용하는 경우:

```bash
bash코드 복사
export PATH="$PATH:$(npm bin -g)"

```

### 4. **변경 사항 적용**

설정 파일을 저장한 후, 변경 사항을 적용하기 위해 터미널에서 다음 명령어를 실행합니다.

- **bash**의 경우:
    
    ```bash
    bash코드 복사
    source ~/.bashrc
    
    ```
    
- **zsh**의 경우:
    
    ```bash
    bash코드 복사
    source ~/.zshrc
    
    ```
    

### 5. **설치 확인**

이제 터미널에서 `pm2`가 정상적으로 작동하는지 확인해 보세요.

```bash
bash코드 복사
pm2 --version

```

정상적으로 버전이 출력되면, `PATH` 설정이 성공한 것입니다.

### 6. **재부팅**

경우에 따라 위의 방법으로도 바로 적용되지 않을 수 있습니다. 그럴 때는 시스템을 재부팅하면 `PATH` 설정이 적용됩니다.

이렇게 하면 `pm2`를 포함한 글로벌 명령어들이 `PATH`에 등록되어, 어느 디렉터리에서든지 `pm2` 명령어를 사용할 수 있게 됩니다.




### **컴퓨터 종료 시 PM2 프로세스 자동 종료**

운영체제에서 컴퓨터가 종료될 때 PM2 프로세스를 자동으로 종료하고 싶다면, 시스템 종료 시 PM2 프로세스를 중지하도록 설정할 수 있습니다. 리눅스와 macOS는 종료 시 스크립트를 추가할 수 있는 방법을 제공합니다.

### 2-1. 리눅스에서 설정하는 방법

리눅스에서는 시스템 종료 시 실행할 스크립트를 `/etc/systemd/system` 또는 `/etc/init.d/`에 작성할 수 있습니다.

1. **스크립트 생성**: 예를 들어, `/etc/systemd/system/pm2-shutdown.service` 파일을 생성합니다.
    
    ```bash
    bash코드 복사
    sudo nano /etc/systemd/system/pm2-shutdown.service
    
    ```
    
2. **스크립트 내용 추가**: 아래와 같이 내용을 작성합니다.
    
    ```
    ini코드 복사
    [Unit]
    Description=Shutdown PM2 process
    Before=shutdown.target
    
    [Service]
    Type=oneshot
    ExecStart=/usr/bin/pm2 stop all
    RemainAfterExit=true
    
    [Install]
    WantedBy=halt.target reboot.target shutdown.target
    
    ```
    
3. **서비스 활성화**: 이 스크립트를 활성화하여 시스템 종료 시 PM2가 자동으로 중지되도록 설정합니다.
    
    ```bash
    bash코드 복사
    sudo systemctl enable pm2-shutdown
    
    ```
    

### 2-2. macOS에서 설정하는 방법

macOS에서 종료 시 PM2 프로세스를 자동으로 중지하려면 `launchd`를 사용해야 합니다. `launchd`는 macOS의 서비스 관리 시스템입니다.

1. **스크립트 파일 작성**: `/Library/LaunchDaemons/pm2-shutdown.plist` 파일을 생성합니다.
    
    ```bash
    bash코드 복사
    sudo nano /Library/LaunchDaemons/pm2-shutdown.plist
    
    ```
    
2. **스크립트 내용 추가**:
    
    ```xml
    xml코드 복사
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>Label</key>
        <string>pm2-shutdown</string>
        <key>ProgramArguments</key>
        <array>
            <string>/usr/local/bin/pm2</string>
            <string>stop</string>
            <string>all</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
        <key>KeepAlive</key>
        <false/>
        <key>OnDemand</key>
        <false/>
    </dict>
    </plist>
    
    ```
    
3. **파일 권한 설정**:
    
    ```bash
    bash코드 복사
    sudo chmod 644 /Library/LaunchDaemons/pm2-shutdown.plist
    sudo launchctl load /Library/LaunchDaemons/pm2-shutdown.plist
    
    ```
    

이렇게 하면 macOS에서 시스템 종료 시 PM2가 자동으로 프로세스를 중지합니다.

### 3. **결론**

1. **PM2 상태 저장**: `pm2 save`를 통해 현재 실행 중인 프로세스를 저장.
2. **PM2 자동 시작 설정**: `pm2 startup` 명령을 통해 시스템이 재시작될 때 PM2 프로세스가 자동으로 시작되게 설정.
3. **컴퓨터 종료 시 자동 중지**: 시스템의 종료 스크립트를 설정하여 컴퓨터 종료 시 PM2 프로세스를 자동으로 중지.

이 과정을 통해 컴퓨터가 꺼지거나 재부팅될 때 PM2로 실행된 프로세스들이 안전하게 종료되고, 재시작 시 다시 자동으로 실행될 수 있습니다.
