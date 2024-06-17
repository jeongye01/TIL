## Gradle?
- 오픈 소스 빌드 자동화 시스템(소스코드 컴파일, 테스트 실행, JAR 파일 생성 등)
- 의존성 관리 : 프로젝트에 필요한 외부 라이브러리와 프레임워크를 자동으로 다운로드
- 성능 : 변경된 부분만 빌드와 캐싱을 통해 빌드 시간을 단축시킨다.
- 플러그인 시스템 :  다양한 플러그인을 통해 기능을 확장할 수 있으며, 커스텀 플러그인도 쉽게 작성할 수 있다. 

## 작성 예시
```jsx
// 플러그인 의존성
plugins {
	id 'java' // Java 플러그인을 적용하여 프로젝트를 Java 프로젝트로 설정
	id 'org.springframework.boot' version '3.3.0' // Spring Boot 플러그인을 적용하여 Spring Boot 관련 기능을 사용
	id 'io.spring.dependency-management' version '1.1.5' // 스프링 부트의 의존성들을 관리해주는 플러그인이라 꼭 추가해야만 함.
} 

group = 'com.yewon'
version = '0.0.1-SNAPSHOT' // SNAPSHOT은 개발 중인 버전을 나타냄

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(17) // 자바 버전 설정
	}
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

// 어디서 의존성을 다운로드 할지 
repositories {
	mavenCentral() // 이전부터 많이 사용하는 저장소. 본인이 만든 라이브러리 업로드 하기 힘듦.
	jcenter() // 라이브러리 업로드 간단. 라이브러리 업로드하면 자동으로 mavenCentral에도 올라감. 점점 jcenter로 이동하는 추세. 두개 모두 적어놔도됌. 
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

tasks.named('test') {
	useJUnitPlatform()
}

```
