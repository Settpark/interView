## 정의

- 리스폰더 오브젝트들이 동적으로 구성된 이벤트 전달 체인입니다.
- 앱은 리스폰더 오브젝트를 이용하여 이벤트를 처리합니다.
- 여기서 리스폰더 오브젝트란, UIResponder 클래스의 인스턴스를 가리킵니다.

## 작동원리

- 리스폰더는 이벤트를 받았다면 즉시 그것을 처리하거나 다른 리스폰더 이벤트로 넘겨주어야 합니다.
- 앱이 이벤트를 전달받으면 UIKit은 자동적으로 이벤트를 처리하기에 가장 적절한 리스폰더 오브젝트로 이벤트를 전달합니다.
- 여기서 가장 적절한 리스폰더를 First Responder라고 합니다.
- 이 리스폰더 체인은 앱의 리스폰더 오브젝트에 따라 동적으로 구성됩니다.

## 이벤트의 First Responder 정하기

- 가장 적절한 리스폰더 오브젝트에 전달 하는데 이 전달하는 기준은 무엇일까..? → 발생하는 이벤트 타입에 따라 결정
- 여기서의 UIEvent는 인스턴스 인데 type에 따라 여러 이벤트가 있습니다.

## 어떤 리스폰더가 이벤트를 포함하고 있는지 결정하기

- UIKit은 뷰 기반 hit-testing을 사용하여 터치 이벤트가 어디서 발생하였는지 결정합니다.
- 구체적으로 UIKit은 터치 이벤트가 발생한 위치와 뷰 계층의 뷰 오브젝트의 bounds를 비교합니다.
- UiView의 hitTest(_: with:) 메서드는 뷰 계층을 돌아다니며 이벤트가 발생한 터치 지점을 포함하고 있는 뷰의 계층 상 가장 최하단 서브뷰를 찾고, 그것을 터치 이벤트의 First Responder로 지정합니다.
- 터치 이벤트가 발생하더라고 발생한 위치가 뷰의 bounds의 밖에 있다면 hittest(_:with:)는 해당뷰와 서브뷰 모두 무시합니다.
- 결과적으로 clipsToBounds가 false로 설정되어 있을 때, 뷰의 bounds를 벗어난 서브뷰에서 터치가 발생하더라고 hittest(_:with:)는 뷰를 리턴하지 않습니다.
- 추가적으로 hidden이거나 alpha값이 0.01 이하거나 user interaciton이 disabled라면 무시됩니다.

## 리스폰더 체인을 변경하기

- 리스폰더 오브젝트의 next속성을 변경하여, 리스폰더 체인을 변경할 수 있습니다. 기본적으로 아래의 계층 구조를 따릅니다.
- [UILabel, UITextField, UIButton] → UIView → UIView → UIViewController → UIWindow → UIApplication → UIApplicationDelegate