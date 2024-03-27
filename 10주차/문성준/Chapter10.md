# ch10 상속과 코드 재사용

---

### 서론

합성 : 새로운 클래스의 인스턴스 안에 기존 클래스의 인스턴스를 포함시키는 방법

사실, 책에서 주구장창 얘기하는 것은 상속보단 다형성을, 다형성보단 합성을 하라는 것이 저자가 전달하고자 하는 목표다. 다형성을 이룸으로써 책임을 할당하게 되고, 이를 통해 각 역할의 상호 협력을 통해 일관성 있는 설계가 이뤄지기 때문이다.

---

# 상속과 중복코드

## DRY 원칙 - 반복하지마라.

중복 코드는 변경을 방해한다.

지식은 항상 변하며, 그에 맞춰 지시식을 표현하는 코드로 변경해야 한다.

만약, 중복 코드를 갖는다면 코드를 수정하는 데 필요한 노력을 몇 배로 증가시킨다.

우선 어떤 코드가 중복인지를 차자.

→ 중복 여부를 결정하는 기준은 코드가 변경에 반응하는 방식이다.

→ 어떻게 반응하는 것일까 ?

중복 코드는 프로그램의 유지보수성을 떨어뜨리는 주요 원인 중 하나입니다. 코드가 변경에 반응하는 방식을 이해하려면, 먼저 코드가 어떤 상황에서 변경이 요구되는지를 알아야 합니다. 코드 변경의 요구는 대개 다음과 같은 상황에서 발생합니다.

1. **기능 추가 또는 수정**
새로운 요구사항이 생겼거나 기존 기능을 변경해야 할 때, 관련된 모든 코드를 찾아 수정해야 합니다. 중복 코드가 있다면, 모든 중복된 부분을 일일이 찾아 수정해야 하므로 더 많은 노력과 시간이 소요됩니다.
2. **버그 수정**
오류가 발견되었을 때, 그 오류가 중복된 코드에 존재한다면 모든 중복된 부분에서 같은 수정이 필요합니다. 이는 수정해야 할 범위를 넓히고, 일부를 놓칠 위험을 증가시킵니다.
3. **리팩토링**
코드의 구조를 개선하거나, 더 효율적인 알고리즘으로 변경하고자 할 때 중복 코드는 과정을 복잡하게 만듭니다. 중복된 로직을 한 곳에 모으는 것만으로도 코드의 가독성과 유지보수성이 크게 향상될 수 있습니다.
4. 

중복 코드가 변경에 반응하는 방식을 결정하는 기준은,

### **‘ 변경 지점이 단일화되어 있는가**와 **변경 시 일관성을 유지할 수 있는가’**입니다.

즉, 코드를 변경할 때 다음 두 조건을 충족시키면 중복을 효과적으로 관리할 수 있습니다

- **단일 책임 원칙(Single Responsibility Principle)**
변경이 필요한 모든 로직이 한 곳에 모여 있어야 합니다. 이는 같은 이유로 변경되는 코드들을 하나의 단위로 묶어야 함을 의미합니다.
- **중복 최소화**
 비슷하거나 동일한 로직을 여러 곳에 작성하는 대신, 함수나 클래스를 재사용하여 중복을 줄입니다. 이는 코드베이스의 크기를 줄이고, 변경 시 발생할 수 있는 오류의 가능성을 낮춥니다.

단일 책임원칙을 지향하지만, DRY 원칙을 다른 표현으로는 ‘단일 지점 제어’라고도 부른다.
→ 코드 안에 중복이 존재해서는 안된다는 것이다.

---

# 그렇다면, 코드 안에서 중복이란 무엇일까 ?

자바 코드에서 중복이란, 두 가지 주요 형태로 나타납니다.

**리터럴 중복**과 **구조적 중복**입니다. 

이 두 형태의 중복 모두 피해야 할 대상이며, 코드의 유지보수성과 확장성에 부정적인 영향을 미칩니다.

### **1. 리터럴 중복**

리터럴 중복은 코드 내에서 하드코딩된 값이 반복적으로 사용될 때 발생합니다. 

