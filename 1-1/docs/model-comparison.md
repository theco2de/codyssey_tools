# 산출물 1. LLM 모델 비교와 선정 보고서

> ✍️ 표시가 있는 칸은 **직접 작성**하는 칸이에요. (과제 제약: 모델 선정 이유는 본인 문장)

## 1. 과업

수강생이 올린 Spring 기술 질문(질문 본문 + 에러 로그/코드)을 받아,
조교가 그대로 다듬어 올릴 수 있는 **답변 초안**을 정해진 형식으로 만든다.

## 2. 비교 대상 모델

| 구분 | 서비스 | 모델 이름·버전 (화면 표기 그대로) | 요금제 | 실행 시점 |
|---|---|---|---|---|
| 모델 A | ChatGPT | GPT-6 | **무료** ✅ | 2026-11-24 15:37 |
| 모델 B | Gemini | Gemini 4.4 | **무료** ✅ | 2026-11-24 15:37 |
| 모델 C | Claude | Sonnet 5.5 | Pro | 2026-11-24 15:37 |

- 버전은 대화창 상단 모델 선택기에 보이는 이름을 그대로 적어요. 스크린샷을 `docs/img/`에 남기면 좋아요.
- **무료 요금제 모델 표시**: 위 표의 ✅ 표시 (최소 1개 이상 필요).

## 3. 비교 방법

- 세 모델 모두 **새 대화**를 열고, 지침(커스텀 인스트럭션)은 비운 상태에서 아래 공통 프롬프트를 **한 글자도 바꾸지 않고** 붙여 넣었다.
- 질문 3건(Q1~Q3)을 각각 새 대화에서 한 번씩 넣었다. → 모델당 3개 응답, 총 9개 응답.
- 이 단계는 **Zero-shot**이다(예시 없이 지시만). 설계 문서의 Zero-shot vs Few-shot 비교에도 이 결과를 재사용한다.

### 3-1. 공통 과업 프롬프트 (그대로 복사)

```text
너는 Spring 입문 과정의 조교야. 아래 수강생 질문에 대한 답변 초안을 써줘.

[수강생 수준] Java 기초와 Spring Boot로 간단한 REST API를 막 만들기 시작한 단계. JPA, Security는 아직 안 배움.
[수강생 환경] Windows, IntelliJ, Spring Boot 3.x, Java 17

[출력 형식]
1. 원인 추정 (1~2문장)
2. 확인 방법 (번호 목록, 최대 3단계)
3. 해결 힌트 (정답 코드를 통째로 주지 말고 어디를 고치면 되는지)
4. 함께 보면 좋은 개념 (1개)

[질문]
{아래 Q1~Q3 중 하나}
```

### 3-2. 비교용 질문 3건

**Q1. 포트 충돌**
```text
서버 실행하니까 이렇게 뜨고 꺼져요. 어제까지는 됐는데 뭐가 문제일까요?

***************************
APPLICATION FAILED TO START
***************************

Description:

Web server failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that's listening on port 8080 or configure this application to listen on another port.
```

**Q2. javax → jakarta**
```text
블로그 보고 필터를 만들어 보려고 import javax.servlet.Filter; 를 썼는데
IntelliJ에서 Cannot resolve symbol 'servlet' 이라고 빨간 줄이 떠요. 의존성 문제인가요?
```

**Q3. 같은 타입 빈 2개**
```text
할인 정책 인터페이스를 만들고 구현체를 2개 만들었는데 실행하면 이 에러가 나요.

org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.example.shop.discount.DiscountPolicy' available:
expected single matching bean but found 2: fixDiscountPolicy,rateDiscountPolicy
```

## 4. 비교 기준 (5개)

| 기준 | 무엇을 보나 | 5점 | 1점 |
|---|---|---|---|
| ① 형식 준수 | 1~4번 항목을 순서·개수 제한대로 지켰나 | 4항목 모두 지킴 | 형식 무시 |
| ② 사실 정확성 | 원인·해결이 기술적으로 맞나 (정답표 §5 대조) | 오류 0건 | 핵심 원인이 틀림 |
| ③ 수준 맞춤 | 안 배운 개념(JPA, Security 등)·과한 설명을 끌어오지 않았나 | 범위 안 | 범위 밖 개념이 해결의 중심 |
| ④ 힌트 우선 | 정답 코드를 통째로 주지 않고 고칠 위치를 짚었나 | 위치만 짚음 | 완성 코드 붙여 넣기 |
| ⑤ 환경 반영 | Windows·Boot 3·Java 17 조건을 반영했나 (예: Windows 명령, jakarta) | 모두 반영 | 다른 환경 기준(macOS 명령, javax 유지 등) |

