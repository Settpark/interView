## ARC를 사용하는 이유는 무엇인가요?

- 매번 전달할 때마다 값을 복사해 전달하는 값 타입과는 달리 인스턴스가 참조를 통해 여러곳을 접근하기 때문에 언제 메모리에서 해제되는지가 중요한 문제입니다. 따라서, 적절한 시점에 메모리에서 해제되지 않으면 한정적인 메모리 자원을 낭비하게 되며, 이는 성능의 저하로 이어지게 됩니다.
- ARC는 컴파일과 동시에 인스턴스를 메모리에서 해제하는 시점이 결정함.

## ARC 강한참조로 인해 메모리에서 해제 되지 않는 경우

### 강한 참조 순환 문제

- 인스턴스끼리 서로가 서로를 강한참조할 때, 강한참조 순환(Strong Reference Cycle)문제가 발생합니다.
- 예시: 아래의 코드는 영원히 deinit을 호출하지 않는다!
    
    ```swift
    class Person {
        let name: String
        
        init(name: String) {
            self.name = name
        }
        
        var room: Room?
        
        deinit {
            print("\(name) is being deinitalized")
        }
    }
    
    class Room {
        let number: String
        
        init(number: String) {
            self.number = number
        }
        
        var host: Person?
        
        deinit {
            print("Room \(number) is deinitialized")
        }
    }
    
    var park: Person? = Person(name: "park") // Person 인스턴스 참조 횟수 1
    var room: Room? = Room(number: "305") // room 인스턴스 참조 횟수 1
    
    room?.host = park // 참조 횟수 2
    park?.room = room // 참조 횟수 2
    
    park = nil // 참조 횟수 1
    room = nil // 참조 횟수 1
    
    //Person 인스턴스를 참조할 방법 상실 - 메모리에 잔존
    //Room 인스턴스를 참조할 방법 상실 - 메모리에 잔존
    ```
    

### 강한참조 순환 문제를 수동으로 해결

- 내부 변수를 먼저 nil 값을 할당함으로써 수동으로 해결
    
    ```swift
    var park: Person? = Person(name: "park") // Person 인스턴스 참조 횟수 1
    var room: Room? = Room(number: "305") // room 인스턴스 참조 횟수 1
    
    room?.host = park // 참조 횟수 2
    park?.room = room // 참조 횟수 2
    
    park?.room = nil
    park = nil // 참조 횟수 1
    
    room?.host = nil
    room = nil // 참조 횟수 1
    ```
    
    - 실수로 해제 하지 않거나 너무 많은 프로퍼티로 해당 문제를 완벽히 해결하지 못할 수 있음

### 약한 참조로 강한참조 순환 문제를 해결

- `weak` 키워드로 인스턴스 참조 횟수를 증가시키지 않음. 옵셔널 변수만 약한 참조를 할 수 있습니다.
- 예시: 클래스 내부 변수를 weak 키워드를 써줌으로써 해결
    
    ```swift
    class Person {
        let name: String
        
        init(name: String) {
            self.name = name
        }
        
        weak var room: Room?
        
        deinit {
            print("\(name) is being deinitalized")
        }
    }
    
    class Room {
        let number: String
        
        init(number: String) {
            self.number = number
        }
        
        weak var host: Person?
        
        deinit {
            print("Room \(number) is deinitialized")
        }
    }
    ```
    

### 미소유참조

- 약한 참조와 다르게 항상 인스턴스가 항상 메모리에 존재할 것이라는 전제를 기반으로 동작합니다.
- 자신이 참조하는 인스턴스가 메모리에서 해제되더라도 스스로 nil을 할당해주지 않습니다.
- 따라서 해당 변수는 옵셔널이나 변수가 아니어도 됩니다.
- 미소유참조 사용 예시:
    
    ```swift
    import Foundation
    
    class Person {
        let name: String
        
        var card: CreditCard?
        
        init(name: String) {
            self.name = name
        }
        
        deinit {print("\(name) is being deinitalized")}
    }
    
    class CreditCard {
        let number: UInt
        unowned let owner: Person
        
        init(number: UInt, owner: Person) {
            self.number = number
            self.owner = owner
        }
        
        deinit {
            print("Card #\(number) is being deinitalized")
        }
    }
    
    var park: Person? = Person(name: "park")
    
    if let person: Person = park {
        // creditcard 인스턴스 참조 횟수: 1
        person.card = CreditCard(number: 1004, owner: person)
        // person 인스턴스 참조 횟수: 1
    }
    
    park = nil // Person 인스턴스 참조 횟수: 0
    //CreditCard 인스턴스 참조 횟수: 0
    ```
    
    - 실행 결과
        
        ```swift
        park is being deinitalized
        Card #1004 is being deinitalized
        ```
        

### 미소유참조에서 발생할 수 있는 문제점

- 메모리에서 해제된 인스턴스에 접근하려 한다면 잘못된 메모리 접근으로 런타임 오류가 발생해 프로세스가 강제로 종료됩니다.