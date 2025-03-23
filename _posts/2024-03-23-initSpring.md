---
title:  "스프링 입문"
categories:
  - springStudy
---

## 1. 학습 정리

### PUT vs POST

|메서드|역할|용도|특징|
|:---:|:---:|:---:|:---:|
|POST|새로운 리소스 생성|회원가입, 글쓰기, 데이터 추가|요청을 보낼 때마다 새로운 데이터가 추가됨
|PUT|리소스 전체 수정(덮어쓰기)|회원 정보 수정, 게시글 수정|기존 데이터가 있으면 덮어쓰고, 없으면 새로 생성함|


### 랜더링 vs 리다이렉트

`랜더링`
서버가 HTML을 렌더링해서 그대로 응답해서 현재 페이지에서 새 데이터를 받아 화면을 다시 그린다.
Post후 새로고침하면 기존 요청이 다시 실행됨 (POST 그대로 다시 요청)

`리다이렉트` 
서버가 "다른 URL로 가라"는 응답을 보내서 새로운 URL로 이동함
새로고침하면 새로운 GET 요청으로 변경됨 (중복 요청 없음)


#### 차이점
차이점은 새로고침에서 보이게 되는데
`랜더링` 방식은 쇼핑몰에서 장바구니에 물건을 담고나서 F5를 누르면 이전 요청이 남아서 장바구니 물건을 한 번 더 담게되고,
`리다이렉트` 방식은 장바구니 요청을 다시 실행하진 않고, 그냥 장바구니 목록이 새로고침됨




## 2. 스프링 부트 시작하기

### (1) 스프링 프레임워크 vs. 스프링 부트 개념 이해

- #### Gradle 은 무엇인가?

 **`유연한 빌드 자동화 도구`** 입니다.
 
 **빌드란?**  
 
 우리가 짠 소스코드를 컴파일하고, 의존성을 추가하여 실행가능한 파일(.jar, .war)로 패키징해줘야 실행이 가능합니다. 그럼 JVM이 실행해줍니다.
 
 자바 소스코드 (class파일), 설정 파일 (application.yml, application.properties), 의존성 라이브러리, 정적 리소스(html, css), 빌드 결과물 등이 저장됨
 
 **유연한 이유**
 
 `Gradle` 은 `Groovy` (프로듀서 ❌) 기반으로 자바와 유사한 구조를 가지고, JVM에서 실행되는 `스크립트 언어` 입니다.
 
 **Groovy**
 
 JVM 위에서 실행되는 동적 프로그래밍 언어( `runtime` 에 변수의 타입을 결정하고, 변경할 수 있는 언어를 말합니다.)
 
 코드를 작성하면 즉시 실행할 수 있으며, 자바처럼 .class 파일을 생성하고 실행하는 과정이 필요 없습니다. 
 
 한 줄씩 실행하는 인터프린터 방식, 간단한 코드로 바로 실행 가능, 자동 메모리 관리 기능, 동적 프로그래밍 지원 기능까지 있습니다.
 
 
 `Groovy와 자바 예제`
 
 ```java
 
 // java < 정적 프로그래밍 언어 >
 public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}

 //groovy < 동적 프로그래밍 언어 >
 println "Hello, World!"

 ```
 
 **빌드 스크립트**
 
 Gradle의 빌드 스크립트는 스크립트 언어(Groovy, Kotlin)를 기반으로 작성된 구성(Configuration) 파일입니다. 
 
 즉, Gradle 자체가 스크립트 언어는 아니지만, 스크립트 언어를 사용하여 빌드 프로세스를 정의하는 구조입니다.
 
 **의존성 관리**
   
   빌드 스크립트에는 의존성 관리(Dependencies)를 해주는데
   프로젝트에서 사용하는 라이브러리나 패키지를 `의존성` 이라고 합니다.
   프로젝트별로 어떤 의존성을 갖는지 명시해줘야 합니다.
   
  `Dependencies Configuration`
  특정 시점에 불필요한 라이브러리를 추가하면 자원낭비가 되므로 라이브러리를 추가하는 시점을 정할 수 있다.
  
  종류에는 implementation, compileOnly, runtimeOnly, testImplementation이 있다.
  
 **빌드 캐시**
 빌드 결과물(컴파일된 클래스파일, 패키징된 jar파일, 테스트 결과, Task실행 결과 등)을 캐시에 저장하므로 반복적으로 빌드를 할 필요가 없음
 
 **점진적 빌드**
 마지막 빌드 이후 변경된 부분만 빌드를 함, 변경되지 않은 부분은 캐시에서 검색해서 재사용한다. Task를 다시 실행할 필요가 없다고 판단하여 `UP-TO-DATE` 가 뜨게 된다.
 
 **데몬 프로세스**
 빌드 속도를 향상시키기 위해 `백그라운드` 에서 실행되는 지속적인 `JVM 프로세스` r이다.
 
 얘를 사용하지 않으면 매번 JVM이 생성되면서 .class파일 로드하고, 스택, 힙을 할당해서 환경을 구성하여 main을 실행하는 과정을 거쳐야하는데 얘는 그냥 한 번 생성된 JVM을 재사용해서 생성 비용, 시간등을 줄여준다.
 
 첫 번째 빌드에만 콘솔에 `Starting Gradle Demon` 이라는게 뜸
 
 **플러그인**
 
 빌드 스크립트는 Gradle이 지원하는 `플러그인` 을 호출할 수 있다.
 
 `Plugin` 이란 특정 작업을 위해 모아놓은 테스크(자바를 빌드하기 위한 여러 개의 명령어들로 구성된 작은 실행 단위)들의 묶음입니다.
 
 자바의 테스크에는 컴파일, 리소스 파일 복사, 클래스 파일 생성, jar파일 생성, 최종 빌드 이런게 있다.
 
 JAVA Plugin을 추가하면 Gradle이 Java 프로젝트를 자동으로 컴파일하고 빌드할 수 있도록 준비합니다.

 
 **Gradle 플러그인의 동작 방식**
 
 `id = java`  
  1. Gradle은 "org.gradle.api.plugins.JavaPlugin" 클래스를 찾아 자동으로 실행
  
  ```java
  
 public class JavaPlugin implements Plugin<Project> {
    @Override
    public void apply(Project project) {
        // Java 컴파일 Task 추가
        project.getTasks().create("compileJava", JavaCompile.class);

        // JAR 파일 생성 Task 추가
        project.getTasks().create("jar", Jar.class);

        // 빌드 Task 정의 (이전 단계들을 연결)
        project.getTasks().create("build").dependsOn("compileJava", "jar");
    }
}
  ```

  
  
  2. Java 관련 Task 자동 생성

  compileJava, test, jar, build 등의 Task가 Gradle의 기본 Task로 등록됩니다.

   3. Task 실행 흐름 자동 연결

   gradle build를 실행하면 Gradle이 내부적으로 필요한 Task들을 순서대로 실행합니다.
  
   
  
   <br><br>