## 5. 정답표 (채점용, 사전 작성)

| 질문 | 맞는 원인 | 맞는 해결 방향 | 틀린 답으로 볼 것 |
|---|---|---|---|
| Q1 | 8080 포트를 다른 프로세스(이전에 띄운 같은 앱 등)가 사용 중 | 기존 프로세스 종료(Windows: `netstat -ano \| findstr :8080` → `taskkill /PID <번호> /F`, 또는 IntelliJ에서 이전 실행 중지) 또는 `application.properties`에 `server.port` 변경 | 코드/의존성 문제라고 하는 것, macOS 명령(`lsof`)만 제시 |
| Q2 | Spring Boot 3는 Jakarta EE 9+ 기반이라 패키지가 `javax.servlet` → `jakarta.servlet`으로 바뀜 | import를 `jakarta.servlet.Filter`로 변경. 별도 의존성 추가 불필요(`spring-boot-starter-web`에 포함) | `javax.servlet-api` 의존성을 추가하라는 답 |
| Q3 | 같은 타입(`DiscountPolicy`) 빈이 2개라 주입 대상을 하나로 못 정함 | `@Primary`, `@Qualifier`, 또는 파라미터 이름 매칭 중 하나로 우선순위 지정 | 구현체 하나를 지우라는 것만 제시, `@Autowired(required=false)` 제안 |

## 6. 점수표

점수: 1~5, 클수록 좋음. 질문 3건의 평균(반올림)을 적고 근거에 질문별 차이를 적는다.

| 비교 기준 | 모델 A | 모델 B | 모델 C | 근거 |
|---|---|---|---|---|
| ① 형식 준수 | 4 | 5 | 4 | A: Q3 해결 힌트에서 "세 가지"라며 `@Qualifier`를 두 번 나열, 개념 칸이 여러 문장. C: Q2 원인이 3문장이라 "1~2문장" 초과. B: 4항목 순서·개수 모두 준수. |
| ② 사실 정확성 | 4 | 5 | 5 | 셋 모두 정답표의 "틀린 답"은 없음. A: Q3의 "세 가지"가 실제로는 두 가지이고 파라미터 이름 매칭이 빠짐. C: `javax.servlet-api` 추가 금지, `required=false`·구현체 삭제는 우회라는 점까지 짚음. |
| ③ 수준 맞춤 | 5 | 5 | 5 | JPA·Security를 끌어온 응답 없음. |
| ④ 힌트 우선 | 4 | 5 | 5 | A: Q1에서 `server.port=8081`을 그대로 제시. B·C: 코드 없이 위치만 짚음. |
| ⑤ 환경 반영 | 5 | 5 | 5 | 셋 모두 Windows 명령과 jakarta 반영. B는 IntelliJ 단축키, C는 Java 17 언급. |
| **합계** | **22** | **25** | **24** | |

- 질문별 차이는 거의 없었다. 감점은 대부분 Q2(C의 원인 문장 수)와 Q3(A의 방법 나열)에서 나왔다.
- 점수에 반영되지 않은 관찰: B는 Q1의 `findstr 8080`에 콜론이 없고 Q3 해결 힌트가 추상적이다. 어투는 A·B가 합니다체, C가 해요체다.

## 7. 모델별 원문 응답

> 응답은 **편집하지 않고** 그대로 붙인다. 길면 `docs/raw/` 아래 파일로 두고 링크해도 된다.

<details><summary>모델 A — Q1 / Q2 / Q3</summary>

