# Protocol

## 프로토콜 (Protocol)

## 프로토콜의 정의

1. 특정 역할을 하기 위한 메서드, 프로퍼티, 기타 요구사항 등의 청사진을 정의
2. 기능을 정의를하고 제시만 할 뿐, 기능을 직접 구현하진 않습니다
3. 하나의 타입으로 사용되기 때문에 타입 사용이 허용 되는 모든 곳에 프로토콜을 사용할 수 있습니다.

## 프로토콜 요구사항

- 프로토콜은 타입이 특정 기능을 실행하기 위해 필요한 기능을 요구합니다.
- 프로퍼티 요구, 메서드 요구, 가변 메서드 요구 (mutating), 이니셜라이저 요구

## 프로토콜 상속과 클래스 전용 프로토콜

- 프로토콜은 하나 이상의 프로토콜을 상속 받아 사용할 수 있습니다. 클래스의 상속 문법과 유사합니다.
    
    ```swift
    protocol Readable {
    	func read()
    }
    
    protocol ReadSpeakable: Readable {
    	func write()
    }
    ```
    
- 클래스 전용 프로토콜을 선언할 수도 있습니다. 아래와 같이 선언할 수 있으며, swift 4 이후로 class라는 키워드는 deprecation 되었고, 현재는 AnyObject로 사용됩니다.
    
    ```swift
    protocol Readable: class {
    	func read()
    }
    
    protocol Readable: AnyObject {
    	func read()
    }
    ```
    

## 프로토콜 조합과 확인

- 프로토콜 조합은 하나의 매개변수가 여러 프로토콜을 준수해야 하는 경우 사용합니다.
- 예시 코드:
    
    ```swift
    protocol Named {
        var name: String { get }
    }
    
    protocol Aged {
        var age: Int { get }
    }
    
    class something: Named {
        var name: String = "박정하"
    }
    
    class something2: Named, Aged {
        var name: String = "박정하"
        var age: Int = 29
        
    }
    var someVar: Named & Aged
    
    someVar = something()
    someVar = something2()
    ```
    
- 프로토콜 확인은 아래와 같은 코드로 is와 as를 통해서 확인할 수 있습니다.
    
    ```swift
    var someVar: Named
    someVar = something()
    print(someVar is Aged)
    
    if let temp = someVar as? Aged {
        print("\(temp) is Aged") // 출력하지 않음
    }
    if let temp = someVar as? Named {
        print("\(temp) is Named") // 출력함
    }
    ```
    

## 프로토콜의 선택적 요구

- 선택적 요구를 하면 해당 요구 사항을 꼭 구현할 필요는 없습니다.
- 아래의 코드와 같이 `optional`과 `@objc` 키워드를 사용하면 선택적 요구를 명시할 수 있습니다.
    
    ```swift
    import Foundation
    
    @objc protocol walkable {
        @objc optional func fly() //구현하지 않아도 됨
        func walk()
    }
    
    class Bird: walkable {
        func walk() {
            print("can walk")
        }
    }
    
    let bird = Bird()
    bird.walk()
    ```