예를 들어, 특정 문자열, 숫자, 또는 다른 데이터 타입의 값을 코드 여러 곳에서 직접 사용하는 경우입니다.

이런 리터럴 값들은 프로그램의 변경 요구 사항이 발생했을 때, 모든 인스턴스를 찾아 수정해야 하는 번거로움이 있습니다. 

예를 들어, 시스템에서 사용하는 특정 URL이나 API 키 같은 정보가 여러 곳에 직접 쓰인 경우, 이 정보가 변경되면 관련된 모든 코드를 찾아 수정해야 합니다.

### **예시**

```java
System.out.println("Error: Invalid operation");
System.out.println("Error: User not found");
```

위 코드에서 "Error: " 문자열이 중복됩니다. ***이를 상수로 정의하여 중복을 제거할 수 있습니다.***

### **2. 구조적 중복**

구조적 중복은 코드의 구조적인 부분, 즉 로직이나 알고리즘이 여러 곳에서 반복되어 사용될 때 발생합니다. 

이는 함수, 메소드, 또는 클래스 단위로 나타날 수 있으며, 비슷하거나 동일한 작업을 수행하는 코드의 블록이 여러 곳에 존재하는 것을 의미합니다. 구조적 중복은 리팩토링을 통해 해결할 수 있으며, 함수나 메소드를 재사용하거나, 디자인 패턴을 적용하여 중복을 최소화할 수 있습니다.

### **예시**

```java
public int calculateSum(int[] numbers) {
    int sum = 0;
    for(int number : numbers) {
        sum += number;
    }
    return sum;
}

public double calculateAverage(int[] numbers) {
    double sum = 0;
    for(int number : numbers) {
        sum += number;
    }
    return sum / numbers.length;
}
```

위 코드에서 숫자 배열의 합을 구하는 로직이 중복됩니다. 이 로직을 별도의 메소드로 추출하여 중복을 제거할 수 있습니다.

### **중복 제거의 중요성**

중복 제거는 코드의 재사용성을 높이고, 변경 사항이 있을 때 한 곳만 수정하면 되도록 하여 유지보수를 용이하게 합니다. 또한, 코드의 가독성을 향상시키고, 버그 발생 가능성을 줄입니다. 따라서 DRY 원칙을 따르고, 단일 지점 제어를 통해 코드베이스의 건강을 유지하는 것이 중요합니다.

---

## 스프링 부트의 예시

### **1. 설정 중복**

스프링 부트는 자동 구성(auto-configuration) 기능을 제공하지만, 때로는 개발자가 애플리케이션의 특정 부분에 대해 세부적인 설정을 수동으로 해야 할 때가 있습니다. 이런 설정 코드가 여러 곳에 중복되어 있다면, 설정을 변경할 때마다 모든 장소를 수정해야 합니다.

### 예시: 데이터 소스 설정

여러 개의 데이터 소스를 설정할 때 비슷한 구성 코드가 여러 클래스에 걸쳐 중복될 수 있습니다.

```java
@Configuration
public class PrimaryDataSourceConfig {

    @Bean
    @Primary
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create()
                .url("jdbc:mysql://localhost:3306/primary_db")
                .username("user")
                .password("pass")
                .build();
    }
}

@Configuration
public class SecondaryDataSourceConfig {

    @Bean
    public DataSource secondaryDataSource() {
        return DataSourceBuilder.create()
                .url("jdbc:mysql://localhost:3306/secondary_db")
                .username("user")
                .password("pass")
                .build();
    }
}

```

위와 같은 중복을 줄이기 위해서는 공통 구성을 부모 클래스나 별도의 구성 클래스에 정의하고 이를 상속받거나 주입하는 방식을 사용할 수 있습니다.

### **2. 비즈니스 로직 중복**

서비스 레이어나 컨트롤러 레이어에서 비슷한 비즈니스 로직이 반복되는 경우가 있습니다. 

예를 들어, 여러 서비스 메소드에서 동일한 방식으로 데이터를 검증하거나, 여러 컨트롤러에서 같은 로직을 통해 응답을 생성하는 경우 등입니다.

