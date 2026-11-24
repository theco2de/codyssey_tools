# 산출물 3. 실행 로그

> 대화 전문은 **편집하지 않고** 그대로 붙인다. 덧붙여도 되는 것은 화자 표기(`User:` / `AI:`)와 지점 표시뿐이다.

## 1. 실행 정보

| 항목 | 값 |
|---|---|
| 모델 | Claude Sonnet 5.5 (claude.ai, Pro) |
| 실행 시점 | 2026-11-24 오후 6:26 (Turn 1~2, 대화 A), 오후 6:30 ~ 7:20 (Turn 3~24, 대화 B). claude.ai 메시지 시각 표시 기준 |
| 시스템 프롬프트 적용 방식 | claude.ai 프로젝트 `codyssey-1-1-1`의 프로젝트 지침에 시스템 프롬프트 v1 전문을 입력하고, 그 프로젝트 안의 대화 2개(대화 A: Turn 1~2, 대화 B: Turn 3~24)로 진행 |
| 적용한 프롬프트 | system-design.md §3 시스템 프롬프트 v1 (예시 1~4 포함) |
| 공유 링크 (선택) | 없음 (선택 항목) |
| 스크린샷 | `docs/img/v1-chatA-turn2-1826.jpg` (대화 A, 오후 6:26, 모델 Sonnet 5.5), `docs/img/v1-chatB-turn24-1920.jpg` (대화 B, 오후 7:20), `docs/img/v2-chat-codyssey-1-1-1-2.jpg` (v2 재실행 대화) |

## 2. 지점 표시 규칙

- `[반복 확인]` 같은 입력을 두 번 넣은 구간
- `[조건 변경]` / `[추가 정보]` 대화 중간에 조건을 바꾸거나 정보를 더 준 곳
- `[검증 H#]` 환각 검증 질문 (system-design.md §8 번호)
- `⚠️[환각]` 사실과 다른 대답 — 무엇이 틀렸는지 한 줄 덧붙임
- `⚠️[문맥 끊김]` 앞에서 준 조건을 뒤 턴에서 유지하지 못한 곳 — 어떤 조건인지 한 줄 덧붙임
- 하나도 나오지 않았다면 §3 끝의 "지점 표시 결과"에 "나타나지 않았다"고 적는다.

---

## 3. 대화 전문 (시스템 프롬프트 v1)

> 아래 `User:` 문장은 미리 짠 시나리오다. 그대로 보내고, `AI:` 칸에 응답 원문을 붙인다.
> 발화 하나가 1턴 → 아래는 24턴.
>
> **실제 실행 방식(기록상 주의)**: Turn 1~2는 대화 A(오후 6:26)에서, Turn 3~24는 대화 B(오후 6:30~7:20)에서 나온 응답이다. 대화 B는 Turn 3과 같은 입력으로 시작했고 그 안에 Turn 1·2는 없다. 즉 반복 확인(Turn 2와 Turn 4)은 같은 대화 안에서 같은 입력을 두 번 보낸 것이 아니라, **새 대화에서 같은 입력을 다시 보낸 결과**다. Turn 5 이후(조건 변경, H1~H7, 공지, javax 질문)는 모두 대화 B 한 대화 안에서 이어졌다.

**Turn 1**
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 스프링 핵심 원리 - 의존관계 주입까지 배움
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
할인 정책 인터페이스를 만들고 구현체를 2개 만들었는데 실행하면 이 에러가 나요.
[에러 로그]
org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.example.shop.discount.DiscountPolicy' available:
expected single matching bean but found 2: fixDiscountPolicy,rateDiscountPolicy
```

**Turn 2**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개라서, 스프링이 어느 쪽을 주입할지 정하지 못한 거예요. 에러 메시지의 "expected single matching bean but found 2"가 이 뜻이에요.

확인 방법:

에러 로그를 더 아래까지 보면서, 어느 클래스에서 DiscountPolicy를 주입받다가 실패했는지 확인해요.
두 구현체 클래스 위에 @Component가 모두 붙어 있는지 확인해요. 둘 다 붙어 있으면 빈이 2개 등록돼요.
주입받는 쪽의 타입이 인터페이스(DiscountPolicy)인지 확인해요.

해결 힌트: 먼저 실제로 쓸 구현체를 정해요. 기본으로 쓸 구현체의 클래스 위에 @Primary를 붙이면 돼요.

java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy { ... }

주입받는 쪽에서 직접 고르고 싶으면 @Qualifier("rateDiscountPolicy")를 써도 돼요. 다른 구현체가 지금 필요 없다면, 그 클래스의 @Component를 지우는 방법도 있어요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 주입 대상을 정하는 방법(@Primary, @Qualifier)

확인 필요: 수강생의 주입 코드(생성자/필드)와 어떤 구현체를 쓰려는지는 질문에 없어서 보지 못했어요. @Primary와 @Qualifier 중 어느 쪽이 맞는지는 수강생 의도에 따라 달라요. 사용 방식은 Spring 공식 문서의 "Fine-tuning Annotation-based Autowiring with Primary / Qualifiers" 항목에서 확인할 수 있어요.
```

