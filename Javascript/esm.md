`.esm.js` === `.mjs` (무조건 ESM으로 해석됨) 파일은 보통 **ESM 규칙에 따라 작성된 자바스크립트 파일**로, 브라우저와 Node.js에서 모두 지원되며, 모듈 간의 의존성을 명확하게 정의하고, 트리 쉐이킹 등과 같은 최적화 기능을 지원한다.

### 주요 특징:

1. **ESM 형식**:
    - ESM(ECMAScript Module)은 ES6(ES2015)부터 도입된 모듈 시스템이다.
    - `import`와 **`export`** 구문을 사용하여 모듈 간의 의존성을 처리한다.
    - 트리 쉐이킹이 잘 작동하여 **사용되지 않는 코드**를 자동으로 제거할 수 있습니다.
    - 모듈은 기본적으로 **strict mode**(엄격 모드)로 동작합니다.
2. **파일 확장자 `.esm.js`**:
    - `.esm.js` 파일은 일반적으로 라이브러리에서 **ESM 모듈 형식**으로 배포되는 JavaScript 파일이다.
    - 많은 라이브러리가 여러 형식으로 배포되며, 예를 들어 CommonJS 형식은 `.cjs.js` === `.cjs` (무조건 CommonJS로 해석됨) 확장자를 사용할 수 있고, ESM 형식은 `.esm.js` 확장자를 사용하는 경우가 많다.
    - **번들링 과정에서 트리 쉐이킹**을 적용할 때 `.esm.js` 파일은 중요한 역할을 한다. ESM 형식의 파일은 **정적 분석**을 가능하게 하여 사용하지 않는 코드가 번들에서 제거된다.
3. **ESM vs. CommonJS**:
    - **ESM(ES6 모듈 시스템)**: 모듈을 **정적으로** 가져오고, 컴파일 타임에 의존성을 분석할 수 있다.
        
        ```jsx
        
        // ESM 예시
        import { myFunction } from './module.js';
        export const anotherFunction = () => {};
        
        ```
        
    - **CommonJS(구형 Node.js 모듈 시스템)**: 동적으로 `require()`를 사용하여 모듈을 가져온다.
        
        ```jsx
        
        // CommonJS 예시
        const myFunction = require('./module.js');
        module.exports = anotherFunction;
        
        ```
        
4. **번들링 과정에서의 사용**:
    - 번들러(Vite, Webpack 등)는 `.esm.js` 파일을 처리할 때 ESM 모듈의 특성 덕분에 **트리 쉐이킹**을 효율적으로 수행할 수 있습니다. CommonJS 형식(`.cjs.js`) 파일은 동적 임포트로 인해 트리 쉐이킹이 어려운 경우가 있지만, ESM 파일은 코드 분석이 정적으로 가능하기 때문에 번들러가 불필요한 코드를 쉽게 제거할 수 있다.