### 예시: 응답 생성 로직

REST API를 제공하는 컨트롤러에서 비슷한 형태의 응답을 생성하는 코드가 중복될 수 있습니다.

```java
@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.findById(id);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }

    @GetMapping("/user/email/{email}")
    public ResponseEntity<User> getUserByEmail(@PathVariable String email) {
        User user = userService.findByEmail(email);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }
}

```

위 예시에서, 사용자를 찾지 못했을 때 **`ResponseEntity.notFound().build()`**를 반환하는 로직이 중복됩니다. 이러한 중복을 줄이기 위해서는 응답 생성 로직을 별도의 메소드나 클래스로 분리하여 재사용할 수 있습니다.

### **중복 제거의 중요성**

스프링 부트 애플리케이션에서 코드 중복을 제거하는 것은 유지보수를 용이하게 하고, 코드의 가독성을 향상시키며, 버그를 줄이는 데 중요합니다. 또한, 애플리케이션의 변경 사항을 더 빠르고 안전하게 적용할 수 있도록 도와줍니다. DRY 원칙을 적용하고 중복을 최소화하여, 깔끔하고 관리하기 쉬운 코드베이스를 유지하도록 합시다.

스프링 부트 코드에서 지금 중복코드가 보이는데, 어떻게 중복 제거를 할 수 있을까???

앞서 본 스프링 부트 코드 예시에서 중복된 응답 생성 로직을 줄이는 방법을 알아보겠습니다. 중복된 부분은 주로 사용자를 찾지 못했을 때의 처리와 사용자 정보를 성공적으로 찾았을 때의 응답 생성 로직입니다. 이러한 로직을 별도의 메소드로 추출하여 재사용함으로써 중복을 제거할 수 있습니다.

### **중복 제거 전**

```java

@RestController
public class UserController {

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.findById(id);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }

    @GetMapping("/user/email/{email}")
    public ResponseEntity<User> getUserByEmail(@PathVariable String email) {
        User user = userService.findByEmail(email);
        if (user == null) {
            return ResponseEntity.notFound().build();
        }
        return ResponseEntity.ok(user);
    }
}

```

### **중복 제거 후**

중복을 제거하기 위해 **`ResponseEntity<User>`**를 반환하는 공통 메소드를 생성합니다. 이 메소드는 **`Optional<User>`**를 매개변수로 받아 사용자 객체가 존재하면 이를 반환하고, 그렇지 않으면 404 Not Found 응답을 반환합니다.

```java
@RestController
public class UserController {

    private final UserService userService;

    // 생성자 주입 방식을 사용하여 UserService를 주입합니다.
    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .map(ResponseEntity::ok)
                .orElseGet(() -> ResponseEntity.notFound().build());
    }

    @GetMapping("/user/email/{email}")
    public ResponseEntity<User> getUserByEmail(@PathVariable String email) {
        return userService.findByEmail(email)
                .map(ResponseEntity::ok)
                .orElseGet(() -> ResponseEntity.notFound().build());
    }
}

```

이 예시에서는 **`Optional`**을 활용하여 사용자 정보를 조회하고, 결과에 따라 적절한 **`ResponseEntity`**를 반환합니다. 이 방법은 중복된 응답 생성 로직을 효과적으로 제거하고 코드의 가독성을 향상시킵니다. 또한, Java 8의 **`Optional`** API를 사용하여 보다 깔끔하고 명확한 조건 처리가 가능해집니다.

이렇게 중복 코드를 제거하는 것은 코드의 유지보수성을 향상시키고, 변경 사항이 발생했을 때 수정해야 하는 코드의 양을 줄여줍니다. 더욱 간결하고 재사용 가능한 코드를 작성함으로써, 개발 효율성을 높일 수 있습니다.

