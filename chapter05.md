# 5장 엔터티

## 엔터티를 사용하는 이유

- 한 개념을 시스템 내의 나머지 모든 객체와 반드시 구분해야 하는 제약 조건이 있을 때, 엔터티로 설계한다.
- 고유 식별자와 변화 가능성 이라는 특징이 엔터티와 값 객체 사이의 차이점이다.
- 엔터티가 모델링 도구로 적합하지 않을 때가 많기 때문에 대부분의 개념은 값으로 모델링해야 한다.

## 고유 식별자

- 엔터티 설계 초기에 고유 식별자 중심의 특성과 행동을 비롯해 이를 쿼리하는 데 도움을 주는 요소에 의도적으로 집중하고 다른 특성이나 행동을 의도적으로 무시한다.
    - 시간이 흘러도 고유성이 보존됨을 보장해줄 수 있다.
- 값 객체는 고유 식별자의 홀더 역할을 할 수 있다.
- 식별자 생성의 일반적 전략
    - 사용자는 애플리케이션에 하나 이상의 초기 고유 값을 입력한다. 애플리케이션은 입력한 값이 고유한지 확인해야 한다.
    - 애플리케이션은 내부적으로 고유성이 보장되는 알고리즘을 사용해 식별자를 생성한다.
    - 애플리케이션이 데이터베이스와 같은 영속성 저장소를 사용해 고유 식별자를 생성한다.
    - 또 하나의 바운디드 컨텍스트(시스템이나 애플리케이션)에서 먼저 고유 식별자를 결정한다. 이 식별자가 입력되거나, 사용자가 여러 선택지 중 하나를 선택한다.

### 사용자가 식별자를 제공한다

- 사람이 읽을 수 있는 식별자가 반드시 필요한 상황에선 가장 적합하다.
- 사용자 입력 값은 언제든 매칭에 사용할 엔터티의 속성으로 고려해도 되지만, 이를 고유 식별자로 사용해선 안 된다.

### 애플리케이션이 식별자를 생성한다.

- UUID 를 사용해 식별자를 생성할 경우 빠르고 사용하기 쉽지만 보기 좋은 형태는 아니다.
- 짧게 줄인 식별자는 애그리케잇의 경계 내에서 엔터티의 로컬 식별자로만 쓰일때 더 신뢰할 만하다.
- 애그리게잇 루트로서 쓰이는 엔터티는 전역 고유 식별자를 필요로 한다.
- 하나 이상의 특정 UUID 세그먼트를 활용할 경우 전역적 고유성, 가독성 등에서 장점이 있다.
    - 이런 종류의 식별자는 String 보다 사용자 지정 식별자 값 객체가 더 잘 맞다.

### 영속성 메커니즘이 식별자를 생성한다.

- 데이터베이스로 시퀀스나 증가 값을 호출한 결과는 언제나 고유하다.
- 성능적 측면에서 단점이 될 수 있기 때문에 회피 방법으로 애플리케이션 안쪽에서 캐싱하는 방법이 있다.
    - 서버 노드가 재시작되면 사용하지 않은 값은 유실된다.
    - 잃어버린 캐시가 용납될 수 없는 수준이거나 상당히 작은 수의 값 만을 준비했다면 실용적이지 않거나 불필요하다.
    - 모델이 늦은 식별자 생성만으로도 충분하다면 미리 캐싱하는 방법은 문제가 되지 않는다.

### 또 하나의 바운디드 컨텍스트가 식별자를 할당한다

- 또 다른 컨텍스트가 식별자를 할당할 땐 각 식별자의 검색과 매칭과 할당을 위한 통합이 필요하다.
- 외부 시스템의 개념을 로컬 바운디드 컨텍스트로 변환하는 문제에서 사용될 수 있다.
- 식별자 생성 전략중 가장 복잡하다.
    - 로컬 도메인 행동에 따른 변화뿐만 아니라 하나 이상의 외부 시스템에서 발생하는 일에도 의존적일 가능성이 있다.
    - 최대한 보수적으로 사용해야 한다.

### 식별자 생성의 시점이 문제가 될 때

- 식별자 생성 시점이 중요한 상황이라면, 무엇이 관련됐는지 이해해야 한다.
- 식별자 생성이 지연될 경우 equals() 를 사용하는 collection 에서 고치기 힘든 버그가 생길 수 있다.
    - 식별자를 초기에 가져와서 할당하도록 설계한다.
    - equals() 메소드를 override 해서 속성값 비교를 하도록 만든다.
        - 이방식을 사용하면 엔터티를 값 객체처럼 구현해야 한다.
        - hashCode() 메소드가 반드시 equals() 메소드와 조화를 이뤄야 한다.

### 대리 식별자

- ORM 을 위해 사용되는 도메인 식별자와 상관 없는 식별자를 대리 식별자 라고 한다.
- 외부에는 대리 속성을 감추는 편이 가장 바람직하다.
    - 추상 기본 클래스에 대리 식별자를 선언하고 private, protected 접근 제어를 할 경우 이 클래스를 확장한 엔터티의 모듈 밖에서는 가시성이 없다.
