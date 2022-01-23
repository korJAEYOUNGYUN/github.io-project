---
title: "UX를 향상시켜주는 SkeletonView"
date: 2022-01-16T17:57:18+09:00
description: "SkeletonView란 무엇일까요"
draft: false
tags: [
  "iOS",
  "SkeletonView",
  "UX"
]
categories: [
  "iOS",
  "UX"
]
series: ["SkeletonView"]
---

네트워크로 데이터를 받아오는 작업과 같이 시간이 오래걸리고, 언제 끝날지 모르는 작업들을 처리할 때, 그리고 그 작업의 결과를 화면에 보여줘야 할때 애플리케이션에서는 작업 진행 중이라는 것을 사용자에게 알려줌으로서 사용자가 작업이 완료될 때까지 기다릴 수 있게끔 유도합니다.

단순히 빙글빙글 도는 인디케이터부터, 퍼센티지를 통해 어느정도 작업이 진행되었는지를 알려주는 progress bar등 다양한 방식으로 앱에서는 사용자 경험을 향상시키고자 노력합니다. 

{{< figure src="iosbasicloading.gif" width=250 caption="iOS 기본 로딩" >}}

이번 글에서는 그 중에서도 직관적으로 어떤 컨텐츠가 로딩되고 있는지를 표현할 수 있는 스켈레톤 스크린과 그걸 iOS에서 구현한 SkeletonView라는 라이브러리에 대해서 알아보겠습니다.

### Skeleton Screen 이란?

스켈레톤 스크린이란 화면의 레이아웃과 모양에 유사하게 나오는 로딩입니다.  
그렇기 때문에 사용자가 앞으로 나올 화면을 예상할 수 있고, 사용자는 로딩이 더욱 실시간같고 빠르게 느껴지게 됩니다. 

유튜브, 페이스북등 많은 웹, 모바일 애플리케이션에서 이를 통해 사용자 경험을 증대시키고 있습니다.

{{< figure src="youtubeskeleton.PNG" width= 250 caption="유튜브 스켈레톤 화면" >}}

### Skeleton View

iOS 에서는 [SkeletonView](https://github.com/Juanpe/SkeletonView) 라는 라이브러리를 사용하면 손쉽게 스켈레톤 스크린을 구현할 수 있습니다.

스켈레톤 뷰는 UIView의 extension을 통해 기능을 제공합니다.  
스켈레톤 뷰를 사용하는 방법은 기본적으로 다음과 같습니다.  

```swift
view.isSkeletonable = true
```

을 통해 내가 원하는 뷰를 스켈레톤 뷰로 띄울 수 있게 설정할 수 있습니다.  
그 다음 아래 코드로 스켈레톤을 켜고 끌 수 있습니다.

```swift
view.showSkeleton()

view.hideSkeleton()
```

추가적으로 스켈레톤의 모양, 타이밍, 애니메이션등 다양한 설정을 해줄 수 있는데 이는 [공식문서](https://github.com/Juanpe/SkeletonView) 에서 확인해보시기 바랍니다.

한 가지 중요한 점은, showSkeleton 과 hideSkeleton을 할때  
해당 뷰의 하위 뷰들에게도 적용이 된다는 점입니다.  

> Since `SkeletonView` is recursive, and we want skeleton to be very efficient, we want to stop recursion as soon as possible. For this reason, you must set the container view as `Skeletonable`, because Skeleton will stop looking for `skeletonable` subviews as soon as a view is not Skeletonable, breaking then the recursion.

공식 문서에 따르면, 스켈레톤 뷰는 하위 뷰들을 재귀적으로 탐색하면서 isSkeletonable이 true로 설정되어 있는 뷰들에게 모두 적용이 됩니다.  
따라서 하나의 화면에 여러 스켈레톤 뷰들을 구성해놓고 일일히 스켈레톤 뷰를 끄고 킬 필요 없이, 최상위 뷰에서부터 하위 뷰까지 isSkeletonable을 true로 설정해주고 스켈레톤을 켜고 끄면 모두 적용이 되게 됩니다.

이러한 기본적인 요소들을 이해하셨다면 스켈레톤 뷰를 적용하는데에 큰 어려움을 없을 것입니다.
