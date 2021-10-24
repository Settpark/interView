# Dispatch Queue

## 개요

- Dispatch Queue는 앱에서 task를 비동기적으로 동시에 수행할 수 있는 방법, task는 앱이 수행해야 하는 작업을 의미
- dispatch queues는 FIFO 구조, 따라서 항상 Queue에 추가하는 task 순서대로 시작합니다.
- GCD는 자동으로 일부 Dispatch Queue를 제공하지만, 특정 용도로 사용할 수 있는 다른 큐를 제공합니다.

## QUEUE의 종류

### Serial

- 큐에 추가된 순서대로 한번에 하나의 task를 실행합니다.관리하는 고유한 쓰레드(task마다 다 다를 수 있음)에서 실행됩니다.
- 필요한 만큼 Queue를 작성할 수 있으며, 다른 큐와 관련하여 동시에 작동합니다.
- 각 Queue는 하나의 task를 실행하지만, 각 4개의 task가 각 큐에서 실행될 수 있습니다.

### Concurrent

- 일종의 global dispatch queue로 알려진 Concurrent queue는 동시에 하나 이상의 task를 실행하지만 task에 추가된 큐에 추가된 순서대로 계속 시작됩니다. 현재 실행 중인 task는 dispatch queue에서 관리하는 고유의 스레드에서 실행됩니다. 특정 시점에서 실행되는 정확한 task의 수는 가변적이며, 시스템 조건에 따라 다릅니다.

### Main dispatch queue

- 앱의 메인 스레드에서 task를 실행하는, 전역적으로 사용 가능한 serial queue입니다. 이 큐는 앱의 실행루프와 함께 동작하여 큐에 있는 task의 실행을 실행 루프에 연결된 다른 이벤트 소스의 실행과 얽힙니다. 앱의 main 스레드에서 실행되므로, main queue는 종종 앱의 주요 동기화 지점으로 사용합니다.