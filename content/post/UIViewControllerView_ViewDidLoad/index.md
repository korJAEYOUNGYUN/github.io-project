---
title: "viewDidLoad에서 navigationController가 없는 경우?"
date: 2022-01-05T20:02:57+09:00
description: "UIViewController의 viewDidLoad 호출 시점에 대해 설명합니다."
draft: false
tags: [
  "iOS",
  "UIKit"
]
categories: [
  "iOS"
]
---

UIViewController의 viewDidLoad는 한번만 호출되기 때문에 이곳에서 View 설정과 같은 ViewController의 초기화 작업을 주로 합니다.

navigationController 역시 viewDidLoad에서 자주 다뤄지는 프로퍼티입니다.  
viewDidLoad에서 아래와 같은 코드를 자주 사용해 보셨을 것 입니다.

```swift
override func viewDidLoad() {
  ...
  navigationController?.setNavigationBarHidden(true, animated: false)
}
```

혹시 이런 상황에서 navigationController가 당연히 있을 것이라고 생각했는데, nil인 경우인 적이 있으신가요? 

{{< figure src="screenshot.png" width=900 >}}

 
분명히 navigationController를 통해서 viewController를 띄웠고  
여태까지 위와 같이 viewDidLoad에서 navigationController에 접근하는 것은 문제가 없었는데,  
 어떤 경우에서 문제가 발생하는걸까요?

이 글에서는 이러한 상황이 왜 일어나는 지에 대해서 알아보도록 하겠습니다.

### viewDidLoad의 호출 시점

이런 상황은 viewDidLoad 호출되는 시점과 관련이 있습니다.

공식문서를 살펴보면, viewDidLoad의 호출 시점에 대해서 이렇게 나와 있습니다.

> *This method is called after the view controller has loaded its view hierarchy into memory*.

뷰 컨트롤러의 뷰가 메모리에 로딩되었을 때 호출된다고 합니다.  
그럼 뷰는 언제 메모리에 로딩될까요? 

다음으로 loadView에 대한 공식 문서입니다.

> *The view controller calls this method when its view property is requested but is currently nil.*

뷰 프로퍼티가 요청되었을 때 뷰가 없으면 뷰를 로딩한다고 나와 있습니다.  
이와 같이 우리가 ViewController가 띄워지기 전에 미리 해당 뷰컨의 view에 접근할 경우 viewDidLoad가 호출된다는 것을 알 수 있습니다.

상황에 대한 정확한 이해를 위해 코드를 통해 확인해보도록 하겠습니다.

### 샘플 코드

##### 일반적인 상황

```swift
class FirstViewController: UIViewController {
  ...
  
  func moveToSecondView() {
    navigationController?.pushViewController(SecondViewController(), animated: true)
  }
}

class SecondViewController: UIViewController {
  override func viewDidLoad() {
  ...
    // do something with navigationController
    // navigationController != nil
    self.navigationController...
  }
}
```

위와 같이 일반적인 상황에서 우리는 뷰컨트롤러를 생성하고 바로 화면 전환을 합니다.  
그러면 화면 전환 전에는 두 번째 뷰컨트롤러의 view를 사용하지 않으니 viewDidLoad가 호출되지 않고 화면 전환될 때 호출되게 됩니다.  
화면 전환이 될 때에는 navigationController를 통해서 화면전환을 시켰으니 두 번째 뷰컨트롤러에서 navigationController에 아무 문제없이 접근할 수 있습니다.

그럼 문제가 되는 다른 상황의 코드를 보겠습니다.

##### navigationController가 없는 상황

```swift
class FirstViewController: UIViewController {
  let secondVC: SecondViewController
  
  // this method is called before moveToSecondView()
  func configureSecondView() {
    // do something with secondVC's view
    secondVC.view...
  }
  
  func moveToSecondView() {
    navigationController?.pushViewController(SecondViewController(), animated: true)
  }
}

class SecondViewController: UIViewController {
  override func viewDidLoad() {
    ...
    // do something with navigationController
    // but navigationController is nil
    self.navigationController...
  }
}
```

위 코드에서는 첫 번째 뷰컨트롤러에서 두 번째 뷰컨트롤러로 화면 전환을 하기 전에 configureSecondView 메소드를 통해 두 번째 뷰컨트롤러의 뷰에 접근을 하고 있습니다.  
이때, 두 번째 뷰컨트롤러의 viewDidLoad가 호출되며 이 시점에서 두 번째 뷰컨트롤러의 viewDidLoad에서 navigationController는 nil 입니다. moveToSecondView 호출을 하지 않았기 때문에 두 번째 뷰컨트롤러에 네비게이션에 대한 어떠한 동작도 하지 않았기 때문입니다.

당연히 이런 상황에서 navigationController를 사용하는 코드는 optional chanining에 걸려서 동작하지 않을 것이고 force unwrapping을 하였다면 앱이 터지는 상황까지 갈 것입니다.

### 정리

이와 같이 우리는 viewDidLoad 에서 초기화 및 설정 작업을 해주지만, 여기서 navigationController가 항상 존재하는 것은 아니라는 점을 알고 있어야 합니다.

자식 뷰컨트롤러의 viewDidLoad에서 navigationController가 없는 상황이 발생할 경우, view에 대한 접근 시점을 화면 전환 이후에 하도록 늦추거나  navigationController를 부모 뷰컨트롤러에서 설정한 후 화면 전환 할 때 해당 navigationController를 통해 화면 전환을 해주면 이러한 상황을 해결할 수 있습니다.

