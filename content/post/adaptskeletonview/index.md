---
title: "Skeletonview 적용하기"
date: 2022-01-23T16:35:40+09:00
draft: false
description: "기존 뷰에 스켈레톤 뷰를 깔끔하게 적용하기 위한 방법"
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

### 스켈레톤 뷰 구성 하기

스켈레톤 뷰는 기존 뷰에 isSkeletonable을 통해 별다른 추가 작업없이 스켈레톤 스크린을 구현할 수 있게 해줍니다.  
하지만 스켈레톤 뷰를 몇 번 사용해보니 우리가 정학히 원하는 모양을 잡기 위해서 기존의 뷰를 그대로 활용하기가 애매해지는 부분이 있었습니다.  
기존의 뷰의 크기가 고정되어 있지 않고 동적인 경우, 스켈레톤 뷰를 위해서 스켈레톤을 띄우는 동안에는 원하는 크기로 고정을 시켜주어야 합니다.

예를 들어, width를 잡지 않고 사용하는 UILabel의 경우, text 크기에 따라서 width가 정해지게 됩니다.  
이 label에 스켈레톤을 적용하려면, width를 잡아주거나, 일정 크기만큼의 dummy text를 넣어주어야 합니다.  
이는 내가 원하지 않은 설계가 되거나, 가독성이 떨어지는 코드가 될 가능성이 있습니다.

{{< figure src="ex1.png" width= 250 caption="기존 뷰" >}}

다음과 같은 기존 뷰에 스켈레톤 뷰를 적용해보겠습니다.  
해당 뷰는 제목과 설명을 보여주는 두 개의 UILabel로 구성되어 있습니다.  
또한, 레이블에 텍스트는 데이터가 로딩 된 후 채워진다고 가정합니다.

```swift
titleLabel.isSkeletonable = true
descriptionLabel.isSkeletonable = true
```

위와 같이 스켈레톤을 적용해주고 showSkeleton()을 호출해주면 데이터가 없는 상태에서 두 레이블의 텍스트가 비어있고, 텍스트가 비어있으니까 width가 0이기 때문에 아무것도 뜨지 않습니다.

이를 해결하기 위해 먼저, 레이블의 너비를 고정시켜보겠습니다.

```swift
titleLabel.widthAnchor.constraint(equalToConstant: 300)
...
descriptionLabel.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -40),
descriptionLabel.heightAnchor.constraint(equalToConstant: 300)
```

이런 방식으로 데이터가 들어오기 전에 스켈레톤 뷰를 구성해 줄 수 있지만, 이렇게 되면 데이터가 들어온 후 레이아웃 제약을 바꿔주어야하므로 사용하기 불편합니다.

다른 방법으로는 레이블에 가짜 데이터를 채워서 너비가 잡혀있게 하는 것인데 이는 코드가 보기에 깔끔하지 않습니다.

### 분리해서 관리하기

마지막으로 이를 해결하기 위해 기존 뷰와 같은 모양과 레이아웃을 가진 뷰를 만들어 이를 스켈레톤 시 보여줬다가 스켈레톤이 끄면 숨겨버리는 방식을 사용하겠습니다.

```swift
class MySkeletonView: UIView {
  let titleView: UIView = {
    let view = UIView()
    view.isSkeletonable = true
    view.translatesAutoresizingMaskIntoConstraints = false
    return view
  }()
  
  let descriptionView: UIView = {
    let view = UIView()
    view.isSkeletonable = true
    view.translatesAutoresizingMaskIntoConstraints = false
    return view
  }()
  
  init() {
    ...
    
    NSLayoutConstraint.activate([
      titleView.topAnchor.constraint(equalTo: topAnchor, constant: 100),
      titleView.leadingAnchor.constraint(equalTo: leadingAnchor, constant: 20),
      titleView.widthAnchor.constraint(equalToConstant: 100),
      titleView.heightAnchor.constraint(equalToConstant: 40),
      
      descriptionView.topAnchor.constraint(equalTo: titleView.bottomAnchor, constant: 40),
      descriptionView.leadingAnchor.constraint(equalTo: leadingAnchor, constant: 20),
      descriptionView.trailingAnchor.constraint(equalTo: trailingAnchor, constant: -20),
      descriptionView.heightAnchor.constraint(equalToConstant: 300)
    ])
  }
}
```

새로 만들어서 분리한 스켈레톤 뷰입니다. 기존 뷰의 껍데기만 흉내내고 있습니다.

```swift
func showMySkeleton() {
  skeletonView.isHidden = false
  skeletonView.showSkeleton()
}
  
func hideMySkeleton() {
  skeletonView.hideSkeleton()
  skeletonView.isHidden = true
}
```

이를 기존 뷰에서 사용하여 스켈레톤을 띄웁니다.

{{< figure src="ex2.gif" width=250 caption="스켈레톤 뷰 적용된 모습" >}}

### 정리

기존 뷰와 스켈레톤용 뷰를 분리하여 구성하면 UI 디자인이 바뀔 때마다 기존 뷰와 스켈레톤 용 뷰 두 개를 수정해야한다는 단점이 있지만, 스켈레톤 용 뷰가  깔끔하고 단순해져서 관리하기가 더 편하다는 장점이 있습니다.

cell 역시 이런식으로 스켈레톤용을 따로 만들어서 사용하면 관리하기가 수월해집니다.