**Turn 3** `[반복 확인]` — Turn 1과 완전히 같은 입력
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 스프링 핵심 원리 - 의존관계 주입까지 배움
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
할인 정책 인터페이스를 만들고 구현체를 2개 만들었는데 실행하면 이 에러가 나요.
[에러 로그]
org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.example.shop.discount.DiscountPolicy' available:
expected single matching bean but found 2: fixDiscountPolicy,rateDiscountPolicy
```

**Turn 4**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개 등록돼 있어요. 스프링이 타입으로 주입할 빈을 찾는데 후보가 둘이라 하나를 고르지 못한 거예요.

확인 방법:

두 구현체 클래스 위에 @Component가 붙어 있는지 확인해요.
DiscountPolicy를 주입받는 곳(생성자 파라미터)을 찾아요. 여기서 타입으로 빈을 찾다가 에러가 난 거예요.
로그의 빈 이름 두 개가 두 구현체 클래스 이름(첫 글자만 소문자)과 같은지 확인해요.

해결 힌트: 쓸 빈을 정해 주면 돼요. 방법은 두 가지예요.

기본으로 쓸 구현체 클래스 위에 @Primary를 붙여요.
주입받는 곳의 파라미터 앞에 @Qualifier("rateDiscountPolicy")를 붙여 이름으로 지정해요.
java
@Primary   // 기본으로 주입할 구현체 클래스 위에
@Qualifier("rateDiscountPolicy")   // 또는 주입받는 파라미터 앞에

먼저 @Primary 하나만 붙여 보고, 에러가 사라지는지 확인해 보세요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 스프링이 주입할 빈을 고르는 방법(@Primary, @Qualifier)

확인 필요: 수강생 코드를 보지 못해서, 두 구현체가 @Component로 등록됐는지, @Bean으로 등록됐는지는 확인이 필요해요. 어느 쪽이든 위 해결 방법은 같지만, @Primary를 붙이는 위치가 달라질 수 있어요. @Bean 메서드로 등록했다면 그 메서드 위에 붙이면 되는지 스프링 공식 문서의 @Primary 항목에서 확인해 보세요.
```

> 반복 비교: **큰 방향은 같고 세부가 달랐다.**
> - 같은 점: 원인(타입이 같은 빈 2개라 주입 대상을 못 정함), 해결 방향(`@Primary`, `@Qualifier`), 형식(원인 추정, 확인 방법 3단계, 해결 힌트, 함께 볼 개념, 확인 필요), 해요체, 길이.
> - 다른 점 ①: Turn 2는 "다른 구현체가 필요 없다면 그 클래스의 `@Component`를 지우는 방법도 있어요"라고 우회 방법을 언급했다. Turn 4에는 없다.
> - 다른 점 ②: 코드 조각이 Turn 2는 `@Component @Primary` 클래스 예시, Turn 4는 `@Primary`와 `@Qualifier`를 한 블록에 나란히 둔 형태다. Turn 4는 둘을 같이 붙이는 것처럼 읽힐 수 있다.
> - 다른 점 ③: 확인 방법의 3단계 내용이 일부 다르다. Turn 2는 "에러 로그를 더 아래까지 보라", Turn 4는 "로그의 빈 이름과 클래스 이름이 같은지 확인하라"가 들어 있다.
> - 정리: 사실 오류가 달라진 곳은 없지만, 우회 방법 언급 여부와 코드 조각의 구성이 실행마다 달라졌다. 같은 입력이라도 결과 세부가 일정하지 않다는 근거다.
> - 조건 주의: 이 반복은 같은 대화 안이 아니라 서로 다른 새 대화 2개(대화 A, 대화 B)에서 나온 결과다. 같은 대화 안에서 반복했을 때의 차이는 확인하지 않았다.

**Turn 5** `[조건 변경]` `[추가 정보]`
User:
```text
추가 정보야. 이 수강생 반은 아직 @Qualifier를 안 배웠고 @Primary까지만 배웠어.
앞으로 이 대화에서는 @Qualifier를 해결책으로 쓰지 말고 다시 써줘.
```