- #### Gradle의 기본적인 사용법

  기본적으로 초기화를 진행한다.
  
  ```bash
  gradle init
  ```
  
  그리고 gradle을 작성한다.
  
  
  ```java
  plugins {
	id 'java'
	id 'org.springframework.boot' version '3.2.1'
	id 'io.spring.dependency-management' version '1.1.7'
   }

   group = 'codestagram'
   version = '0.0.1-SNAPSHOT'

   java {
	 toolchain {
		languageVersion = JavaLanguageVersion.of(21)
	 }
   }

   repositories {
	mavenCentral()
   }

   dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation('nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect')
	runtimeOnly 'com.h2database:h2'
   } 

   tasks.named('test') {
	useJUnitPlatform()
   }
 
 **Plugin**
 Gradle이 특정 기능을 자동으로 수행할 수 있도록 미리 정의된 설정과 Task를 추가함. 그러면 Task가 등록된 java나 spring을 빌드해준다.
 
 **dependencies**
 의존성(Dependency)은 프로젝트에서 사용하는 외부 라이브러리(코드 모음)를 의미하고, 필요한 라이브러리를 자동으로 다운로드하고, 관리할 수 있습니다.
 
 
 |키워드|사용 시점|포함 여부|
 |:---:|:---:|:---:|
 |implementation|컴파일 + 런타임|.jar 파일에 포함됨|
 |compileOnly|컴파일 시만 필요|런타임에는 포함되지 않음|
 |runtimeOnly|런타임에만 필요|컴파일 시에는 사용 불가능|
 |testImplementation|테스트 코드에서만 필요|테스트 환경에서만 포함됨|
  
  
  
  **자주 사용하는 빌드 명령어**
  
  |명령어	|설명|
  |:---:|:---:|
  |gradle build|	전체 빌드 (컴파일, 테스트, 패키징)|
  |gradle clean|	이전 빌드 결과 삭제 (build/ 폴더 제거)|
  |gradle compileJava|	Java 코드 컴파일|
  |gradle test|	테스트 실행|
  |gradle bootRun|	Spring Boot 애플리케이션 실행|
  |gradle dependencies|	프로젝트의 의존성 확인|
  |gradle --status|	실행 중인 Gradle 데몬 확인|
  
  

- #### Gradle vs Maven 차이점

  |비교 항목|Gradle|Maven|
  |:---:|:---:|:---:|
  |속도|빠름 (병렬 실행 + 캐싱 지원)|상대적으로 느림 (순차적 실행)|
  |빌드 스크립트|Kotlin DSL (build.gradle.kts) 또는 Groovy (build.gradle) 사용|XML (pom.xml) 사용|
  |유연성|커스텀 Task 작성 가능 (스크립트 기반)|표준적인 Lifecycle 기반 (커스텀화 제한적)|
  |의존성 관리|같은 저장소 사용 (Maven Central)|같은 저장소 사용 (Maven Central)|
  |학습 난이도|상대적으로 어려움 (DSL 문법 필요)|상대적으로 쉬움 (XML 기반, 직관적)|
  |사용 사례|대규모 프로젝트, Spring Boot, Kotlin, Android|전통적인 Java 프로젝트|
    

### (2) 스프링 부트의 기본 구성 요소

- ####  @SpringBootApplication, @Component, @Bean, @Configuration

 **`@SpringBootApplication`**
 
 Spring Boot 애플리케이션에서 가장 기본적으로 사용되는 어노테이션으로 3가지의 주요 어노테이션을 포함한다.
 
 1) `@SpringBootConfiguration`
  ‣ `@Configuration` 의 확장판으로, 이 클래스가 Spring의 설정 클래스임을 의미하고, Bean을 등록하거나 환경 설정을 정의할 수 있습니다.   
 		
 2) `@EnableAutoConfiguration`
     ‣  Spring Boot의 자동 설정(Auto Configuration) 기능을 활성화합니다. (원래는 어떤 데이터베이스를 사용할지 명시하고, 그에 맞는 설정을 해야함)
   ‣ 의존성에 등록한 Classpath(실행할 때 필요한 라이브러리 경로)를 확인하여 적절한 빈(Bean)을 자동으로 구성합니다.
   ‣ JPA를 추가하면 자동으로 DB연결을 위해`DataSource` 를 설정하고, `DispatcherServlet` 으로 요청을 해당하는 컨트롤러로 전달하고 응답한다.
  
 3) `@ComponentScan`
  ‣ 현재 패키지와 그 하위 패키지를 스캔하여 `@Component`, `@Service`, `@Repository`, `@Controller` 등을 자동으로 스프링 컨테이너에 등록합니다.
  ‣ `@SpringBootApplication` 이 선언된 패키지를 기준으로 탐색하므로, 최상위 패키지에 배치하는 것이 일반적입니다.
  
  
 **`@Component`**
  
  
  #### 기존의 방식
  
  
  ```java
  public class MyService {
     public void doSomething() {
         System.out.println("일을 수행 중...");
    }
  }
  
  public class MyApp {
     public static void main(String[] args) {
         MyService service = new MyService(); // 직접 객체 생성
         service.doSomething();
     }
  }
  ```
  
  
  #### @Component사용
  
  ```java
  @Component
   public class MyService {
       public void doSomething() {
            System.out.println("Spring이 관리하는 객체입니다!");
       }
   }

   public class MyApp {
       private final MyService myService;

       @Autowired  // MyService 객체를 자동으로 주입받음!
       public MyApp(MyService myService) {
           this.myService = myService;
       }

       public void run() {
           myService.doSomething();
       }
   }
  ```
  
  #### @component 특징
  
  ‣ Spring이 자동으로 객체를 생성하고 관리해줘서 개발자가 `new` 를 직접 쓰지 않아도 됨.

  ‣ `@Autowired` 로 쉽게 가져와서 쓸 수 있음 (의존성 주입이 가능함)

  ‣ Spring 컨테이너가 실행될 때 `@ComponentScan` 이 `@Component` 가 붙은 클래스를 자동으로 찾아서 등록함.
  
  #### @component와 비슷한 어노테이션
 |어노테이션|역할|
 |:---:|:---:|
|@Component|일반적인 Spring Bean을 등록|
|@Service|비즈니스 로직을 담당하는 클래스|
|@Repository|데이터베이스 작업을 담당하는 클래스|
|@Controller|웹 요청을 처리하는 클래스|
|@RestController|REST API 요청을 처리하는 클래스 (@Controller + @ResponseBody)|

 보통은 `@Service`, `@Repository`, `@Controller` 같은 더 구체적인 어노테이션을 쓰지만, 특정한 역할이 없는 일반적인 클래스라면 `@Component` 를 사용하면 된다.
 
 
 **`bean`**
 Spring이 대신 생성하고 관리하는 객체이다.
 
 위의 방법처럼 component에도 Bean이 포함되어 있으므로 component로 적는 방법과, 아래와 같이 Configuration으로 등록하는 방법도 있다. (둘 다 Autowired을 통해 자동으로 주입함)
 
 ```java
 @Configuration  // 이 클래스는 설정 클래스!
  public class AppConfig {
      @Bean  // 직접 Bean 등록
      public MyService myService() {
          return new MyService();
      }
  }
  ```

  ‣  **Bean의 기본 생명주기**
 1) Spring이 Bean을 생성함 (@Component 또는 @Bean 사용)
 2) 필요할 때 Bean을 사용함 (@Autowired 등으로 주입)
 3) Spring이 애플리케이션 종료 시 Bean을 정리함
 4) `@PostConstruct` → Bean이 생성될 때 실행
 5) `@PreDestroy` → Bean이 소멸될 때 실행
 
 
 #### `@Configuration`
 
 |개념|설명|
 |:---:|:---:|
|Configuration|설정 클래스 (Spring이 설정 정보로 인식)|
|Component|자동으로 Bean을 등록할 때 사용 (주로 서비스, 레포지토리, 컨트롤러)|
|Bean|@Configuration 내부에서 직접 Bean을 등록할 때 사용|

 #### 주요 설정클래스 정리
 
 |설정 클래스|설명|
 |:---:|:---:|
 |DB 설정 (DataSourceConfig)|데이터베이스 연결 관련 설정을 관리|
 |JPA 설정 (JpaConfig)|JPA 관련 설정 (EntityManager, Hibernate 설정)|
 |웹 설정 (WebConfig)|CORS, Interceptor, ViewResolver 등 웹 설정
 |시큐리티 설정 (SecurityConfig)|Spring Security를 사용한 인증/인가 설정|
 |서비스 설정 (ServiceConfig)|특정 서비스 관련 Bean 설정|
 |외부 라이브러리 설정 (ExternalConfig)|직접 수정할 수 없는 외부 라이브러리를 Bean으로 등록|


- #### 의존성 주입(DI) 이란 무엇인가?

 Spring에서는 객체가 다른 객체를 직접 생성하지 않고, 외부에서 필요한 객체를 주입받는 방식을 사용한다.
 
 |주입 방식|설명|특징|
 |:---:|:---:|:---:|
 |생성자 주입 (Constructor Injection)|생성자를 통해 주입|강추|
 |필드 주입 (Field Injection)|필드에 직접 주입|유지보수 어려움|
 |Setter 주입 (Setter Injection)|Setter 메서드를 통해 주입|필요한 경우 사용|
 
 
 
```java

