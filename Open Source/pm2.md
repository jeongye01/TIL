https://engineering.linecorp.com/ko/blog/pm2-nodejs

# 사용 예시

##  내 로컬에서 특정 브랜치를 배포

yarn을 사용하는 경우에도 PM2로 특정 브랜치를 로컬에서 지속적으로 배포할 수 있습니다. npm 대신 yarn 명령어를 사용하는 방법을 설명하겠습니다.

1. PM2 설치
우선, PM2가 설치되어 있지 않다면 글로벌로 설치합니다.

bash
코드 복사
yarn global add pm2
혹은 npm으로도 설치할 수 있습니다.

bash
코드 복사
npm install pm2 -g
2. 특정 브랜치로 이동
배포하고자 하는 브랜치로 이동합니다.

bash
코드 복사
git checkout <브랜치명>
3. 의존성 설치
해당 브랜치에서 필요한 패키지를 설치합니다.

bash
코드 복사
yarn install
4. PM2로 yarn 명령어를 사용하여 서버 실행
이제 PM2를 사용해 yarn으로 애플리케이션을 데몬화하여 실행할 수 있습니다.

bash
코드 복사
pm2 start yarn --name "<프로세스_이름>" -- start
예시:

bash
코드 복사
pm2 start yarn --name "my-react-app" -- start
이 명령어는 my-react-app이라는 이름으로 PM2가 백그라운드에서 애플리케이션을 실행하도록 설정합니다. 이후에도 애플리케이션은 계속 백그라운드에서 실행됩니다.

5. 다른 브랜치로 전환해도 배포 유지
이제 터미널에서 다른 브랜치로 전환하더라도 PM2에 의해 애플리케이션이 계속 실행됩니다.

bash
코드 복사
git checkout <다른_브랜치명>
이 작업을 해도 이전 브랜치의 애플리케이션은 백그라운드에서 계속 실행됩니다.

6. PM2에서 프로세스 확인 및 관리
실행 중인 PM2 프로세스를 확인하려면 다음 명령어를 사용합니다.

bash
코드 복사
pm2 list
특정 프로세스를 중지하고 싶다면:

bash
코드 복사
pm2 stop <프로세스_이름>
프로세스를 삭제하려면:

bash
코드 복사
pm2 delete <프로세스_이름>
7. PM2 자동 재시작 (옵션)
PM2는 기본적으로 오류가 발생하거나 서버가 재시작되면 애플리케이션을 자동으로 재시작하는 기능이 있습니다. 로그를 확인하려면 아래 명령어를 사용합니다.

bash
코드 복사
pm2 logs <프로세스_이름>
요약
브랜치 체크아웃: git checkout <브랜치명>
의존성 설치: yarn install
PM2로 서버 실행: pm2 start yarn --name "my-app" -- start
프로세스 관리: pm2 list, pm2 stop, pm2 delete
이렇게 하면 yarn으로 특정 브랜치를 배포하고, PM2를 통해 애플리케이션이 백그라운드에서 지속적으로 실행될 수 있습니다.