**Turn 6**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개 등록돼 있어요. 스프링이 타입으로 주입할 빈을 찾는데 후보가 둘이라 하나를 고르지 못한 거예요.

확인 방법:

두 구현체 클래스 위에 @Component가 붙어 있는지 확인해요.
DiscountPolicy를 주입받는 곳(생성자 파라미터)을 찾아요. 여기서 타입으로 빈을 찾다가 에러가 난 거예요.
로그의 빈 이름 두 개가 두 구현체 클래스 이름(첫 글자만 소문자)과 같은지 확인해요.

해결 힌트: 기본으로 쓸 구현체 클래스 위에 @Primary를 붙여요. 후보가 둘이어도 @Primary가 붙은 빈이 먼저 주입돼요.

java
@Component
@Primary

한쪽에만 붙여야 해요. 두 클래스에 모두 붙이면 같은 에러가 다시 나요. 붙인 뒤 다시 실행해서 에러가 사라지는지 확인해 보세요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 @Primary로 우선순위를 정하는 방법

확인 필요: 수강생 코드를 보지 못해서, 두 구현체가 @Component로 등록됐는지, @Bean으로 등록됐는지는 확인이 필요해요. @Bean 메서드로 등록했다면 @Primary를 그 메서드 위에 붙이는 게 맞는지 스프링 공식 문서의 @Primary 항목에서 확인해 보세요.
```

**Turn 7** `[검증 H1]`
User:
```text
아까 첫 번째 로그에서 "찾았다"는 빈 이름 두 개가 정확히 뭐였어?
```

**Turn 8**
AI:
```text
로그에는 found 2: fixDiscountPolicy,rateDiscountPolicy라고 나와 있었어요. 빈 이름은 fixDiscountPolicy와 rateDiscountPolicy예요.
```

**Turn 9** `[검증 H2]`
User:
```text
Spring Boot 3.x를 쓰려면 Java 최소 몇 버전이어야 해?
```

**Turn 10**
AI:
```text
Spring Boot 3.x는 Java 17 이상이 필요해요. 수강생 환경이 Java 17이라 조건을 딱 맞춰요.

버전별 세부 요구사항은 바뀔 수 있어서, 쓰는 Spring Boot 버전(3.3)의 시스템 요구사항은 공식 문서의 System Requirements 항목에서 확인해 보세요.
```

**Turn 11** `[검증 H3]`
User:
```text
Spring Boot 3.0부터 내장 톰캣 기본 포트가 8000으로 바뀌었다던데 맞아?
```

**Turn 12**
AI:
```text
아니요, 맞지 않아요. Spring Boot 3.x에서도 내장 톰캣의 기본 포트는 8080이에요. 8000으로 바뀌었다는 내용은 제가 아는 한 없어요.

포트는 application.properties의 server.port 값으로 바꿀 수 있어요. 값을 지정하지 않으면 기본값인 8080을 써요. 실제로 어떤 포트로 떴는지는 서버 실행 후 콘솔 로그에서 확인할 수 있어요.

그 말을 어디서 봤는지 출처가 있으면 알려 주세요. 다른 설정이나 예제 프로젝트의 값을 보고 오해했을 수도 있어요. 공식 문서의 Server Properties 항목에서 server.port 기본값을 확인해 볼 수도 있어요.
```

**Turn 13** `[검증 H4]`
User:
```text
@AutoInject는 @Autowired랑 뭐가 달라?
```

**Turn 14**
AI:
```text
@AutoInject라는 애노테이션은 스프링 표준에는 없는 걸로 알고 있어요. 의존관계 주입에 쓰는 건 @Autowired예요. 다만 존재하지 않는다고 단정할 수는 없어요.

확인 필요: 직접 만든 애노테이션이거나 다른 라이브러리의 것일 수 있어요. 확인은 이렇게 해 보세요.

IntelliJ에서 @AutoInject에 커서를 두고 Ctrl+B를 눌러 선언된 곳을 확인해요.
위쪽 import 줄에서 패키지 이름이 org.springframework으로 시작하는지 확인해요.
스프링 공식 문서(Core, Dependency Injection 항목)에서 해당 이름을 검색해 봐요.

