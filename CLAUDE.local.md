# Personal Claude Code Standards
> 개인적으로 모든 프로젝트에 적용하는 코딩 표준

## 🎯 핵심 철학

### 1. 유비쿼터스 언어 최우선
- 애플리케이션 레이어에서는 **항상** 기술 용어보다 비즈니스 용어 사용
- 코드가 곧 문서가 되도록 작성

### 2. 단순함이 최고
- 복잡한 패턴(Pipeline, Chain of Responsibility 등)보다 깔끔한 메서드 추출 선호
- 2-3개 분기는 if-else로, 4개 이상일 때만 Strategy Pattern 고려

## 📐 표준 서비스 구조

### 5단계 메서드 추출 패턴
```kotlin
@Service
class BusinessService {
    @Transactional
    fun executeBusinessOperation(command: Command): Result {
        // 1. 요청 접수/기록
        recordRequest()
        
        // 2. 데이터 조회
        val data = retrieveData()
        
        // 3. 조기 반환 체크
        if (isAlreadyProcessed()) {
            return handleAlreadyProcessed()
        }
        
        // 4. 핵심 로직 실행
        val result = executeCore()
        
        // 5. 완료 처리
        completeTransaction()
        
        return result
    }
}
```

## 🏷️ 네이밍 규칙

### 선호하는 동사
| ❌ 피하기 | ✅ 사용하기 |
|----------|-----------|
| get | identify, retrieve, find |
| process | execute, perform, conduct |
| handle | manage, coordinate, orchestrate |
| validate | ensure, verify, check |
| do | 구체적 행위 동사 사용 |

### 메서드명 패턴
- 조회: `identify{Actor}()`, `retrieve{Target}()`
- 검증: `ensure{Condition}()`, `verify{State}()`
- 실행: `execute{Operation}()`, `perform{Action}()`
- 완료: `complete{Transaction}()`, `finalize{Process}()`

## 📦 Kotlin Import 관리

### Import 규칙
- **NEVER use fully qualified class names** in code
- **ALWAYS add proper imports** at the top of the file
- **Prefer shorter class names** with imports

### Import 순서
1. Kotlin 표준 라이브러리
2. Java 표준 라이브러리  
3. Spring Framework
4. 외부 라이브러리
5. 프로젝트 내부 패키지

### Example
```kotlin
// ✅ Good
import me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.entity.PaymentOrder

private fun processPayment(paymentOrder: PaymentOrder) { }

// ❌ Bad  
private fun processPayment(
    paymentOrder: me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.entity.PaymentOrder
) { }
```

## 🚫 안티패턴

### 1. 과도한 추상화
```kotlin
// ❌ Bad: 불필요한 추상화
interface PaymentProcessor {
    fun process(payment: Payment): Result
}
class SimplePaymentProcessor : PaymentProcessor
class ComplexPaymentProcessor : PaymentProcessor

// ✅ Good: 직접적인 구현
class PaymentService {
    fun processSimplePayment() { }
    fun processComplexPayment() { }
}
```

### 2. 모호한 네이밍
```kotlin
// ❌ Bad
fun handleData(data: Data)
fun processStuff(stuff: Any)
fun doWork()

// ✅ Good
fun validateUserInput(input: UserInput)
fun calculateOrderTotal(order: Order)
fun sendNotification(recipient: User)
```

### 3. 긴 패키지 경로 인라인 사용
```kotlin
// ❌ Bad: 코드 가독성 저하
val result = me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.service.PaymentDomainService()
    .processPaymentConfirmation(
        paymentOrder = me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.entity.PaymentOrder(),
        paymentBillId = me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.type.PaymentBillId()
    )

// ✅ Good: Import 사용
import me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.service.PaymentDomainService
import me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.entity.PaymentOrder
import me.ogq.ocp.dam.market.rcs.apiserver.core.domain.payment.type.PaymentBillId

val result = paymentDomainService.processPaymentConfirmation(
    paymentOrder = PaymentOrder(),
    paymentBillId = PaymentBillId()
)
```

