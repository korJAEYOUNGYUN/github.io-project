---
title: "StringFormat 순서 바꾸기"
date: 2021-12-30T01:23:29+09:00
description: "String format 순서"
draft: false
tags: [
  "iOS",
  "String format"
]
categories: [
  "iOS"
]
image: "brett-jordan-M3cxjDNiLlQ-unsplash.jpg"
---

String format을 사용할 때, 인자들의 순서들이 상황에 따라 다르게 적용되도록 하게 해야 할 때가 있습니다.
(다국어 처리 시 시간, 주소의 순서 등...)

이를 위해서 [Swift format](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html)에서는 인자들의 데이터 타입뿐만 아니라 순서 specifier도 제공합니다.
순서 specifier는 "n$" 형태로 사용되며 포맷 specifier와 결합하여 인자들의 순서를 조절할 수 있습니다.

{{< gist korJAEYOUNGYUN 35b009c648f1b4e33038e5e62dbd2939 >}}

위 코드는 String format에 순서를 적용하는 간단한 예시입니다.