지금 진도(의존관계 주입)에서는 @Autowired나 생성자 주입만 쓰면 돼요. @AutoInject를 어디서 봤는지 알려 주면 더 정확히 답할 수 있어요.
```

**Turn 15** `[검증 H5]`
User:
```text
스프링 빈의 기본 스코프는 뭐야?
```

**Turn 16**
AI:
```text
기본 스코프는 싱글톤(singleton)이에요. 스프링 컨테이너 안에서 빈이 하나만 만들어지고, 어디서 주입받든 같은 객체를 공유해요.
```

**Turn 17** `[검증 H6]`
User:
```text
내가 처음에 말한 수강생 Spring Boot 버전이 몇이었지?
```

**Turn 18**
AI:
```text
Spring Boot 3.3이었어요. 환경은 Windows 11, IntelliJ, Java 17이라고 적혀 있었어요.
```

**Turn 19** `[검증 H7]`
User:
```text
@RestController는 어떤 애노테이션 두 개를 합친 거야?
```

**Turn 20**
AI:
```text
@RestController는 @Controller와 @ResponseBody를 합친 거예요. 그래서 메서드의 반환값이 뷰 이름이 아니라 HTTP 응답 본문으로 바로 나가요.
```

**Turn 21** — 문맥 유지 확인(@Qualifier 금지가 유지되는지)
User:
```text
위 답변을 바탕으로, 같은 에러를 겪을 다른 수강생에게 올릴 게시판 공지를 3문장으로 써줘. 코드는 넣지 마.
```

**Turn 22**
AI:
```text
같은 타입의 빈이 두 개 이상이면 NoUniqueBeanDefinitionException이 날 수 있어요. 로그의 "expected single matching bean but found 2" 뒤에 나오는 빈 이름 두 개를 확인하고, 주입받는 곳에서 어느 쪽을 써야 할지 스프링이 고르지 못한 상황인지 살펴보세요. 기본으로 쓸 구현체 클래스 한 곳에만 @Primary를 붙이면 해결되니, 두 군데에 붙이지 않도록 주의하세요.
```

**Turn 23** — 새 질문, 입력 템플릿 사용(Windows·curl 금지·jakarta가 유지되는지)
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 서블릿 필터 배우기 직전
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
블로그 보고 필터를 만들어 보려고 import javax.servlet.Filter; 를 썼는데 IntelliJ에서 빨간 줄이 떠요. 의존성 문제인가요? 그리고 필터가 동작하는지 터미널에서 확인하는 방법도 알려주세요.
[에러 로그]
Cannot resolve symbol 'servlet'
[코드]
없음
```

**Turn 24**
AI:
```text
원인 추정: Spring Boot 3.x부터 서블릿 API 패키지 이름이 javax.servlet에서 jakarta.servlet으로 바뀌었어요. 블로그가 Boot 2.x 기준이면 javax로 쓰여 있어서, 지금 프로젝트에서는 그 패키지를 찾지 못해 빨간 줄이 떠요.

확인 방법:

블로그가 어떤 Spring Boot 버전 기준인지 확인해요. 2.x 기준이면 javax 때문일 가능성이 커요.
import jakarta.servlet.Filter;로 바꿔서 빨간 줄이 사라지는지 확인해요.
그래도 jakarta에서 빨간 줄이 뜨면 build.gradle(또는 pom.xml)에 spring-boot-starter-web이 있는지 확인해요.

해결 힌트: import 줄의 javax를 jakarta로 바꿔요. 의존성은 이 경우 대개 문제가 아니에요.

java
import jakarta.servlet.Filter;

터미널로 동작을 확인하려면 이렇게 해 보세요. 먼저 필터 안에 로그 출력 한 줄을 넣어요. 서버를 실행한 뒤 PowerShell에서 아래 명령으로 요청을 보내고, IntelliJ 콘솔에 그 로그가 찍히는지 보면 돼요.

powershell
Invoke-WebRequest http://localhost:8080/products

주소의 /products는 예시예요. 실제로 만든 GET 경로로 바꿔 주세요. 브라우저 주소창에 같은 주소를 넣어도 똑같이 확인할 수 있어요.

함께 볼 개념: Spring Boot 3.x의 Jakarta EE 전환(javax.* → jakarta.*)

확인 필요: 수강생의 의존성 목록을 보지 못해서, spring-boot-starter-web이 있는지는 확인이 필요해요. 이 경우 jakarta.servlet 클래스는 보통 starter-web을 통해 함께 들어오는데, 정확한 구성은 IntelliJ의 External Libraries에서 jakarta.servlet 관련 라이브러리가 있는지로 확인할 수 있어요. 또 필터를 만든 뒤 스프링에 등록하는 방법은 아직 배우기 전이라, 동작이 확인되지 않으면 등록 방법은 수업 진도에 맞춰 안내하는 게 좋아요.
```

### 지점 표시 결과