그렇다면 해당 코드를 이전에 프로젝트에 진행했던, BaseEntity 처럼 빼내서 상속을 받아서 인자로 전달받아서 반환하는 구조로 변경을 한다면 ??? 이를 엔티티가 아닌 컨트롤러에 적용을 한다면 어떻게 되는 것일까라는 생각으로 시작되었다. (중복된 컨트롤러의 메서드)
그 부분을 별도로 BaseContoroller로 빼낸다음에 구현하면 좋은 설계가 되는 것일까?

**(ChatGPT 답변)**

네, 맞습니다. 특정 패턴이나 로직이 여러 컨트롤러에서 반복되는 경우, 그 부분을 **`BaseController`**라는 공통 컨트롤러 클래스로 추출하는 것은 좋은 설계 방법 중 하나입니다. 이 방식은 코드의 재사용성을 높이고, 중복을 줄이며, 변경 사항을 쉽게 관리할 수 있게 해 줍니다.

### **BaseController의 장점**

1. **중복 제거**
공통 로직을 한 곳에 모아 두면, 비슷한 기능을 하는 여러 컨트롤러에서 같은 코드를 반복해서 작성할 필요가 없습니다.
2. **유지보수 용이**
 공통 로직을 수정해야 할 경우, **`BaseController`**만 수정하면 되므로 유지보수가 용이해집니다.
3. **일관성 유지**
공통 로직을 **`BaseController`**에 정의함으로써 애플리케이션 전반에 걸쳐 일관된 방식의 처리를 할 수 있습니다.

### **BaseController 예시**

아래는 **`BaseController`**를 활용한 예시입니다. 예를 들어, 여러 컨트롤러에서 공통으로 사용하는 응답 생성 로직을 **`BaseController`**에 정의할 수 있습니다.

```java
public class BaseController {

    protected ResponseEntity<?> responseNotFound() {
        return ResponseEntity.notFound().build();
    }

    protected <T> ResponseEntity<T> responseOk(T body) {
        return ResponseEntity.ok(body);
    }
}

```

그리고 다른 컨트롤러에서 이 **`BaseController`**를 상속 받아 사용합니다

```java

@RestController
public class UserController extends BaseController {

    @GetMapping("/user/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .map(this::responseOk)
                .orElseGet(this::responseNotFound);
    }

    // 다른 메서드들...
}

```

이런 방식으로, 공통의 응답 로직을 **`BaseController`**에서 관리하면서, 각 컨트롤러에서는 이를 상속받아 필요한 로직만 구현하여 사용할 수 있습니다. 이는 코드의 중복을 줄이고, 애플리케이션의 구조를 더 깔끔하고 관리하기 쉬운 형태로 만들어 줍니다.

하지만 모든 컨트롤러에서 **`BaseController`**의 메서드를 필요로 하는 것은 아닙니다. 따라서, 이런 방식을 적용할 때는 애플리케이션의 구조와 필요성을 고려하여, 실제로 재사용성이 높은 공통 로직에 대해서만 **`BaseController`**를 사용하는 것이 바람직합니다.

이전 방학 프로젝트 진행시 에러 코드를 뽑아서 쓰는 것처럼.

- Vacationproject/shoppingMall/common/Error/exception/ErrorCode.java

### **BaseController 예시 확장**

```java
public class BaseController {

    // 404 Not Found 응답
    protected ResponseEntity<?> responseNotFound(String message) {
        Map<String, String> error = new HashMap<>();
        error.put("error", message);
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    // 200 OK 응답, 제네릭 메서드
    protected <T> ResponseEntity<T> responseOk(T body) {
        return ResponseEntity.ok(body);
    }

    // 400 Bad Request 응답
    protected ResponseEntity<?> responseBadRequest(String message) {
        Map<String, String> error = new HashMap<>();
        error.put("error", message);
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
    }

    // 다른 공통 메서드들...
}

```

이 **`BaseController`**를 상속 받아 사용하는 **`UserController`**의 예시는 다음과 같습니다.

### **UserController 예시 확장**