```text
Q1. 포트 충돌 (Port 8080 already in use)
1. 원인 추정
Spring Boot가 기본으로 사용하는 8080 포트를 다른 프로그램이 이미 사용하고 있어서 서버가 실행되지 않는 상황입니다. 이전에 실행한 Spring Boot 서버가 종료되지 않고 백그라운드에 남아 있거나, 다른 프로그램이 같은 포트를 사용하고 있을 가능성이 있습니다.
2. 확인 방법
1. IntelliJ의 Run 창을 확인해 이전에 실행한 Spring Boot 서버가 아직 실행 중인지 확인하고, 실행 중이라면 Stop 버튼으로 종료해 보세요.
2. Windows CMD에서 다음 명령어를 실행해 8080 포트를 사용하는 프로세스의 PID를 확인하세요.
   netstat -ano | findstr :8080
3. 마지막에 표시된 PID를 이용해 어떤 프로그램인지 확인하세요.
   tasklist | findstr <PID>
3. 해결 힌트
두 가지 해결 방법이 있습니다.
- 기존 서버가 원인이라면 IntelliJ의 Stop 버튼으로 종료하세요.
- 다른 프로그램이 포트를 사용하고 있다면 src/main/resources/application.properties에서 포트를 변경해 보세요.
  server.port=8081
  
  
  단, PID를 확인했다고 무조건 강제 종료하지 말고 어떤 프로그램인지 먼저 확인하세요.
4. 함께 보면 좋은 개념
포트(Port)와 프로세스(Process)

포트는 네트워크 통신에서 프로그램을 구분하는 번호이고, 프로세스는 현재 실행 중인 프로그램입니다. 같은 IP 주소와 포트 조합으로 여러 서버가 동시에 실행될 수 없다는 점을 기억하세요.

Q2. javax → jakarta (Cannot resolve symbol 'servlet')
1. 원인 추정
Spring Boot 3.x부터는 Java EE 기반의 javax.servlet 패키지 대신 Jakarta EE 기반의 jakarta.servlet 패키지를 사용합니다. 따라서 블로그의 예제가 Spring Boot 2.x 기준이라면 기존 import 문을 그대로 사용했을 때 오류가 발생할 수 있습니다.
2. 확인 방법
1. build.gradle 또는 pom.xml에서 Spring Boot 버전이 3.x인지 확인하세요.
2. 필터 클래스의 import 문이 javax.servlet.Filter로 되어 있는지 확인하세요.
3. 프로젝트에 Spring Web 의존성이 포함되어 있는지 확인하세요. Gradle이라면 spring-boot-starter-web 의존성을 확인하면 됩니다.
3. 해결 힌트
우선 필터 클래스의 import 문부터 확인해 보세요.
- 기존 javax.servlet을 jakarta.servlet로 변경해야 합니다.
- Filter, ServletRequest, ServletResponse, FilterChain 등 관련 클래스도 Jakarta 패키지를 사용해야 합니다.
- 만약 변경 후에도 빨간 줄이 남아 있다면 build.gradle의 의존성을 확인하고 IntelliJ에서 Gradle Reload를 실행해 보세요.
주의: Spring Boot 3.x 프로젝트에서는 기존 블로그의 javax.servlet 코드를 그대로 복사하면 안 됩니다. 패키지 이름뿐 아니라 일부 API 사용법도 달라졌을 수 있습니다.
4. 함께 보면 좋은 개념
Jakarta EE와 Java EE의 패키지 변경

Java EE가 Eclipse Foundation으로 이전되면서 Jakarta EE로 이름이 바뀌었고, 이후 패키지 이름도 javax.*에서 jakarta.*로 변경되었습니다.

Spring Boot 3.x를 학습할 때는 Jakarta 기반의 예제를 참고하는 습관을 들이세요.

Q3. 같은 타입 빈 2개 (NoUniqueBeanDefinitionException)
1. 원인 추정
Spring 컨테이너에 DiscountPolicy 타입의 빈이 2개 등록되어 있는데, Spring이 어떤 빈을 주입해야 할지 결정하지 못해서 발생한 오류입니다. 현재 fixDiscountPolicy와 rateDiscountPolicy 두 구현체가 모두 등록되어 있기 때문에 어느 것을 선택해야 할지 모르는 상황입니다.
2. 확인 방법
1. DiscountPolicy 인터페이스를 구현한 클래스가 몇 개인지 확인하세요.
2. 각 구현 클래스에 @Component 또는 @Service 같은 어노테이션이 붙어 있는지 확인하세요. 컴포넌트 스캔으로 두 구현체가 모두 빈으로 등록되었을 수 있습니다.
3. DiscountPolicy를 생성자 주입받는 클래스에서 어떤 구현체를 사용하려고 하는지 확인하세요.
3. 해결 힌트
Spring에게 어떤 빈을 사용할지 명확하게 알려주는 방법이 있습니다.

대표적으로 다음 세 가지 방법을 알아두세요.
- @Primary: 특정 구현체를 기본 주입 대상으로 지정합니다.
- @Qualifier: 주입받는 위치에서 사용할 빈의 이름을 지정합니다.
- 빈 이름으로 직접 지정: @Qualifier를 활용해 원하는 구현체를 선택할 수 있습니다.
예를 들어, 할인 정책 중 정률 할인 정책을 기본으로 사용하고 싶다면 해당 구현 클래스에 @Primary를 적용하는 방법을 생각해 보세요.

반대로 특정 서비스에서만 정액 할인 정책을 사용하고 싶다면 생성자 주입 부분에 @Qualifier를 적용하는 방법을 고려해 보세요.
4. 함께 보면 좋은 개념
의존성 주입(DI, Dependency Injection)

Spring은 객체가 필요한 의존 객체를 직접 생성하지 않고, Spring 컨테이너가 대신 생성하고 주입해 줍니다.

같은 타입의 빈이 여러 개 등록되어 있으면 Spring이 어떤 객체를 주입할지 결정해야 합니다.

이때 @Primary와 @Qualifier를 사용하면 원하는 구현체를 명확하게 선택할 수 있습니다.
```
</details>