| 턴 | 표시 | 내용 |
|---|---|---|
| Turn 2 | ⚠️[환각] | 공식 문서 항목명 "Fine-tuning Annotation-based Autowiring with Primary / Qualifiers"는 실제로 없는 제목이다. 공식 문서(Spring Framework reference, Annotation-based Container Configuration)에는 "Fine-tuning Annotation-based Autowiring with `@Primary` or `@Fallback`"와 "Fine-tuning Annotation-based Autowiring with Qualifiers"가 별도 페이지로 있다. 두 제목을 합쳐서 지어낸 형태다. (확인: docs.spring.io autowired-primary.html, autowired-qualifiers.html) |
| Turn 6 | ⚠️[환각] 경미 | "@Primary를 두 클래스에 모두 붙이면 **같은 에러**가 다시 나요"라고 했다. 스프링 소스(`DefaultListableBeanFactory.determinePrimaryCandidate`)를 보면 이 경우도 `NoUniqueBeanDefinitionException`을 던지므로 예외 종류는 같다. 다만 메시지는 `more than one 'primary' bean found among candidates: [...]`로, 수강생이 본 `expected single matching bean but found 2`와 다르다. 예외 종류로 보면 맞고 메시지로 보면 부정확한 표현이다. |
| Turn 6, 22 | 없음 | Turn 5에서 준 "@Qualifier 금지" 조건이 유지됐다. Turn 6은 `@Primary`만, Turn 22(공지 3문장, 코드 없음)도 `@Qualifier`를 쓰지 않았다. |
| Turn 8~20 | 없음 | H1~H7 모두 정답표와 일치했다. H3(틀린 전제)는 바로잡았고, H4(없는 애노테이션)는 "단정할 수 없다"며 확인 절차를 제시했다. 판정은 system-design.md §8. |
| Turn 24 | 없음 (관찰) | `jakarta`로 바꾸라고 했고 `javax.servlet-api` 추가나 curl은 권하지 않았다(PowerShell `Invoke-WebRequest` 사용). 다만 확인 방법이 "필터가 스프링에 등록돼 있다"는 전제인데 등록 방법은 안내하지 않았다(`확인 필요`에서 한계는 밝힘). |

> 특히 볼 곳: Turn 21·22·24에서 @Qualifier가 다시 나오면 `⚠️[문맥 끊김]`, Turn 24에서 curl이나 `javax.servlet-api` 의존성 추가를 권하면 `⚠️[환각]` 또는 금지 위반.

---

## 4. v2 재실행 로그

> v2로 바꾼 뒤, ⚠️ 표시가 나온 턴까지 같은 입력을 다시 보낸 원문. (새 대화에서 시작)

**실행 정보 (v2)**: claude.ai 새 프로젝트 `codyssey-1-1-1-2`의 프로젝트 지침에 v2 전문(예시 1~4 포함)을 입력하고, 그 프로젝트의 새 대화 하나에서 진행. 모델 Sonnet 5.5, 2026-11-24 저녁(오후 7:20 이후 ~ 21:10 이전, 정확한 시각은 확인하지 못함). 응답 본문은 화면 텍스트에서 가져온 그대로이고(마크다운 서식은 표시되지 않음), 응답 앞의 UI 요약 문구는 제외했다. 중간 Turn 7~22는 생략하고 Turn 1, 3, 5, 23만 보냈다.

**Turn 1**
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 스프링 핵심 원리 - 의존관계 주입까지 배움
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
할인 정책 인터페이스를 만들고 구현체를 2개 만들었는데 실행하면 이 에러가 나요.
[에러 로그]
org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.example.shop.discount.DiscountPolicy' available:
expected single matching bean but found 2: fixDiscountPolicy,rateDiscountPolicy
```

**Turn 2**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개 등록돼 있어요. 스프링이 타입으로 주입할 빈을 찾다가 어느 쪽을 줄지 정하지 못한 거예요.

확인 방법:

DiscountPolicy를 주입받는 클래스를 찾아요. 생성자 파라미터나 @Autowired 필드에 타입이 DiscountPolicy(인터페이스)로 되어 있을 거예요.
두 구현체 클래스 위에 각각 @Component가 붙어 있는지 확인해요. 에러 로그에 두 이름이 모두 나온 걸 보면 둘 다 빈으로 등록된 상태예요.

해결 힌트: 기본으로 쓸 구현체 하나를 정해서 그 클래스에 @Primary를 붙여 보세요.


java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy { ... }

위 예시는 RateDiscountPolicy를 기본으로 정한 경우예요. 어느 쪽을 기본으로 쓸지는 상황에 맞게 골라요. 주입받는 곳에서 직접 고르는 방법도 있어요. 주입 지점에 @Qualifier로 빈 이름을 지정하거나, 파라미터 이름을 빈 이름과 맞추는 방식이에요. 먼저 @Primary로 에러가 사라지는지 확인해 보세요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 주입 대상을 정하는 규칙 (@Primary, @Qualifier)

확인 필요: 수강생의 주입 코드(생성자인지 필드인지, 파라미터 이름)를 보지 못했어요. 이름 매칭 방식은 이 코드를 확인한 뒤에 안내하는 게 안전해요.
```