```java
javaCopy code
@RestController
public class UserController extends BaseController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/user/{id}")
    public ResponseEntity<?> getUserById(@PathVariable Long id) {
        return userService.findById(id)
                .map(this::responseOk)
                .orElseGet(() -> responseNotFound("User not found with id: " + id));
    }

    @PostMapping("/user")
    public ResponseEntity<?> createUser(@RequestBody User user) {
        try {
            User savedUser = userService.save(user);
            return responseOk(savedUser);
        } catch (DataIntegrityViolationException e) {
            return responseBadRequest("User cannot be created due to constraints violation.");
        }
    }

    // 다른 컨트롤러 메서드들...
}

```

이와 같은 방식으로 **`BaseController`** 내에 공통적으로 사용되는 응답 패턴을 메서드로 정의하고, 각 컨트롤러에서 이를 상속받아 사용함으로써, 코드의 중복을 줄이고 유지보수성을 향상시킬 수 있습니다. 이 예시에서는 단순화를 위해 몇 가지 기본적인 응답 타입만을 다루었지만, 실제 애플리케이션에서는 다양한 HTTP 상태 코드와 에러 메시지, 데이터 포맷을 처리하기 위한 추가적인 메서드들을 **`BaseController`**에 구현할 수 있습니다.

---

## 중복과 변경

중복 코드는 항상 함께 수정돼야 하기 떄문에 많은 code dummy속에서 어떤 코드가 중복인지를 파악하는 일은 쉬운 일이 아니다.

그렇지만 중복 코드는 새로운 중복 코드를 부른다.

따라서 중복 코드를 제거하지 않은 상태에서 코드를 수정할 수 있는 유일한 방법은 새로운 중복 코드를 추가하는 것뿐이다.

변경에 대해서 유연하게 대처하기 위해선 기회가 생길 때마다 코드를 DRY하게 만들어야 한다(중복 제거).

객체지향 프로그래밍 언어는 타입 코드를 사용하지 않고도 중복 코드를 관리할 수 있는 효과적인 방법을 제공한다.

## 상속을 이용해서 중복 코드 제거하기

코드를 복사하지 말고 상속을 이용해 코드를 재사용하라는 것이다.

어 ?? → 이전에 말했지 않은가. 상속은 코드 재사용을 위해 사용하지 말라고 했는데, 이게 무슨 말인가 ?

헷갈릴 것이다. 필자도 그러하다… ㅋㅋ 

하지만 저자께선 일부러 이렇게 제목을 달고 설명하셨다.

이유는 간단하다. 쓰지 말라는 것이다. 이에 대해 설명하겠다.

잘못된 상속은 요구사항과 구현 사이의 차이를 더 크게 벌린다.
또한 상속을 염두에 두고 설계되지 않은 클래스를 상속을 이용해 재사용하는 것은 생각처럼 쉽지 않다는 것이다.
→ 개발자는 재사용을 위해 상속 계층 사이에 무수히 많은 가정을 세웠을 지도 모른다. 그리고 그 가정은 코드를 이해하기 어렵게 만들뿐만 아니라 직관에도 어긋날 수 있다.
→ 코드를 재사용함으로써, 상속 관계로 연결된 자식 클래스가 부모 클래스의 변경에 취약해지는 현상이 유발된다. 

<aside>
⚠️ 상속을 위한 경고1
자식 클래스의 메서드 안에서 super 참조를 이용해 부모 클래스의 메서드를 직접 호출할 경우 두 클래스는 강하게 결합된다. super 호출을 제거할 수 있는 방법을 찾아 결합도를 제거하라.

</aside>

---

# 취약한 기반 클래스 문제

취약한 기반 클래스 문제는 부모 클래스의 변경에 의해 자식 클래스가 영향을 받는 현상이다.

취약한 기반 클래스 문제는 상속이라는 문맥 안에서 결합도가 초래하는 문제점을 가리키는 용어다.
상속 관계를 추가할수록 결합도가 높아지기 떄문이다.

**객체를 사용하는 이유**는 구현과 관련된 세부사항을 퍼블릭 인터페이스 뒤로 캡슐화할 수 있기 때문이다.

기억하자. (캡 ! 상추다) + SOLID 원칙