## 💬 주석 스타일

### 한글 주석 사용 시점
- 주요 비즈니스 로직 단계 구분
- 복잡한 도메인 규칙 설명
- TODO/FIXME 마커

```kotlin
// 1. 구매 승인 요청 접수
recordPurchaseApprovalRequest()

// 비즈니스 규칙: 중복 구매 시 자동 환불 처리
if (isDuplicate) {
    // TODO: 환불 실패 시 재시도 로직 추가
    processRefund()
}
```

## 🎨 코드 포맷팅

### 공백 라인 규칙
- 클래스 멤버 간: 1줄
- 메서드 간: 1줄
- 논리적 블록 간: 1줄
- 파일 끝: 1줄

### 줄 길이
- 최대 120자 권장
- 긴 메서드 체인은 각 호출마다 개행

## 🔧 리팩토링 우선순위

1. **네이밍 개선** - 가장 먼저, 가장 중요
2. **Import 정리** - Fully qualified names 제거
3. **메서드 추출** - 긴 메서드를 작은 단위로
4. **조기 반환** - 중첩 감소
5. **임시 변수 제거** - 직접 반환 선호
6. **불필요한 추상화 제거** - YAGNI 원칙

## 📝 커밋 메시지

```
type(scope): 간단한 설명

- 상세 내용 1
- 상세 내용 2

Issue: #123
```

Types:
- `refactor`: 리팩토링
- `style`: 코드 스타일 개선
- `naming`: 네이밍 개선
- `docs`: 문서화
- `import`: Import 정리

## 🌍 적용 범위

이 규칙들은 다음 기술 스택에 적용:
- Kotlin/Java Spring Boot 프로젝트
- TypeScript/JavaScript Node.js 프로젝트
- Python FastAPI/Django 프로젝트

각 언어의 관례를 존중하되, 핵심 철학은 동일하게 적용

## 🛠️ Editor 동작 규칙

### Edit/Write 도구 사용 시
1. 항상 필요한 import 확인 및 추가
2. Fully qualified names를 절대 도입하지 않음
3. 네이밍 충돌 시 import alias 사용
4. 기존 코드의 import 스타일 따르기
5. **코드 작성/수정 후 반드시 컴파일 확인** (`./gradlew :module:compileKotlin`)

### 코드 작성 완료 후 필수 체크
```bash
# 1. 해당 모듈 컴파일
./gradlew :usecase:compileKotlin :controller:compileKotlin

# 2. 전체 빌드 (테스트 제외)
./gradlew build -x test
```

### 코드 리뷰 시 확인사항
- [ ] 유비쿼터스 언어 사용 여부
- [ ] 5단계 패턴 준수 여부
- [ ] Import 정리 상태
- [ ] 불필요한 추상화 없음
- [ ] 명확한 메서드명
- [ ] **컴파일 성공 여부**

## 🧪 테스트 코드 표준

### 테스트 아키텍처 (80% 통합, 20% 단위)
- **통합 테스트**: `src/intTest/kotlin/` (Kotest + WebTestClient)
- **단위 테스트**: `src/test/kotlin/` (JUnit 5 + MockK)
- **테스트 픽스처**: `src/testFixtures/kotlin/` (공유 테스트 데이터)

### 통합 테스트 패턴
```kotlin
@IntegrationTestConfig
@DisplayName("기능 설명")
class FeatureAPITest(
    private val testClient: WebTestClient,
    private val cleanUpDataService: CleanUpDataService,
    private val testDataService: TestDataService,
) : FunSpec({
    
    beforeTest {
        cleanUpDataService.deleteAllData()
        testDataService.createTestData()
    }
    
    test("시나리오 설명") {
        // given
        val request = TestRequestFactory.create(...)
        
        // when
        val response = testClient.apiCall(request)
        
        // then
        response.field shouldBe expectedValue
    }
})
```