**Turn 3** `[반복 확인]` — Turn 1과 완전히 같은 입력
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 스프링 핵심 원리 - 의존관계 주입까지 배움
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
할인 정책 인터페이스를 만들고 구현체를 2개 만들었는데 실행하면 이 에러가 나요.
[에러 로그]
org.springframework.beans.factory.NoUniqueBeanDefinitionException:
No qualifying bean of type 'com.example.shop.discount.DiscountPolicy' available:
expected single matching bean but found 2: fixDiscountPolicy,rateDiscountPolicy
```

**Turn 4**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개 등록돼 있어요. 스프링이 타입으로 주입할 빈을 찾다가 어느 쪽을 줄지 정하지 못한 거예요.

확인 방법:

DiscountPolicy를 주입받는 클래스를 찾아요. 생성자 파라미터나 @Autowired 필드에 타입이 DiscountPolicy(인터페이스)로 되어 있을 거예요.
두 구현체 클래스 위에 각각 @Component가 붙어 있는지 확인해요. 에러 로그에 두 이름이 모두 나온 걸 보면 둘 다 빈으로 등록된 상태예요.

해결 힌트: 기본으로 쓸 구현체 하나를 정해서 그 클래스에 @Primary를 붙여 보세요.


java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy { ... }

위 예시는 RateDiscountPolicy를 기본으로 정한 경우예요. 어느 쪽을 기본으로 쓸지는 상황에 맞게 골라요. 주입받는 곳에서 직접 고르는 방법도 있어요. 주입 지점에 @Qualifier로 빈 이름을 지정하거나, 파라미터 이름을 빈 이름과 맞추는 방식이에요. 먼저 @Primary로 에러가 사라지는지 확인해 보세요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 주입 대상을 정하는 규칙 (@Primary, @Qualifier)

확인 필요: 수강생의 주입 코드(생성자인지 필드인지, 파라미터 이름)를 보지 못했어요. 이름 매칭 방식은 이 코드를 확인한 뒤에 안내하는 게 안전해요.
```

**Turn 5** `[조건 변경]` `[추가 정보]`
User:
```text
추가 정보야. 이 수강생 반은 아직 @Qualifier를 안 배웠고 @Primary까지만 배웠어.
앞으로 이 대화에서는 @Qualifier를 해결책으로 쓰지 말고 다시 써줘.
```

**Turn 6**
AI:
```text
원인 추정: DiscountPolicy 타입의 빈이 fixDiscountPolicy, rateDiscountPolicy 두 개 등록돼 있어요. 스프링이 타입으로 주입할 빈을 찾다가 어느 쪽을 줄지 정하지 못한 거예요.

확인 방법:

DiscountPolicy를 주입받는 클래스를 찾아요. 생성자 파라미터나 @Autowired 필드에 타입이 DiscountPolicy(인터페이스)로 되어 있을 거예요.
두 구현체 클래스 위에 각각 @Component가 붙어 있는지 확인해요. 에러 로그에 두 이름이 모두 나온 걸 보면 둘 다 빈으로 등록된 상태예요.

해결 힌트: 기본으로 쓸 구현체 하나를 정해서 그 클래스에 @Primary를 붙여 보세요.


java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy { ... }

위 예시는 RateDiscountPolicy를 기본으로 정한 경우예요. 어느 쪽을 기본으로 쓸지는 상황에 맞게 골라요. 지금 당장 한 가지 구현체만 필요하다면, 쓰지 않는 쪽의 @Component를 지워서 빈을 하나만 남기는 방법도 있어요. 먼저 @Primary로 에러가 사라지는지 확인해 보세요.

함께 볼 개념: 같은 타입의 빈이 여러 개일 때 @Primary로 기본 빈을 정하는 방식

확인 필요: 수강생의 주입 코드(생성자인지 필드인지)를 보지 못했어요. @Primary는 주입 방식과 상관없이 적용돼요. 다만 주입 지점에서 특정 구현체를 직접 골라야 하는 상황이라면 코드를 확인한 뒤에 안내하는 게 안전해요.
```