<details><summary>모델 B — Q1 / Q2 / Q3</summary>

```text
Q1. 포트 충돌
1. 원인 추정
어제 실행했던 Spring Boot 서버나 다른 프로그램이 완전히 종료되지 않고 8080 포트를 계속 점유하고 있어서 새 서버가 해당 포트로 뜨지 못한 상황입니다. Windows에서 IntelliJ를 강제 종료했거나 이전 실행 프로세스가 백그라운드에 남아 있을 때 자주 발생합니다.

2. 확인 방법
IntelliJ 하단 Run 탭에서 실행 중인 이전 서버 프로세스가 있는지 먼저 확인하세요.

Windows 명령 프롬프트(cmd 또는 PowerShell)를 열고 netstat -ano | findstr 8080을 입력해 8080 포트를 점유 중인 프로세스의 PID(맨 오른쪽 숫자)를 확인하세요.

확인한 PID가 어떤 프로그램인지 tasklist /FI "PID eq <확인한_PID>"로 살펴보세요.

3. 해결 힌트
기존 프로세스 종료: 위에서 찾은 PID를 기준으로 Windows 작업 관리자에서 해당 프로세스를 끝내거나, 터미널에서 프로세스 종료 명령(taskkill)을 사용해 8080 포트를 비운 뒤 다시 실행해 보세요.

포트 번호 변경: 당장 다른 포트를 쓰려면 Spring Boot 설정 파일(src/main/resources 아래의 설정 파일)에서 서버 포트 관련 속성 값을 다른 번호로 바꿔볼 수도 있습니다.

4. 함께 보면 좋은 개념
TCP 포트(Port)와 프로세스(PID): 한 컴퓨터 안에서 여러 네트워크 프로그램이 서로 충돌하지 않고 통신하기 위해 사용하는 번호 체계와, 실행 중인 각 프로그램을 구분하는 고유 번호의 관계입니다.

Q2. javax → jakarta
1. 원인 추정
의존성 누락이라기보다 Spring Boot 3.x부터 서블릿 API 패키지가 javax.servlet에서 jakarta.servlet으로 변경되었기 때문입니다. 오래된 블로그 글의 javax import 구문을 그대로 쓰면서 IntelliJ가 심볼을 찾지 못한 것으로 보입니다.

2. 확인 방법
build.gradle 또는 pom.xml에 Spring Boot 웹 스타터(spring-boot-starter-web)가 정상적으로 추가되어 있는지 확인하세요.

필터 클래스 상단의 import 문이 javax.servlet...으로 시작하는지 살펴보세요.

IntelliJ에서 jakarta.servlet.을 입력했을 때 자동 완성 목록에 Filter가 뜨는지 확인하세요.

3. 해결 힌트
필터 클래스 상단의 import 패키지 경로를 javax 계열에서 jakarta 계열로 바꿔 보세요.

기존 javax import 줄을 지운 뒤, Filter 클래스명 위에서 IntelliJ 자동 임포트 단축키(Alt + Enter)를 눌러 jakarta.servlet.Filter를 선택해 보세요.

4. 함께 보면 좋은 개념
Java EE에서 Jakarta EE로의 네임스페이스 전환: Spring Boot 2.x(javax.*)와 Spring Boot 3.x(jakarta.*)를 구분할 때 반드시 마주치는 패키지 명칭 변경 배경입니다.

Q3. 같은 타입 빈 2개
1. 원인 추정
DiscountPolicy 인터페이스를 구현한 빈(fixDiscountPolicy, rateDiscountPolicy)이 2개 등록되어 있는데, 주입받는 쪽에서 어떤 빈을 선택해야 할지 Spring 컨테이너가 결정하지 못해 발생한 에러입니다. 단일 빈 주입을 기대하는 지점에 후보가 2개라 충돌이 난 상황입니다.

2. 확인 방법
에러 메시지에 나온 fixDiscountPolicy, rateDiscountPolicy 두 구현체 클래스에 @Component 또는 @Service 같은 빈 등록 애노테이션이 붙어 있는지 확인하세요.

DiscountPolicy 타입을 주입받고 있는 필드, 생성자, 또는 @Bean 메서드가 어디에 있는지 찾아보세요.

주입받는 곳에서 특정 구현체 하나만 쓰도록 지정했는지 확인하세요.

3. 해결 힌트
DiscountPolicy를 주입받는 위치(생성자/필드/설정 메서드)에서 특정 빈 이름을 지정하거나, 구현체 클래스 쪽에 우선순위 또는 구분용 애노테이션을 붙여 어떤 빈을 먼저 선택할지 알려줘 보세요.

두 구현체 모두 당장 필요한 상태인지 점검하고, 주입받는 쪽에서 어떤 정책을 사용할지 명확히 지정해 보세요.

4. 함께 보면 좋은 개념
Spring 빈 조회와 의존관계 자동 주입 충돌 해결(@Primary, @Qualifier, 빈 이름 매칭): 같은 타입의 빈이 여러 개일 때 Spring이 하나를 고르는 기준과 우선순위 규칙입니다.
```
</details>

