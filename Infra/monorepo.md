# **모노레포란?**

모노레포는 단일 리포지터리에 여러 개의 서브 프로젝트가 존재하는 방식.단일 저장소의 하나의 프로젝트 안에 여러 개의 서브 패키지를 만드는 방식

![image](https://github.com/jeongye01/TIL/assets/74299317/4f2156af-4ba5-412c-b225-378ae4973037)


### 멀티 레포 단점

: 각 모듈이 서로 독립된 영역에서 존재하기 때문에 코드 단계에서의 재사용이 어려워지고 빌드와 배포 과정이 복잡해짐. 

### 모노레포 특징

- 모놀리식 리포지터리와 멀티레포의 장점을 모두 취함
- 모든 서브 패키지의 설정을 동일하게 맞춰서 코드의 일관성을 유지할 수 있음
- 코드 관심사 분리 용이
- 기능 추가나 삭제에 의한 영향 감소
- Visibility
    - 리포지터리가 하나이기 때문에 모든 프로젝트의 코드와 자원(assets) 간의 관계와 의존성을 한눈에 확인할 수 있습니다.
- Collaboration
    - 모든 커밋 히스토리가 한 리포지터리에 남기 때문에 히스토리를 추적하거나 전체 리포지터리의 개발 방향을 이해하는 게 쉬워집니다.
    - 여러 곳에서 중복으로 사용하는 자산들(테스트 코드 등)을 공유하고 재사용할 수 있습니다.
- Speed
    - 배포와 빌드, 테스트와 같은 작업을 병렬로 한 번에 처리할 수 있으므로 한 번의 명령으로 여러 개의 리포지터리에서 작업을 진행할 수 있습니다.
    

# 구조 예시

typescript 풀스택 프로젝트 repo 

```json
apps
  ㄴ web
  ㄴ api
pacakges
  ㄴ ufb-shared      // @ufb/shared
  ㄴ ufb-tailwind   // @ufb/tailwind
  ㄴ ufb-ui         // @ufb/ui
tooling
  ㄴ eslint          // @ufb/eslint-config
  ㄴ prettier        // @ufb/prettier-config
  ㄴ typescript      // @ufb/tsconfig
```

- tooling : 전반적인 공통 설정을 위한 패키지 모음
- packages : 코드를 공유하기 위한 패키지 모음
- apps **:** 하나의 애플리케이션으로 빌드되는 패키지