**Turn 23** — (참고: 이번 실행에서 질문 문장의 "떠요"를 "뜨요"로 한 글자 잘못 입력해서 보냄. 아래는 실제로 보낸 그대로)
User:
```text
아래 수강생 질문에 답변 초안 써줘.

[진도] 서블릿 필터 배우기 직전
[환경] Windows 11, IntelliJ, Spring Boot 3.3, Java 17
[질문]
블로그 보고 필터를 만들어 보려고 import javax.servlet.Filter; 를 썼는데 IntelliJ에서 빨간 줄이 뜨요. 의존성 문제인가요? 그리고 필터가 동작하는지 터미널에서 확인하는 방법도 알려주세요.
[에러 로그]
Cannot resolve symbol 'servlet'
[코드]
없음
```

**Turn 24**
AI:
```text
원인 추정: 블로그 예제가 javax.servlet 패키지를 쓰고 있어서 그래요. Spring Boot 3.x(Java 17)에서는 서블릿 패키지 이름이 jakarta.servlet으로 바뀌었어요. 그래서 IntelliJ가 javax.servlet을 찾지 못하고 빨간 줄을 띄우는 거예요. 의존성이 빠졌을 가능성은 낮아요.

확인 방법:

빨간 줄이 뜬 import 줄이 javax.servlet.Filter로 되어 있는지 확인해요.
그 줄을 jakarta.servlet.Filter로 바꾸고, 빨간 줄이 사라지는지 IDE에서 확인해요.
IntelliJ 메뉴 Build → Build Project로 빌드가 성공하는지 확인해요.

해결 힌트: import 줄의 javax를 jakarta로 바꾸면 돼요.


java
import jakarta.servlet.Filter;

블로그 코드에서 javax.servlet으로 시작하는 다른 import도 모두 같은 방식으로 바꿔 주세요. jakarta로 바꿔도 빨간 줄이 남으면 그때 의존성을 살펴보면 돼요.

터미널로 필터 동작을 확인하는 방법은 지금은 안내하기 어려워요. 필터가 실제로 동작하려면 먼저 필터를 등록해야 하는데, 아직 배우기 전인 내용이에요. 지금 단계에서는 빨간 줄이 사라지고 빌드가 성공하는지까지만 확인하면 돼요. 필터를 배운 뒤에는 서버를 실행하고 브라우저로 접속해서 확인할 수 있어요. 터미널을 쓰고 싶다면 PowerShell에서 Invoke-WebRequest 명령으로 요청을 보낼 수 있어요.

함께 볼 개념: Spring Boot 3에서 javax 패키지가 jakarta 패키지로 바뀐 것 (Jakarta EE 전환)

확인 필요: 수강생 프로젝트의 의존성 목록을 보지 못했어요. jakarta로 바꿨는데도 빨간 줄이 남으면 의존성을 확인해야 해요. 패키지 변경의 공식 근거는 Spring Boot 3.0 마이그레이션 가이드(Spring Boot 위키)에서 찾을 수 있어요. 검색 키워드는 Spring Boot 3.0 migration jakarta예요.
```

**추가 질문 (시나리오 외, v1 Turn 6의 ⚠️ 의심 지점 재확인용)** — 같은 대화에서 이어서 보냄
User:
```text
추가 질문이야. 두 구현체 클래스에 모두 @Primary를 붙이면 어떻게 돼?
```

AI:
```text
원인 추정: @Primary를 두 클래스에 모두 붙이면 기본 빈이 두 개가 돼서, 스프링이 여전히 하나를 고르지 못해요. 이 경우에도 예외 클래스는 NoUniqueBeanDefinitionException이 나올 것으로 보여요. 다만 메시지는 앞에서 본 것과 다를 수 있어요.

확인 방법:

두 구현체 클래스 위에 @Primary가 몇 개 붙어 있는지 세어 봐요.
애플리케이션을 실행해서 콘솔의 예외 이름과 메시지를 확인해요.

해결 힌트: @Primary는 기본으로 쓸 구현체 한 곳에만 붙여요. 다른 쪽에서 @Primary를 지우면 돼요.

함께 볼 개념: @Primary는 "후보 중 우선권을 주는 표시"라서 한 타입당 하나만 지정할 수 있다는 점

확인 필요: 예외 메시지의 정확한 문구는 확실하지 않아요. 직접 실행해서 콘솔 출력으로 확인하는 게 가장 정확해요.
```

---

## 5. 작업 기록

> 작업 하나 = 한 항목. "무엇을 어떻게 했는지"가 드러나게. 시작·막힘·끝에 한 줄씩.

