## 📌 개념

- **PnP = Plug’n’Play**
- 기존 `node_modules` 방식 대신, **전역 캐시 + 매핑 파일**을 사용해 의존성을 관리하는 Yarn의 새로운 방식.
- 프로젝트 안에 `node_modules` 디렉토리를 만들지 않는다.

---

## 🛠 동작 원리

1. **전역 캐시**
    - 모든 패키지를 `~/.yarn/cache/` 같은 전역 캐시에 한 번만 저장.
    - 버전이 다르면 여러 개 저장되지만, 같은 버전은 중복 저장하지 않음.
2. **매핑 파일 (.pnp.cjs)**
    - 각 프로젝트 루트에 `.pnp.cjs` 파일 생성.
    - `require("lib")` 호출 시 → Yarn 런타임이 `.pnp.cjs`를 읽어 전역 캐시의 정확한 패키지로 매핑.
    - 즉, “이 프로젝트는 lodash@4.17.21 → 여기 캐시에 있음” 이라는 매핑 정보 저장소.

---

## 📂 구조 비교

### 기존 (node_modules)

```
project-a/node_modules/lodash (4.17.21)
project-b/node_modules/lodash (4.17.21)   ← 중복 설치

```

### Yarn PnP

```
~/.yarn/cache/lodash-4.17.21.zip   ← 전역에 한 번만 존재
project-a/.pnp.cjs                 ← 매핑
project-b/.pnp.cjs                 ← 매핑

```

---

## ✅ 장점

- 설치 속도 🚀 (복사 대신 매핑만 함)
- 디스크 절약 💾 (중복 설치 제거)
- 의존성 해상도 명확 🔍 (어떤 버전을 쓰는지 `.pnp.cjs`에 기록)
- 보안 강화 🔒 (선언되지 않은 패키지는 import 불가)

---

## ⚠️ 단점

- 일부 도구/IDE는 `node_modules` 전제가 있어 호환성 이슈 발생 가능.
- VSCode, TypeScript 등은 Yarn SDK 플러그인 세팅 필요.

---

## 📌 명령어

```bash
# Yarn Berry (v2+)에서 PnP 활성화
yarn config set nodeLinker pnp

# node_modules 방식으로 되돌리기
yarn config set nodeLinker node-modules

```
