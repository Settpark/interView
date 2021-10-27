## 개요

- 윈도우는 뷰들을 담는 컨테이너이다.
- 윈도우는 이벤트를 전달해주는 매개체이다.
- iOS 앱은 콘텐츠를 화면에 보여주기 위해서 최소 1개 이상의 윈도우를 가지고 있다. (보통 1개)
- 시스템 윈도우에서 생성된 이벤트들은 key window로 전달이 된다.

## 정의

> 앱의 사용자 인터페이스 배경, 뷰에 이벤트를 전달(dispatch)하는 객체
> 

## 특징

- UIWindow는 UIView의 서브클래스
- 상속 관계: [UIResponder] - [UIView] - [UIWindow] → 따라서 '뷰에 이벤트를 전달하는' 역할이 수행됨.
- 윈도우는 한 개 이상의 뷰를 포함할 수 있는 비어있는 컨테이너입니다. (새로운 콘텐츠를 보여주기 위해선 윈도우 대신 뷰를 교체해야 합니다.)
- 앱의 뷰 컨트롤러와 화면 방향전환을 용이하게 하기 위해 협업합니다.

## 윈도우를 사용하는 경우

1. 앱의 콘텐츠를 보여주기 위하여 Main Window를 제공해야하는 경우,
2. 추가적인 콘텐츠를 보여주기 위한 경우 (external display를 사용하는 경우)

## 윈도우를 생성하는 방법

1. 스토리보드로 생성하는 방식, 이 경우 Xcode가 앱의 메인 윈도우를 자동으로 제공합니다.
2. 코드로 생성하는 경우, 자동으로 제공하지 않기 때문에 윈도우를 직접 생성해야 합니다.

## 윈도우 생성에서 고려해야할 점

1. iOS 12 이하 or AppDelegate만 사용하는 경우
    1. Storyboard를 사용하는 경우, 자동으로 초기화 되기 때문에 따로 생성할 필요 없음.
    2. Programmatically하게 구성하는 경우, window를 AppDelegate의 didFinishWithLaunchingOptions에서 초기화 해주어야 합니다.
2. SceneDelegate를 사용하는 경우
    1. StoryBoard를 사용하는 경우 생성할 필요 없음
    2. 아래의 메소드에서 직접 생성
        
        ```swift
        func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        	guard let _ = (scene as? UIWindowScene) else { return }
        }
        ```