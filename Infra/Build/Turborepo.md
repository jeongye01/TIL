> High-performance build system for Monorepos.
Turborepo의 주요 미션은 모노레포 환경에서 개발자가 조금 더 쉽고 빠르게 개발할 수 있도록 빌드 도구를 제공하는 것
> 

**JavaScript 및 TypeScript 코드를 위한 고성능 빌드 시스템**

# **Turborepo의 특징**

- Incremental builds: 한 번 작업을 수행하며 수행한 계산은 이후 다시 수행하지 않음. 따라서 두 번째 실행할 때는 이전에 계산한 작업은 건너뛰고 이전에 캐싱해 놓은 로그를 다시 보여줌. → CI 소요시간을 줄여줌
- Remote Caching : 공용 캐시 서버
- Content-aware hashing **:** 코드 내용을 구분•식별해서 해시값으로 코드의 변경을 감지
- Parallel execution : 모든 코어를 사용하는 병렬 실행을 목표로함. 지정된 태스크 단위로 의존성을 판단해 최대한 병렬적으로 작업 진행
    
    ![image](https://github.com/jeongye01/TIL/assets/74299317/4c3fc957-1973-493a-9157-fffc87d27a89)

    

# Turbo 설정

 `turbo.json`

파이프라인의 각 명령어들은 하나의 태스크 단위이며 이 단위가 '병렬 처리 및 의존성의 범위'가 됨

```json
{
    "baseBranch": "origin/main",
    "pipeline": {
      "build": {
        // ^는 커맨드 실행 전 dependencies 혹은 devDependencies의 위상 의존성을 가질 때 명시해 줍니다(https://turborepo.org/docs/glossary#topological-order).
        // 의존성 빌드 명령이 실행된 후 build 커맨드가 실행됩니다.
        "dependsOn": [
          "^build"
        ],
        // 기본 캐시 폴더를 지정합니다.
        "outputs": [
          ".next/**",
          "lib/**",
          "storybook-static/**"
        ]
      },
      "cypress:ci": {
        "dependsOn": [
          // 특정 패키지를 지정하고 싶다면 '패키지명#스크립트'로 하면 됩니다.
          "@linecorp/uvp#build"
        ]
      },
      // 아무런 명시가 없다면 의존성이 없다는 것을 의미하며, 이는 언제든지 실행될 수 있다는 것을 의미합니다.
      // 작업이 가능할 때마다 실행합니다.
      "lint": {},
      "deploy": {
        // 의존성을 여러 개 지정할 경우 터보가 똑똑하게 순서를 맞춰서 진행해 줍니다. 위 'Profile in browser'의 이미지를 참고해 주세요.
        "dependsOn": [
          "build",
          "cypress:ci",
          "snapshots",
          "lint"
        ]
      },
      // 개발 환경과 같이 핫 로딩이 필요할 경우 캐시를 비활성화할 수 있습니다.
      "dev": {
        "cache": false
      }
   }
}
```

# Turbo 실행

파이프라인의 키 명령어들은 각 모노레포의 `package.json` 스크립트와 매핑됨. 만약 패키지 스크립트에 없는 명령어라면 해당 모노레포에서는 무시됨(예: A 패키지에는 린트(lint) 스크립트가 있지만 B 패키지에는 린트 스크립트가 없다면 A 패키지만 실행됨).

참고

https://engineering.linecorp.com/ko/blog/monorepo-with-turborepo?ref=codenary

https://techblog.lycorp.co.jp/ko/monorepo-structure-for-abc-user-feedback?ref=codenary

https://tech.inflab.com/20231101-optimizing-ci-pipeline/?ref=codenary