### API 확장 함수 패턴
별도 파일(`Test{Domain}Apis.kt`)에서 WebTestClient 확장 함수로 정의:
```kotlin
fun WebTestClient.createOrder(
    req: TestCreateOrderReq,
    token: String? = TEST_USER_TOKEN,
    assertStatus: (StatusAssertions) -> WebTestClient.ResponseSpec = StatusAssertions::is2xxSuccessful
): TestOrderDTO {
    return this.post()
        .uri("/api/v1/orders")
        .contentType(MediaType.APPLICATION_JSON)
        .header(AUTHORIZATION_HEADER, "Bearer $token")
        .bodyValue(req)
        .exchange()
        .validateRes(assertStatus, object : ParameterizedTypeReference<TestOrderDTO>() {})
        .res()
}
```

### 단위 테스트 패턴
```kotlin
class ServiceTest {
    private val dependency = mockk<Dependency>()
    private lateinit var service: Service
    
    @BeforeEach
    fun setUp() {
        service = Service(dependency)
    }
    
    @Test
    @DisplayName("테스트 시나리오 설명")
    fun testScenario() {
        // given
        every { dependency.method(any()) } returns expectedResult
        
        // when
        val result = service.execute(input)
        
        // then
        verify { dependency.method(input) }
        assert(result == expected)
    }
}
```

### 테스트 픽스처 Factory 패턴
```kotlin
object TestOrderReqFactory {
    fun create(
        productId: String? = null,
        quantity: Int = 1,
        customField: String? = null
    ): TestCreateOrderReq {
        val seed = UUID.randomUUID().toString()
        return TestCreateOrderReq(
            productId = productId ?: "test-product-${seed}",
            quantity = quantity,
            customField = customField ?: "default-value"
        )
    }
}
```

### 테스트 네이밍 규칙
| 테스트 유형 | 네이밍 패턴 | 예시 |
|------------|------------|------|
| 통합 테스트 | `{Feature}APITest` | `UserAPITest` |
| 단위 테스트 | `{Class}Test` | `UserServiceTest` |
| API 확장 함수 | `Test{Domain}Apis` | `TestUserApis.kt` |
| 팩토리 | `Test{Entity}Factory` | `TestUserFactory` |

### 에러 케이스 테스팅
```kotlin
test("유효하지 않은 요청 시 400 에러 반환") {
    // given
    val invalidRequest = TestFactory.createInvalid()
    
    // when & then
    testClient.apiCall(
        invalidRequest,
        assertStatus = { it.isEqualTo(HttpStatus.BAD_REQUEST) }
    )
}
```

### HTTP 테스트 파일 작성
```http
### API 설명
POST {{host}}/api/v1/endpoint
Authorization: Bearer {{auth-token}}
Content-Type: application/json

{
  "field": "value"
}
```

### 테스트 작성 원칙
1. **블랙박스 테스팅**: API 응답과 상태 변화에 집중
2. **비즈니스 시나리오 중심**: 기술 구현보다 요구사항 검증
3. **Given-When-Then 구조**: 명확한 단계 구분
4. **한국어 DisplayName**: 비즈니스 관점의 설명
5. **에러 케이스 포함**: 정상/비정상 케이스 모두 테스트

### 테스트 데이터 관리
- `CleanUpDataService`: 각 테스트 전 데이터 정리
- `TestDataService`: 도메인별 테스트 데이터 생성
- 트랜잭션 경계 및 롤백 검증
- 외부 서비스는 TestGateway나 MockK로 스텁 처리

## 📚 참고 자료

- Clean Code by Robert C. Martin
- Domain-Driven Design by Eric Evans
- Refactoring by Martin Fowler
- Effective Kotlin by Marcin Moskała

---
*Last Updated: 2025-01-21*
*Personal standards for @jungcollin*
*GitHub: ~/.claude repository*