<details><summary>모델 C — Q1 / Q2 / Q3</summary>

```text
Q1. 포트 8080 충돌

1. 원인 추정
8080 포트를 이미 다른 프로세스가 쓰고 있어서 서버가 못 떠요. 어제 실행한 같은 앱이 아직 종료되지 않았을 가능성이 가장 커요.

2. 확인 방법
1. IntelliJ 하단 Run 창에서 빨간 정지 버튼이 활성화된 실행 항목이 남아 있는지 보세요.
2. 명령 프롬프트(cmd)에서 netstat -ano | findstr :8080을 실행하세요. 맨 오른쪽 숫자가 그 포트를 쓰는 PID예요.
3. 작업 관리자 → 세부 정보 탭에서 그 PID가 java.exe인지 확인하세요.

3. 해결 힌트
- 이전 실행이 남아 있다면 IntelliJ에서 정지하거나, 확인한 PID를 taskkill로 종료하세요.
- 다른 프로그램이 8080을 꼭 써야 한다면 application.properties에서 서버 포트 설정을 바꾸세요. 이 경우 접속 주소의 포트도 같이 바뀌어요.
- 코드나 의존성 문제가 아니니 코드는 건드리지 않아도 돼요.

4. 함께 보면 좋은 개념
내장 톰캣과 서버 포트: Spring Boot 앱이 실행될 때 포트를 하나 점유하는 방식이에요.

---

Q2. javax → jakarta

1. 원인 추정
의존성 문제가 아니에요. Spring Boot 3.x는 Jakarta EE 기반이라 패키지 이름이 javax.servlet에서 jakarta.servlet으로 바뀌었어요. 블로그 글이 Boot 2.x 기준일 가능성이 높아요.

2. 확인 방법
1. build.gradle(또는 pom.xml)에서 Spring Boot 버전이 3.x인지 확인하세요.
2. spring-boot-starter-web이 들어 있는지 확인하세요.
3. IntelliJ 외부 라이브러리 목록에서 jakarta.servlet 관련 jar가 있는지 보세요.

3. 해결 힌트
- import의 javax를 jakarta로 바꾸세요. 다른 부분은 그대로 두면 돼요.
- javax.servlet-api 의존성을 따로 추가하지 마세요. starter-web에 이미 포함돼 있고, 추가하면 오히려 충돌할 수 있어요.
- 같은 블로그의 다른 javax.* import도 같은 방식으로 확인해 보세요.

4. 함께 보면 좋은 개념
Spring Boot 2 → 3 마이그레이션에서 달라진 점(javax → jakarta, Java 17 이상 필수).

---

Q3. NoUniqueBeanDefinitionException

1. 원인 추정
DiscountPolicy 구현체가 둘 다 빈으로 등록돼 있어요. 주입받는 쪽에서 둘 중 어느 것을 쓸지 정할 수 없어서 에러가 나요.

2. 확인 방법
1. 두 구현체 클래스에 @Component(또는 @Service)가 붙어 있는지 확인하세요.
2. 주입받는 생성자나 필드의 타입이 인터페이스인지 확인하세요.
3. 에러 메시지의 fixDiscountPolicy, rateDiscountPolicy가 각각 어느 클래스인지 대응해 보세요.

3. 해결 힌트
기본으로 쓸 구현체를 정하는 방법은 세 가지예요. 하나만 고르세요.
- 기본값으로 쓸 구현체 클래스에 @Primary를 붙이세요.
- 주입받는 곳에서 @Qualifier로 빈 이름을 지정하세요.
- 주입 파라미터 이름을 빈 이름과 맞추세요.

구현체 하나를 지우는 것은 해결이 아니라 우회예요. required=false도 이 에러를 가릴 뿐이라 권하지 않아요.

4. 함께 보면 좋은 개념
의존성 주입(DI)의 빈 선택 규칙: 타입으로 먼저 찾고, 여러 개면 우선순위나 이름으로 좁혀요.
```
</details>