객체지향의 기반은 캡슐화를 통한 변경의 통제다.
상속은 코드의 재사용을 위해 캡슐화의 장점을 희석시키고 구현에 대한 결합도를 높임으로써 객체지향이 가진 강력함을 반감시킨다.

## 불필요한 인터페이스 상속 문제

LIFO인 스택.

<aside>
⚠️ 상속을 위한 경고2
상속받은 부모 클래스의 메서드가 자식 클래스의 내부 구조에 대한 규칙을 깨트릴 수 있다.

</aside>

## 메서드 오버라이딩의 오작용 문제

(책에 나온 코드 참고)좋은 해결책은 메서드를 오버라이딩하고 추가되는 각 요소에 대해 한 번씩 중복되는 메서드를 호출하는 것이다.

→ 하지만, 오버라이딩된 메서드의 구현이 중복이된다는 단점이 발생한다.

<aside>
⚠️ 상속을 위한 경고3
자식 클래스가 부모 클래스의 메서드를 오버라이딩할 경우 부모 클래스가 자시의 메서드를 사용하는 방법에 자식 클래스가 결합될 수 있다.

</aside>

즉, 메서드들이 같은 클래스의 다른 메서드들 호출하는지에 대해 반드시 문서화해야 한다.
오버라이딩 가능한 메서드를 호출할 수 있는 어떤 상황에 대해서도 문서화해야한다는 것이다.

→ 이는, 내부 구현을 문서화하라는 말이다.

?????? 그럼 캡슐화 위반 아닌가? 라고 생각할 수 있따.

맞다. 결국 상속이 캡슐화를 위반함으로써 초래된 불행인 것이다.
서브클래스가 안전할 수 있게끔 클래스를 문서화하려면 클래스의 상세 구현 내역을 기술해야 한다.

상속은 코드 재사용을 위해 캡슐화를 희생한다.
캡슐화를 원한다면 코드 재사용을 포기하거나 상속 이외의 다른 방법을 사용해야한다.
즉, 캡슐화와 상속은 트레이드 오프의 산물이다.
근데, 여기서 궁금한점이 생겼다. 상속 외에 어떤 방법을 말하는지 ??

제가 아는 몇 가지 방법을 알려드리겠습니다.
(참고만 해주세요)

1. **컴포지션**
HAS -A 관계를 통해 기능을 재사용하는 방식입니다.
객체가 다른 객체의 인스턴스를 포함하고 있으며, 그 객체의 메서드를 호출하여 작업을 수행합니다.
이 방식은 삭속보다 더욱 유연하며, 동시에 캡슐화를 유지하면서 코드 재사용을 가능하게 합니다.
2. **인터페이스**
메서드의 시그니처를 정의하지만, 구현을 제공하지 않는 타입입니다.
클래스는 하나 이상의 인터페이스를 구현할 수 있으며, 이를 통해 다형성을 제공합니다.
인터페이스는 상속의 한 형태로 볼 수 있지만, 인터페이스를 통해 메서드의 구현을 강제하며, 다중 상속의 문제를 해결할 수 있습니다.
3. **전략패턴**
저도 해당 책을 읽으면서 15장의 디자인 패턴의 꽃인 전략패턴에 대해 알게 되었으며, 조금 더 자세히는 객체지향 원리와 이해의 전략패턴에 대해 학습하였습니다.
이 패턴을 사용하면 컴포지션을 통해 다양한 알고리즘을 캡슐화하고, 실행 시점에 알고리즘을 변경할 수 있습니다. 이는 유연성과 재사용성을 증가시키는 방법입니다.

### 상속의 문제 4

상속은 당연스레 부모 클래스의 구현을 재사용한다는 기본 전제를 깔고 시작한다.

따라서 코드 재사용을 위한 상속은 부모 클래스와 자식 클래스를 강하게 결합시키기 때문에 함께 수정해야 하는 상황 역시 빈번하게 발생할 수 밖에 없다.

<aside>
⚠️ 상속을 위한 경고4
클래스를 상속하면 결합도로 인해 자식 클래스와 부모 클래스의 구현을 영원히 변경하지 않거나, 자식 클래스와 부모 클래스를 동시에 변경하거나 둘 중 하나를 선택할 수밖에 없다.

