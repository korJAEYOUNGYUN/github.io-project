---
title: "alignmentGuide 활용하기"
date: 2022-06-02T19:58:34+09:00
draft: false
tags: [
  "iOS",
  "SwiftUI"
]
categories: ["iOS", "SwiftUI"]
image: "thumbnail.jpg"
---

SwiftUI에서는 alignmentGuide 메소드를 통해 정렬 기준을 커스터마이징할 수 있습니다.   
이를 통해서 우리는 단순히 제공되는 정렬이 아닌, 우리만의 기준으로 뷰들을 정렬시킬 수 있습니다.

### func alignmentGuide(g, computeVlue) -> some View

alignmentGuide 메소드는 **g**와 **computeValue** 두 가지 인자를 받습니다.

g는 HorizontalAlignment 또는 VerticalAlignment인데, 해당 Alignment에 대한 정렬 기준을 나타낸다는 뜻입니다.  
예를들어 g에 HorizontalAlignment.leading를 주게 되면 HorizontalAlignment.leading에 대한 기준을 alignmentGuide를 통해 커스텀한다는 뜻입니다.

두 번째 인자인 computeValue는 (ViewDimensions) -> CGFloat 클로저 입니다. 이 클로저는 정렬에 사용할 offset을 계산합니다.  
클로저에서는 ViewDimensions를 주는데,  ViewDimensions는 해당 뷰의 크기와 alignmentGuide들을 제공합니다.

**결과적으로 computeValue에서 계산된 offset이 g에 적용되어 g에 대한 정렬이 바뀌게 되는 것입니다.**

### 예제 - top 정렬을 center 정렬로 바꾸기

예제를 통해서 alignmentGuide 활용법에 대해서 알아보도록 하겠습니다.

우리는 책의 목록을 나타내고자 합니다.
책의 목록은 아래와 같이 단순하게 책 제목과 책 제목 왼쪽에 점이 붙어 있는 형태입니다.

- 책1
- 책2

이를 구현하기 위해서 생각나는대로 코드를 작성해봅니다.

```swift
struct BookItemView: View {
  let title: String

  var body: some View {
    HStack(alignment: .top) {
      Image(systemName: "circle.fill")
        .resizable()
        .frame(width: 8, height: 8)

      Text(title)
    }
  }
}
```

위 코드처럼 BookItemView를 만들고 이를 List안에 ForEach를 통해서 책의 목록을 구현하였습니다.  
하지만 여기에는 문제가 있습니다.

{{< figure src="wrong1.png" width=250 caption="정렬이 맞지 않는 목록 아이템" >}}

위 이미지처럼 동그라미 아이콘과 책 제목의 정렬이 맞지 않는 것입니다.

#### top padding으로 해결하기

이를 해결하기 위해 이미지에 필요한 top padding 계산해서 넣어주면 해결됩니다.

```swift
var body: some View {
  HStack(alignment: .top) {
    Image(systemName: "circle.fill")
      .resizable()
      .frame(width: 8, height: 8)
      .padding(.top, 6)  // 정렬을 맞추기 위한 top padding

      Text(title)
  }
}
```

{{< figure src="good.png" width=250 caption="top padding을 줘서 정렬된 목록 아이템" >}}

이렇게 하면 정렬이 잘 되지만 이미지나 텍스트의 크기가 변할 경우, 패딩 또한 다시 계산해서 바꿔줘야합니다.

{{< figure src="wrong2.png" width=250 caption="top padding을 줬지만 아이콘 크기가 바뀌어 정렬이 맞지 않는 목록 아이템" >}}

#### alignmentGuide로 해결하기

alignmentGuide를 사용하면 이런 상황을 깔끔하게 해결할 수 있습니다.

```swift
var body: some View {
  HStack(alignment: .top) {
    Image(systemName: "circle.fill")
      .resizable()
      .frame(width: 8, height: 8)
      .alignmentGuide(VerticalAlignment.top) { viewDimensions in // 정렬을 맞추기 위한 alignmentGuide
        viewDimensions.height / 2
      }

    Text(title)
      .alignmentGuide(VerticalAlignment.top) { viewDimensions in // 정렬을 맞추기 위한 alignmentGuide
        viewDimensions.height / 2
      }
  }
}
```

위 코드에서는 Image와 Text에 alignmentGuide를 주었습니다.

alignmentGuide의 첫 번째 인자인 VerticalAlignment.top은 말 그대로 VerticalAlignment.top 정렬에 대한 기준을 정하겠다는 것을 뜻합니다.

두 번째 인자에서 그 기준에 대한 offset을 계산하는데 여기서는 해당 뷰의 절반만큼을 offset으로 주었습니다.  
top에서 뷰 높이의 절반만큼 더하면 뷰의 중간이 되겠죠.  
따라서 우리가 원하는 중간 정렬이 됩니다. 이는 뷰의 크기가 변하더라도 그에 맞게 계산되어 앞의 문제를 해결합니다.

#### 더 나아가서

사실 중간정렬은 alignment를 top이 아닌, center로 주면 간단하게 구현가능합니다. 😅  
하지만 이해를 위해 위 예제를 만들어 보았습니다.

이해에 도움이 되었으면하는 바람이며, 이를 통해서 나만의 커스텀 정렬을 수행해보시길 바랍니다.  
예를들어, 아이콘과 여러줄의 Text의 첫 번째 줄을 가운데 정렬해보시는 것들도 alignmentGuide를 사용하면 구현할 수 있습니다.