## 8. 선정 결론

**선정 모델**: Claude (Sonnet 5.5)

**선정 근거** (직접 작성 — 점수 합계만이 아니라 "어떤 약점은 프롬프트로 고칠 수 있고, 어떤 약점은 못 고친다고 봤는지"를 적어요):

세 모델 모두 정답표의 "틀린 답"은 내지 않았고 JPA·Security 같은 범위 밖 개념도 끌어오지 않았다. 점수 합계는 Gemini 25, Claude 24, GPT 22로 Gemini가 1점 앞서지만, 그 차이는 Claude의 Q2 원인 문장 수 초과(3문장) 하나에서 나왔다. 그래서 합계보다 "남은 약점이 프롬프트로 고쳐지는가"를 기준으로 Claude를 골랐다.

- **프롬프트로 고칠 수 있다고 본 약점**
  - Claude의 원인 문장 수 초과: 형식 제약이 느슨해서 생긴 문제라, 지침에 "원인은 반드시 2문장 이내"를 명시하면 잡힌다고 봤다.
  - GPT의 길고 장황한 개념 칸, 코드 한 줄(`server.port=8081`) 제시: 분량 제한과 "코드 금지" 지시로 줄일 수 있다. 다만 GPT는 같은 항목을 중복 나열하는 모습도 있어서 지시를 더 많이 넣어야 한다.
  - 어투: Claude는 해요체가 기본으로 나왔고, Gemini·GPT는 합니다체라서 페르소나(스프링 조교)에 맞추려면 지침이 따로 필요하다.
- **프롬프트로 고치기 어렵다고 본 약점**
  - Gemini의 Q3 해결 힌트가 "특정 빈 이름 지정", "구분용 애노테이션"처럼 추상적이었다. 힌트를 주되 정답 코드는 주지 말라는 지시와 충돌하는 부분이라, 구체성과 힌트 우선 사이의 균형을 지침으로 맞추기 어렵다고 봤다.
  - Gemini의 Q1 `findstr 8080`(콜론 없음)처럼 명령어 세부가 부정확한 문제는 지침이 아니라 모델의 정확성 문제라서 고칠 수 없다고 봤다.
- **Claude를 고른 결정적 이유**: 틀린 답으로 빠질 만한 지점을 먼저 막아 줬다는 점이다. Q2에서 `javax.servlet-api` 의존성 추가 금지, Q3에서 `required=false`와 구현체 삭제는 우회라는 설명이 그렇다. 답변 초안을 조교가 그대로 올리는 과업에서는 이런 경고가 수강생의 오답 경로를 줄여 준다.
- **한계**: 질문당 한 번씩만 실행했고 시스템 프롬프트 없이 비교했다. 응답은 실행마다 달라질 수 있어서, 이후 24턴 시나리오에서 Claude의 일관성을 다시 확인해야 한다.