- 도메인 식별자가 데이터베이스의 기본 키 역할을 수행해야 할 필요는 없다.
- 대리 데이터베이스 기본 키는 다른 테이블내에 외래 키로서, 참조 무결성을 제공해 데이터 모델 전체에 걸쳐 사용될 수 있다.

### 식별자 안정성

- 대부분의 경우 고유 식별자는 수정하지 못하도록 보호되고, 할당된 엔터티의 수명주기에 걸쳐 안정적으로 유지돼야 한다.
- 실별자 수정을 방지하기 위해 취할 수 있는 방법
    - 세터를 클라이언트에 노출하지 않는 방법
    - 세터가 이미 존재한다면 식별자의 상태 변화로부터 엔터티 자체를 보호하기 위해 세터 내에 가드를 만드는 방법

## 엔터티의 발견과 그들의 내부적인 특성

- 바운디드 컨텍스트의 유비쿼터스 언어는 도메인 모델의 설계에 필요한 개념과 용어를 제공한다.
    - 이름으로 쓰일 명서, 이를 설명해주는 형용사, 무엇을 해야 할지 알려주는 동사 등으로 구성
    - 명사와 중요한 오퍼레이션의 이름으로 쓰일 동사의 집합만을 사용하면 모델이 가져야 하는 유창함과 풍부함을 억누를 수 있다.
    - 팀은 완전한 문장으로 이 언어를 말하게 되며, 모델은 이야기되는 언어를 분명히 반영하게 된다.

### 엔터티와 속성을 알아내기

- 요구사항을 완전히 다시 정리하고 다른 항목을 추가하고 더 분명하게 다듬는 일은, 실제로 일어나는 일이 무엇인지 훨씬 더 정확히 정의해 준다.
- 식별자가 특별한 타입을 가져야 하는 경우
    - 식별자는 광범위하게 사용될 수 있다
    - 모든 컨텍스트의 모든 다른 엔터티에 설정될 수 있다.

### 필수 행동 파헤치기

- 퍼블릭 세터는 오직 언어가 사용을 허용할 때나 하나의 요청을 완수하기 위해 여러 세터를 사용할 필요가 없을 때만 사용한다.
    - 다수의 세터는 의도를 모호하게 만든다.
    - 하나의 논리적 커맨트가 돼야 하는 어떤 결과를, 하나의 의미 있는 도메인 이벤트로 게시하기 어렵게 만든다.
- 특정 도메인 로직에 엔터티 수준을 벗어난 높은 수준의 조정자가 필요한 경우 도메인 서비스를 사용할 수 있다.
- 엔터티와 값 객체를 구분할때 변경 이란 단어가 중요하다.
- 이벤트는 최소한 두 가지를 이룰 수 있게 해준다.
    - 모든 객체의 수명주기에 걸쳐 변화를 추적할 수 있도록 해준다.
    - 외부 구독자가 변경에 맞춰 동기화 할 수 있도록 해주고, 이는 외부자에게 자율성의 잠재력을 부여한다.

### 역할과 책임

- 모델링의 한 측면은 객체의 역할과 책임을 발견하는 것이다.

#### 여러 역할을 수행하는 도메인 객체

- 객체지향 프로그래밍에서 일반적으로 인터페이스는 구현 클래스의 역할을 결정한다.
    - 올바르게 설계됐다면, 클래스는 구현하는 각 인터페이스마다 하나의 역할을 갖는다.
    - 클래스에 명시적으로 선언된 역할이 없다면(어떤 명시적 인터페이스도 구현하지 않은) 기본적으로 해당 클래스의 역할을 갖는다. (퍼블릭 메소드)
- 객체 정신 분열증 (객체 식별자의 혼란)
    - 위임된 객체가 위임받기 전 본래의 객체 식별자를 모르는 상황
    - 위임은 복잡하게 만들지 않으며 단순하게 해줄 때에만 좋은 설계다.
    - 좀 더 작은 단위로 역할 인터페이스를 만들면 도움이 될 수 있다.
        - 유스케이스별 행동도 유효성 검증과 같은 다른 역할과 연결될 수 있다.
        - 구현한 클래스 자체에 행동을 구현하기가 더 쉽도록 해준다.
        - 구현을 별도의 클래스로 위임할 필요가 없으며, 그렇기 때문에 객체 정신 분열증을 막아준다.
- 도메인 모델 인터페이스의 장점
    - 인터페이스를 사용해 클라이언트에게 흘리고 싶지 않은 모델의 구현 세부사항을 숨길 수 있다.
    - 구현 클래스는 인터페이스보다 훨씬 복잡할 수 있지만 도메인 모델 인터페이스는 기술적 구현의 세부사항에 영향을 받지 않는다.
- 어떤 설계를 선택하더라도, 유비쿼터스 언어가 기술적 선호보다 항상 우위에 서도록 한다.