//생성자 주입
@Component
public class App(){
	private final MyService myService;

    @Autowired  // 생성자 주입
    public MyApp(MyService myService) {
        this.myService = myService;
    }
}

// 필드에 직접 주입
@Component
public class App(){
	// 필드에 직접 주입
	private final MyService myService;
} 

```


- #### 스프링 부트 자동 설정 원리
Spring Boot는 클래스패스(Classpath)에 있는 라이브러리를 보고, 필요한 설정을 자동으로 적용함.
 

## 3. 자바

### 선택문과 반복문

|개념|설명|문법|
|:---:|:---:|:---:|
|if 문|	조건이 참일 때 실행|	if (조건) { 실행문; }
|if-else 문|	조건이 참/거짓일 때 실행|	if (조건) { 실행문; } else { 실행문; }
|if-else if-else 문|	여러 개의 조건 검사|	if (조건1) {...} else if (조건2) {...} else {...}
|switch-case 문|	특정 값에 따라 실행 분기|	switch (변수) { case 값: 실행문; break; ... }
|for 문|	횟수가 정해진 반복|	for (초기식; 조건식; 증감식) { 실행문; }
|while 문|	조건이 true면 반복|	while (조건) { 실행문; }
|do-while 문|	최소 1번 실행 후 조건 검사|	do { 실행문; } while (조건);
|for-each 문|	배열/컬렉션 반복|	for (타입 변수 : 배열) { 실행문; }
|break|	반복문 즉시 종료|	if (조건) break;
|continue|	다음 반복으로 건너뛰기|	if (조건) continue;


