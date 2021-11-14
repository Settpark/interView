# CaptureList 재정리

## 재정리 이유

- bind { }, flatMap { }, map { } 등에서 발생하는 캡처리스트 이슈의 이해를 더욱 높이기 위함

## 참조의 종류

- 강한 참조: retain count를 증가 시킵니다.
- 약한 참조: retain count를 증가 시키지 않습니다. 참조하는 인스턴스가 메모리에서 해제되어 없을 수도 있다는 것을 예상해야 합니다. 자신이 참조 횟수를 증가시키지 않았기 때문에 다른 곳에서 강한 참조하던 프로퍼티나 변수에서 참조 횟수를 0으로 감소시켜 버리면 자신이 참조하던 인스턴스가 메모리에서 해제되기 때문입니다.
- 미소유 참조: retain count를 증가 시키지 않습니다. 증가시키진 않지만 자신이 참조하는 인스턴스가 항상 메모리에 존재할 것이라고 기대하고 동작합니다. 미소유참조를 하면서 메모리에서 해제된 인스턴스에 접근하려 한다면 잘못된 메모리 접근으로 런타임 오류가 발생해 프로세가 강제로 종료됩니다. 따라서 미소유참조는 해당 인스턴스가 메모리에서 해제되지 않는다는 확신이 있을 때 사용해야 합니다.

## 캡처리스트

### 클로저의 강한참조

- 강한 참조 순환 문제는 두 인스턴스끼리의 참조일 때만 발생하는 것이 아닌 클로저가 인스턴스 프로퍼티일 때나, 클로저의 값 획득 특성 때문에 발생합니다.
- 강한 참조 예시: deinit 호출되지 않음
    
    ```swift
    class Person {
        let name: String
        let hobby: String?
        
        lazy var introduce: () -> String = {
            var introduction: String = "My name is \(self?.name)."
            
            guard let hobby = self?.hobby else {
                return introduction
            }
            introduction += " "
            introduction += "My hobby is \(hobby)"
            
            return introduction
        }
        
        init(name: String, hobby: String? = nil) {
            self.name = name
            self.hobby = hobby
        }
        
        deinit {
            print("\(name) is being deinitialized")
        }
    }
    
    var park: Person? = Person(name: "park", hobby: "piano")
    print(park?.introduce())
    park = nil
    ```
    
- 클로저는 Capture를 통해 self(person)를 강한 참조하고 있기 때문에, deinit을 해도 Person의 남아있는 참조 카운트 때문에 deinit이 호출되지 않습니다.
- 이 문제를 Capture list로 해결할 수 있으며, 이는 클로저 내부에서 참조하는 타입의 규칙을 제시해줄 수 있는 기능입니다. 예를 들어 클로저 내부의 self 참조를 약한 참조로도 강한 참조로도 규정할 수 있습니다.
    
    ```swift
    lazy var introduce: () -> String = { [weak self] in //Capture List
        var introduction: String = "My name is \(self?.name)."
        
        guard let hobby = self?.hobby else {
            return introduction
        }
        introduction += " "
        introduction += "My hobby is \(hobby)"
                
        return introduction
    }
    ```