### 생성

- 새로운 엔터티를 인스턴스화할 때, 이를 완전히 식별해 클라이언트가 찾을 수 있도록 충분한 상태 정보를 포착하는 생성자를 사용해야 한다.
- 때로 엔터티는 하나 이상의 고정자를 갖는데, 고정자는 엔터티의 전체 수명주기에 걸쳐 트랜잭션 일관성이 유지돼야 하는 상태다.
- 엔터티의 필수 항목들은 생성자의 매개변수로 포함되도록 한다.
- 복잡한 엔터티 인스턴스화를 위해선 팩토리를 사용한다.

### 유효성 검사

- 모델 내의 유효성 검사를 사용하는 주 이유는 하나의 특성/속성, 전체 객체, 객체의 컴포지션 등의 정확성을 확인하기 위해서다.
- 도메인 객체의 모든 특성/속성이 개별적으로 유효하다고 객체 전체가 하나의 대상으로서 유효하다는 의미는 아니다.
- 하나의 객체 전체가 유효하다고 해서 객체의 컴포지션도 유효하다고 할 수 없다.

#### 특성/속성의 유효성 검사

- 자가 캡슐화
    - 자가 캡슐화는 같은 클래스 내에서부터 모든 데이터로의 액세스가 접근자 메소드를 거쳐가도록 설계하는 방법.
    - 객체의 인스턴스 변수를 추상화할 수 있도록 해준다.
- 세터에서의 유효성 검사
    - 계약에 의한 설계 접근법 측면에서의 assertion
    - 설계하는 컴포넌트의 전제 조건, 완료 조건 고정자 등을 구체화 하도록 해준다.
    - 값을 엔터티 특성으로 할당할 때 해당 값의 특성을 가드하지 않는다면 잘못된 값이 설정되는 상황을 가드할 방법이 없다.
    - 데이터베이스 컬럼 제약조건으로 검사하는 방법에 비해 여러 장점이 존재함
        - 데이터베이스 의존적인 오류 메세지를 도메인 에러로 변경하지 않아도 됨
        - 데이터베이스 특성을 고려하지 않아도 됨
    - high-low 범위 확인을 비롯한 다른 여러 조건의 가드도 고려할 수 있다.
    - 한 엔터티의 기본적인 값들이 제대로 돼 있다면, 전체 객체와 객체 컴포지션에서 큰 단위의 유효성 검사를 수행하는 일이 훨씬 쉬워진다.

#### 전체 객체의 유효성 검사

- 완전히 유효한 특성/속성의 엔터티를 가졌다고 하더라도, 이는 반드시 전체 엔터티가 유효하다는 의미가 아니다.
    - 전체 엔터티의 유효성을 검사하기 위해선 전체 객체의 상태(특성/속성)로의 액세스가 필요하다.
    - 전체 객체에게 유용한 방법은 지연 유효성 검사다.
- 유효성 검사 컴포넌트
    - 엔터티 상태가 유효한지 결정하는 책임을 갖는다.
    - 엔터티와 같은 모듈(패키지) 안에 두자.
    - 같은 모듈안에 있지 않으면 모든 특성/속성 접근자가 퍼블릭이 되어야 하는데, 이는 여러 측면에서 바람직하지 않다.
    - 명세 패턴이나 전략 패턴을 구현할 수 있다.
    - 유효성 검사 프로세스에서 첫 번째 문제가 발생했을 때 예외를 던지는 것보단, 전체 결과를 수집하는 편이 중요하다.

#### 객체 컴포지션의 유효성 검사

- 지연 유효성 검사
    - 단순한 활동과 그 밖의 부수적인 부분을 모두 아우르는 검사가 필요한, 더욱 복잡한 행동
    - 하나 이상의 애그리게잇 인스턴스를 포함한 클러스터나 엔터티의 컴포지션이 모두 유효한지 판단한다.
    - 도메인 서비스를 사용하는편이 최선일 수 있다.
        - 도메인 서비스는 유효성 검사가 필요한 애그리게잇 인스턴스를 읽기 위해 리파지토리를 사용할 수 있다.
        - 인스턴스를 각각 별도로 실행하거나 다른 인스턴스와 묶어서 실행할 수 있다.
    - 애그리게잇 상태 모델링을 통해 부적절한 시점에 유효성 검사가 이뤄지지 않도록 방지할 수 있다.
    - 유효성 검사에 적당한 조건이 준비되면, 모델은 도메인 이벤트를 게시해 이를 클라이언트에게 알려줄 수 있다.
        - 클라이언트는 이벤트를 통해 적절한 유효성 검사 시점을 알 수 있고, 그때까지 클라이언트는 유효성 검사를 제한한다.

## 변화 추적

- 엔터티의 정의에 따라 수명주기에 걸쳐 일어나는 모든 상태 변경을 추적할 필요는 없다.
- 이벤트 소싱을 통해 모델에서 일어난 모든 변화를 추적할 수 있다.