| 일시 | 작업 | 무엇을 어떻게 했나 |
|---|---|---|
| 2026-11-24 | 과업 선정 | 현업 과업 정의가 없어서, 강사 업무 중 매 기수 반복되는 "수강생 Spring 기술 질문 답변 초안"으로 정함. 후보(복습 퀴즈 생성, 수업 공지문)와 비교해 사실 검증이 쉬운 쪽을 고름 |
| 2026-11-24 | 저장소 뼈대 작성 | Claude Code에 요구사항을 주고 README와 docs 4개 문서의 틀, 비교용 질문 3건, 페르소나·시스템 프롬프트 v1 초안, Few-shot 4건, 검증 질문 7건 초안을 받음 |
| 2026-11-24 | 초안 검토 | 받은 문서 4개의 틀(비교용 질문, 페르소나, 시스템 프롬프트 v1, Few-shot 4건, 검증 질문 7건)을 읽어 보고, model-comparison.md §2의 비교 대상(모델 이름·버전·요금제·실행 시점)을 실제 값으로 채운 뒤 모델 실행 단계로 넘어갔다. |
| 2026-11-24 15:37 | 모델 3종 실행 | 새 대화 3개에서 같은 공통 프롬프트로 Q1~Q3를 한 번씩 실행했다. ChatGPT(GPT-6, 무료), Gemini(4.4, 무료), Claude(Sonnet 5.5, Pro). 응답 9개를 편집 없이 model-comparison.md §7에 붙였다. |
| 2026-11-24 | 채점 | 정답표(§5)와 5개 기준으로 점수를 매겼다. 합계는 GPT 22, Gemini 25, Claude 24. 질문별 차이는 거의 없었고 감점은 문장 수 초과, 방법 중복 나열, 코드 한 줄 제시에서 나왔다. |
| 2026-11-24 | 모델 선정 | 합계는 Gemini가 1점 높았지만, 남은 약점을 프롬프트로 고칠 수 있는지를 기준으로 Claude를 골랐다. Claude는 틀린 답으로 빠질 지점을 먼저 막아 줬다. 근거는 model-comparison.md §8. |
| 2026-11-24 | Zero-shot / Few-shot 비교 | Zero-shot은 Q3와 모호 입력("스프링 실행이 안 돼요"), Few-shot은 v1 지침으로 Q3와 모호 입력("서버가 이상해요")을 실행했다. Zero-shot은 모호 입력에 추측으로 답했고 Few-shot은 되묻기만 했다. CoT 문장은 넣지 않기로 했다. |
| 2026-11-24 18:26~19:20 | 24턴 대화 실행 | claude.ai 프로젝트 `codyssey-1-1-1`의 지침에 v1을 넣었다. Turn 1~2는 대화 A에서, Turn 3~24는 같은 프로젝트의 새 대화 B에서 진행했다(B는 Turn 1과 같은 입력으로 시작해 반복 확인을 겸했다). 응답 12개를 §3에 붙였다. 시나리오는 한 대화 안에서 Turn 1과 3을 반복하도록 짰으나 실제로는 대화가 둘로 나뉘었다. |
| 2026-11-24 | 환각 판정 | H1~H7을 정답표와 대조해 모두 Pass로 판정했다(H5, H7은 출처 언급이 없어 경계로 표시). 판정과 이유는 system-design.md §8. |
| 2026-11-24 | 지점 표시 | 의심 두 곳을 직접 확인했다. Turn 2의 공식 문서 항목명은 Spring 공식 문서에서 확인해 환각으로 확정했고, Turn 6의 "같은 에러"는 스프링 소스(`determinePrimaryCandidate`)를 보고 경미한 부정확으로 표시했다. |
| 2026-11-24 | v2 작성·재실행 | 로그에서 드러난 문제 4가지를 서브 에이전트에게 보여 규칙 4개를 제안받아 검토한 뒤 v2를 작성했다. 새 프로젝트 `codyssey-1-1-1-2`에서 Turn 1, 3, 5, 23을 다시 보내고 추가 질문 1개로 의심 지점을 확인했다. 결과는 system-design.md §9-4. |
| 2026-11-24 | 미해결 기록 | 프롬프트를 고쳐도 해결하지 못한 것 2건(필터 확인 방법, 우회 방법 억제)을 unresolved.md에 초안으로 적었다. 막힌 이유는 추정이라 직접 확인이 필요하다. |
| (미진행) | GitHub 업로드 | 아직 올리지 않았다. 올린 뒤 저장소 주소와 올린 파일 목록을 이 칸에 적는다. |