</aside>

---

# 추상화에 의존하자.

이게 정답이다.

더 할말이 없다. 추상화다 그냥 .

부모 클래스와 자식 클래스 모두 추상화에 의존하도록 수정하자.

코드 중복을 제거하기 위해 상속을 도입할 때 따르는 두 가지 원칙이 있다. (저자 기준)

1. 두 메서드가 유사하게 보인다면, 차이점을 메서드로 추출하라.
메서드 추출을 통해 두 메서드를 동일한 형태로 보이도록 만들 수 있다.
2. 부모 클래스의 코드를 하위로 내리지 말고 자식 클래스의 코드를 상위로 올려라.
부모 클래스의 구체적인 메서드를 자식 클래스로 내리는 것보다 자식 클래스의 추상적인 메서드를 부모 클래스로 올리는 것이 재사용성과 응집도 측면에서 더 뛰어난 결과를 얻을 수 있다.

### 차이를 메서드로 추출하라.

- 중복 코드 안에서 가장 먼저 할 일은 차이점을 별도의 메서드로 추출하는 것이다.
- “변하는 것으로부터 변하지 않는 것을 분리하라”
”변하는 부분을 찾고 이를 캡슐화하라”라는 조언을 메서드 수준에서 적용한 것이다.

### 중복 코드를 부모 클래스로 올려라.

- 공통 코드를 옮길 때, 인스턴스 변수보다 메서드를 먼저 이동시키는 게 편한데, 메서드를 옮기고 나면 그 메서드에 필요한 메서드나 인스턴스 변수가 무엇인지를 컴파일 에러를 통해 자동으로 알 수 있기 때문이다.
- 다시 말해, 시그니처만 부모 클래스로 이동시켜야 한다.
Q. 여기서 시그니처란 무엇일까? 
또한, 시그니처만 이동시키는 것이므로 메서드를 추상 메서드로 선언하고 자식 클래스에서 오버라이딩할 수 있도록 protected로 선언하자.
Q. 여기서는 왜 protected로 선언해야 하는 것일까?
- 정답은 알려주지 않겠습니다. 
불친절하다고 느껴질 수 있겠지만, 알아보면서 공부하는 것이 좋기 때문입니다.

즉, 자식 클래스들 사이의 공통점을 부모 클래스로 옮김으로써 실제 코드를 기반으로 상속 계층을 구성할 수 있다.

이제 우리의 설계는 추상화에 의존하게 된다는 것이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/399f4360-7230-4766-9285-912ebea39919/Untitled.heic)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/075997f4-35b2-4955-b621-060d6a108880/9050977d-416a-4b15-b020-1bed47e48ca6/Untitled.heic)

# 다시 말하지만 ‘추상화가 핵심’입니다.

‘각 클래스는 서로 다른 변경의 이유를 가진다’는 것에 주목하자.

부모 클래스 역시 자신의 내부에 구현된 추상 메서드를 호출하기 때문에 추상화에 의존하며, 의존성 역전 원칙도 준수한다.

설계는 확장에는 열려있고 수정에는 닫혀있다면, 개방-폐쇄 원칙 역시 준수한다고 말할 수 있다.

이것들은 전부 클래스들이 추상화에 의존하기 때문에 얻어지는 장점들이다.

코드를 진화시키는 데 걸림돌이 된다면 추상화를 찾아내고 상속 계층 안의 클래스들이 그 추상화에 의존하도록 코드를 리팩토링하자.

## 의도를 드러내는 클래스 이름 선택하자.

당연한 얘기다.

상속 계층 안에 속한 클래스들이 구현이 아닌 추상화에 의존해야 한다는 사실이다.

이를 위해선 명확한 클래스명이 필요하다.

# 결론

10장은 상속과 캡슐화의 관계는 트레이드오프의 산물이며, 추상화에 의존하는 코드가 clean코드에 더 가까워 진다는 것을 타당한 근거와 함께 설명해주고 있습니다.
