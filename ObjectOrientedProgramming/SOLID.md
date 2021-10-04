## SRP 단일 책임 원칙 (Single Response Principle)

- 한 클래스는 하나의 책임만 가져야 한다.
- 올바른 클래스 이름은 해당 클래스의 책임을 나타낼 수 있는 가장 좋은 방법
- 무조건 책임을 분리한다고, SRP가 적용되는 것이 아니다. 각 객체 간의 응집력이 있다면 병합이 순 작용의 수단이 되고 결합력이 있다면 분리가 순 작용의 수단이 됩니다.

## OCP 개방 폐쇄 원칙 (Open/closed principle)

- 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- 변경을 위한 비용은 가능한 줄이고 확장을 위한 비용은 가능한 극대화 해야한다는 의미로, 요구사항의 변경이나 추가사항이 발생하더라도 기존 구성요소의 수정이 일어나지 말아야 하며, 기존 구성요소를 쉽게 확장해서 재사용할 수 있어야 한다는 뜻입니다.
- 변경 (확장)될 것과 변하지 않을 것을 엄격히 구분합니다.
- **이 두 모듈이 만나는 지점에 인터페이스를 지정합니다.**
- 구현에 의존하기보다 정의한 인터페이스에 의존하도록 코드를 작성합니다.
- 구현 예시
    
    ```swift
    protocol StringInstrument {
    	var serialNumber: String {get set}
    	var stringInstrumentSpec: StringInstrumentSpec {get set}
    }
    
    protocol StringInstrumentSpec {
    	var price: Int
    	var model: String
    }
    
    class Guitar: StringInstrument {
    	init(serialNumber: String, guitarSpec: StringInstrumentSpec) {
    		self.serialNumber = serialNumber
    		self.stringInstrumentSpec = guitarSpec
    	}
    }
    
    class GuitarSpec: StringInstrumentSpec {
    }
    
    class Violin: StringInstrument {
    	init(serialNumber: String, violinSpec: StringInstrumentSpec) {
    		self.serialNumber = serialNumber
    		self.stringInstrumentSpec = violinSpec
    	}
    }
    
    class ViolinSpec: StringInstrumentSpec {
    }
    ```
    

## LSP 리스코프 치환 원칙 (Liskov substitution principle)

- 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.
- 한마디로, **서브타입은 언제나 기반타입으로 교체할 수 있어야 한다.**
- 서브 타입은 기반 타입이 약속한 규약을 지켜야 합니다.
- 상속은 구현상속이든 인터페이스 상속이든 궁극적으로는 다형성을 통한 확장성 획득을 목표로 합니다.
    - 확장성 획득을 고려하지 않은 코드 (list만 사용할 때)
    
    ```swift
    void func() {
    	LinkedList list = new LinkedList();
    	modify(list);
    }
    
    void modify(LinkedList list) {
    	list.add(...);
    	doSomethingWith(list);
    }
    ```
    
    - 확장성 획득을 고려한 코드 (List, HashSet은 모두 Collection interface를 상속하고 있습니다)
    
    ```swift
    void func() {
    	Collection collection = new HashSet();
    	modify(collection);
    }
    
    void modify(Collection collection) {
    	collection.add(...);
    	doSomethingWith(collection);
    }
    ```
    
- 적용 방법
    1. 만약 두 개체가 똑같은 일을 한다면 둘을 하나의 클래스로 표현하고 이들을 구분할 수 있는 필드를 둡니다.
    2. 똑같은 연산을 제공하지만, 이들을 약간씩 다르게 한다면 공통의 인터페이스를 만들고 둘이 이를 구현합니다.
    3. 공통된 연산이 없다면 완전 별개인 2개의 클래스를 만듭니다.
    4. 만약 두 개체가 하는 일에 추가적으로 무언가를 더 한다면 구현 상속을 사용합니다.

## ISP 인터페이스 분리 원칙(Interface segregation principle)

- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 보다 낫다.
- 만약 어떤 클래스를 이용하는 클라이언트가 여러 개고 이들이 해당 클래스의 특정 부분집합만을 이용한다면, 이들을 따로 인터페이스로 빼내어 클라이언트가 기대하는 메시지만을 전달할 수 있도록 합니다. SRP가 클래스의 단일 책임을 강조한다면 ISP는 인터페이스의 단일책임을 강조합니다. **하지만, ISP는 어떤 클래스 혹은 인터페이스가 여러 책임 혹은 여러 역할을 갖는 것을 인정합니다.** 이러한 경우 ISP가 사용되는데 SPR가 클래스 분리를 통해 변화에의 적응성을 획득하는 반면, ISP에서는 인터페이스 분리를 통해 같은 목표에 도달합니다.
- **하지만, ISP는 어떤 클래스 혹은 인터페이스가 여러 책임 혹은 여러 역할을 갖는 것을 인정**에 대한 이해
    - 인터페이스가 여러 역할을 갖는 경우가 있다. 가령 예를 들어 '이체'라는 인터페이스는
        1. 트랜잭션을 시작한다.
        2. 상대의 계좌 존재 여부를 확인한다.
        3. 고객의 계좌에 원하는 금액을 출금한다.
        4. 상대의 계좌에 입금한다.
        5. 트랜젝션을 마친다 절차를 거친다.
    - 위에 참여하는 역할들은 서로 응집성이 결여된다.
    - 하지만 특정 비즈니스 목적에 의해 하나의 처리 절차로 묶이면서 '이체'라는 인터페이스 만족시킨다.
    - 다시 말하자면, '이체'라는 인터페이스는 여러 역할의 묶음으로 구현된다.(1:n 관계의 인터페이스와 역할)
    - **이 때, '이체' 인터페이스는 이런 역할의 묶음의 Facade가 된다.**

## DIP 의존 관계 역전 원칙 (Dependency inversion principle)

- 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다.
- 의존 관계의 역전 Dependency Inversion이란 구조적 디자인에서 발생하던 하위 레벨 모듈의 변경이 상위 레벨 모듈의 변경을 요구하는 위계관계를 끊는 의미의역전입니다. **실제 사용 관계는 바뀌지 않으며,** 추상을 매개로 메시지를 주고 받음으로써 관계를 최대한 느슨하게 만드는 원칙입니다.
- 구현 예시
    
    ```swift
    class PolicyLayer: MechanismAbstractLayer {
    	var policy: Bool
    }
    
    protocol MechanismAbstractLayer {
    	var policy: Bool {get set}
    }
    
    class Mechanism: UtilityAbstractLayer {
    	var policyLayer: PolicyLayer // (x) 구체화에 의존
    	var policyLayer: MechanismAbstractLayer
      var mechanism: Bool
    }
    
    protocol UtilityAbstractLayer {
    	var mechanism: Bool {get set}
    }
    
    class UtilityLayer {
    	var mechanism: Mechanism // (x)
    	var mechanism: UtilityAbstractLayer
    }
    ```