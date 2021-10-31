## 왜 UI 업데이트는 main에서 해야하는가?

- Cocoa Touch 앱에서 UIApplication의 인스턴스가 Main thread에 attach되기 때문
- (UIApplication은 앱을 시작할 때 인스턴스화 되는 앱의 첫번째 부분인데, 앱의 run loop를 포함하여 main event loop를 설정하고 event처리를 시작합니다.
- 앱의 main event loop는 touch, gesture등의 모든 UI event를 수신합니다.

## 왜! main.sync를 사용하면 안될까??

1. Deadlock (교착상태)에 의해 사용하면 안됩니다!
    1. Deadlock이란 두개 이상의 작업이 서로 상대방의 작업이 끝나기만을 기다리고 있기 때문에 결과적으로 아무것도 완료하지 못하는 상태.
    
    ```swift
    let myqueue = DispatchQueue.init(label: "label")
    myqueue.async {
    	  myqueue.sync {
            // 외부 블록이 완료되기 전에 내부 블록은 시작되지 않습니다.
    				// 외부 블록은 내부 블록이 완료되기를 기다립니다.
        }
    		//이 부분은 영원히 실행되지 않습니다.
    }
    ```
    
2. 위 예시에서 sync 내부 블록은 외부 블록이 완료될 때까지 기다리고, sync 블록 이후의 동작은 sync 내부 블록이 완료될 때까지 기다리는 deadlock 이 발생.

결론. DispatchQueue.main 은 프로그램의 메인 스레드에서 작업을 실행하는 전역적으로 사용이 가능한 **serial queue** 입니다. 앱의 run loop 와 함께 작동하며, 대기 중인 동작을 run loop 의 다른 이벤트 처리와 조율해줍니다. 이러한 DispatchQueue.main 에서 sync 를 호출하게 된다면 끊임없이 앱의 이벤트 처리를 하고 있던 메인 스레드가 sync 호출에 의해 멈추게 되고 위에서 예시로 들었던 코드와 같이 deadlock 이 발생하게 됩니다.

## 그럼 main.sync는 도대체 언제 사용할까?

- 백그라운드 스레드에서 이루어지는 작업들 사이에 순서에 맞게 메인 스레드에서 어떠한 작업이 이루어져야 할 때 사용합니다.
    
    ```swift
    DispatchQueue.global().async {
      // UI 업데이트 전 실행되어야만 하는 코드
      DispatchQueue.main.sync {
          // UI 업데이트
      }
      // UI 업데이트 후에 실행되어야만 하는 코드
    }
    ```
    
    - 백그라운드 스레드에서 작업이 진행되는 중 UI 업데이트가 이루어져야 하는 상황입니다. 좀 더 구체적으로 예를 들자면 테이블 뷰나 콜렉션 뷰에 셀을 추가하거나 제거할 때, 셀의 추가, 제거 작업이 이루어지던 중 네트워크 통신과 같은 다른 비동기 작업으로 인해 DataSource 의 데이터가 변경된다면 DataSource 의 데이터와 뷰의 데이터가 일치하지 않아 에러가 발생하게 됩니다. 이를 방지하기 위해 사용합니다.
    - 애플의 [PHPhotoLibraryChangeObserver](https://developer.apple.com/documentation/photokit/phphotolibrarychangeobserver) 문서 ← 참고 자료