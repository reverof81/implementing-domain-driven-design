# 12장 리파지토리

- Evans
    - 전역 엑세스가 필요한 각 객체의 타입마다, 해당 타입의 모든 객체가 담긴 인메모리 컬렉션이란 허상을 제공하는 객체를 생성
    - 잘 알려진 전역 인터페이스를 통한 액세스 설정
    - 객체를 추가하거나 제거하는 메소드를 제공
    - 일정 조건에 부합되는 특성을 갖는 객체를 선택해 완전히 인스턴스화된 객체나 여러 객체의 컬렉션으로 반환하는 메소드를 제공
    - 애그리게잇에 대해서만 리파지토리를 제공

## 컬렉션 지향 리파지토리

- 하위의 영속성 메커니즘을 전혀 눈치채지 못하도록 리파지토리 인터페이스를 설계한다.
- 리파지토리는 Set 컬렉션을 흉내 내야 한다.
- 리파지토리로부터 객체를 가져오게 하고 수정할 때 이를 리파지토리에 '재저장'할 필요가 없다.
- 영속성 메커니즘이 퍼블릭 인터페이스로 전혀 표현되지 않으면서 컬렉션을 흉내 낸다.
- 영속성 객체에 일어나는 변화를 암시적(implicit) 으로 추적하는 기능
    - 암시적 읽기 시 복사
        - 저장소로부터 읽어와 재구성할 때 암시적으로 각 영속성 객체의 복사본을 만들고, 커밋 시에 클라이언트의 복사본과 자신의 복사본을 비교한다.
        - 저장소에서 하나의 객체를 읽어오도록 요청하면 이를 수행하면서 그 즉시 전체 객체의 복사본을 만든다.
        - 트랜잭션이 커밋될 때, 영속성 메커니즘은 가져온 복사본을 비교해 수정 여부를 확인하고, 변경이 발견된 모든 객체는 데이터 저장소에 해당 내용을 반영한다.
    - 암시적 쓰기 시 복사
        - 영속성 메커니즘은 모든 로드된 영속성 객체를 프록시를 통해 관리한다.
        - 클라이언트는 프록시의 존재를 알지 못한 상태로 프록시 객체의 행동을 호출하고, 진짜 객체의 행동을 반영한다.
        - 프록세 메소드가 처음으로 호출되는 시점에 객체의 복사본을 만들어 관리한다.
        - 프록시는 관리되는 객체의 상태에 일어난 변화를 추적해 더티로 표시한다.
        - 영속성 메커니즘을 통해 생성된 트랜잭션이 커밋되면, 더티한 객체를 모두 찾아서 데이터 저장소로 반영시킨다.
- 암시적 추적기능을 사용할때 고려사항
    - 아주 많은 객체를 메모리로 가져와야 하며 매우 고성능의 도메인이 필요하다면, 메모리와 실행 모두에 불필요한 오버헤드를 더하게 된다.

## 영속성 지향의 리파지토리

- 