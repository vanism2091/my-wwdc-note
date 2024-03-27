# Demystify SwiftUI

> 🔗 [https://developer.apple.com/videos/play/wwdc2021/10022/](https://developer.apple.com/videos/play/wwdc2021/10022/)
> Peek behind the curtain into the core tenets of SwiftUI philosophy: Identity, Lifetime, and Dependencies. Find out about common patterns, learn the principles that drive the framework, and discover how you can use them to guarantee correctness and performance for your app.

이 wwdc 영상은 하나의 흥미로운 질문으로 시작한다.

**When SwiftUI looks at your code, what does it see?**

SwiftUI는 내가 짠 코드를 보고 어떻게 뷰를 만들고 관리하는 걸까?
SwiftUI는 아래의 개념을 바탕으로 어떤 뷰가, 어떻게, 언제 변경될지 결정한다.

1. **Identity**: how SwiftUI recognizes elements as the same or distinct across multiple updates of your app.
2. **Lifetime**: how SwiftUI trakcs the existence of views and data over time.
3. **Dependencies**: how SwiftUI understands when your interface needs to be updated and why.

## Identity
view에 있어서 동일한 view인지 아닌지 여부는 중요하다. 이에 따라 사용자가 경험하는 상호작용이 달라지기 때문이다.
![image](https://private-user-images.githubusercontent.com/107124308/310041808-259dcaad-eeb4-4d3f-9d74-ce684dc37f57.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDk2MjgyMDEsIm5iZiI6MTcwOTYyNzkwMSwicGF0aCI6Ii8xMDcxMjQzMDgvMzEwMDQxODA4LTI1OWRjYWFkLWVlYjQtNGQzZi05ZDc0LWNlNjg0ZGMzN2Y1Ny5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzA1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMwNVQwODM4MjFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zMzQxNjE2YzhiYWQ1ZDJkNDRkMmNkMDYxY2Y0NDkwNTlmYWQ1OTQwOGMwOTc4Y2Q4YjRmZGNiMGJlNDg3YTllJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.eiTP1pBG0bN0dhfaYiep6HP-tqyiu335WYp7Bw0-o40)
가운데 있는 동그란 뷰의 identity가 동일하다면(동일한 뷰라면), 다음 화면으로 바뀔 때 애니메이션이 적용될 것이고 서로 다른 뷰라면 fade in and out이 적용될 것이다.

SwiftUI는 view간의 identity를 바탕으로 어떤 뷰를 변경할지, 그리고 transition이 어떻게 발생할지 결정한다.

그렇다면 우리는 특정 view에 고유한 identity를 어떻게 코드로 부여할 수 있을까? 이에 대한 답을 찾기 위해 identity의 종류에 대해 살펴보자.

> how identity is represented in your code, focusing on the two different types of identity used by SwiftUI
> 1. **explicit identity**: using custom or data-driven identifiers
> 2. **structural identity**: distinguishing views by their type and position in the view hierarchy

Identity의 종류는 크게 두 가지가 있다.
### Explicit Identity
우리는 일상적으로 이름 혹은 닉네임 등으로 명시적으로 identity를 부여하곤 한다. 

이러한 방법은 강력하고 유연하지만, 언제 어디서든 모든 ID을 관리하고 추적해야 한다.

UIKit 혹은 AppKit에서는 이러한 ID로 pointer identity를 활용한다. SwiftUI에서는 pointer identity를 사용하지 않지만, pointer identity에 대해 아는 것은 SwiftUI는 어떻게 사용하지 않고 왜 그러지 않는지 이해하는데 도움이 된다.

#### Pointer Identity

![Pointer Identity](https://private-user-images.githubusercontent.com/107124308/310050363-f05a1726-dadf-4887-ac6b-656112ba0174.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDk2Mjk4MDUsIm5iZiI6MTcwOTYyOTUwNSwicGF0aCI6Ii8xMDcxMjQzMDgvMzEwMDUwMzYzLWYwNWExNzI2LWRhZGYtNDg4Ny1hYzZiLTY1NjExMmJhMDE3NC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzA1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMwNVQwOTA1MDVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zZWYyOGQ2ZDc4ZmNjNjA2OWE2YzMxNWY1NDIzMzA4MWYzM2ZhNzFjZjMxZjliMmFmMDFhMDdiOWRhNGVlYTJhJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.U5Yr9A583qliUdxBRHkncI4mMz31OIFejOoJTgGBVlo)

UIKit이나 AppKit의 View 계층을 위의 사진처럼 단순화해보자. 이 경우 메모리의 어디에 할당되었는지를 의미하는 메모리 주소가 고유한 포인터이며, 뷰의 explicit identity가 될 수 있다.

그런데 SwiftUI view는 value type이다. 그렇기 때문에 reference type인 class와 달리 pointer 개념을 사용하지 않음.

> 왜 SwiftUI에서 reference type이 아니라 value type을 사용하기로 결정했는지 궁금하다면 [WWDC 2019 SwiftUI essentials](https://developer.apple.com/videos/play/wwdc2019/216/)을 참고하자.

값 타입 그 자체에는 SwiftUI가 뷰를 관리할 때 identity로 사용할만한 정식적인 reference가 없다. (참조 타입인 class에는 포인터가 있지만..)
대신 SwiftUI는 다른 형태의 explicit identity를 사용한다.

#### Explicit Identity 예시 1

![SwiftUI code에서 explicit identity](https://private-user-images.githubusercontent.com/107124308/310053061-79b93927-95c6-4e33-bcab-5ea9e0499c6b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDk2MzAzMzUsIm5iZiI6MTcwOTYzMDAzNSwicGF0aCI6Ii8xMDcxMjQzMDgvMzEwMDUzMDYxLTc5YjkzOTI3LTk1YzYtNGUzMy1iY2FiLTVlYTllMDQ5OWM2Yi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzA1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMwNVQwOTEzNTVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT05ZTBkYjJjN2EwZDlmZDQ2ODA4NGZiNTE2ZWFhNDkwYWM5OTAyZTdmYjlhMTZlYWJkNmVlY2JmZDM4NjRmMDc0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.hPOjW0gvR1J2pz55ulUp69AnMB1GgABygAgg7E29mt4)

SwiftUI 코드를 작성하며 id parameter에 위와 같은 코드를 작성한 경우가 왕왕 있었을 것이다.
만약 dogs 컬렉션이 변경되면, SwiftUI는 id parameter로 넘긴 `dogTagID`를 바탕으로 어떤 뷰가 변경되어야 하는지 하는지 결정하고 이에 적절한 애니메이션을 생성할 것이다.

#### Explicit Identity 예시 2
![SwiftUI-Explicit-ID-eg2](https://private-user-images.githubusercontent.com/107124308/310055329-1d5a2408-88b8-4887-a2fe-32f80b1264c0.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MDk2MzA3MTEsIm5iZiI6MTcwOTYzMDQxMSwicGF0aCI6Ii8xMDcxMjQzMDgvMzEwMDU1MzI5LTFkNWEyNDA4LTg4YjgtNDg4Ny1hMmZlLTMyZjgwYjEyNjRjMC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzA1JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMwNVQwOTIwMTFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT03MTgzNzY4N2ExNzBmZjViMDVmODdmOTlmMmE4YzA2OTg3ZGU4ZjZkZWIyYzI4YjE4ZjVmZDNhZmQ1NTNlMGNlJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.Kkgo3UTDov-falHF01FSdvVX7Ot-TePeB5efFD5uOVM)

explicit identity를 이용해 상단으로 scroll을 구현한다. 
(scroll 최상단에 위치한 HeaderView에 id modifier로 id를 부여한 후 proxy로 scroll)
필요한 view에만 exp id를 부여하면 된다. exp id가 없다고 id가 없다는 것은 아니다.

**Every view has an identity**

모든 뷰는 identity를 가지고 있다. 명시적인 identity가 없다면, 암시적인 identity를 갖고 있는데 이는 SwiftUI가 뷰에게 부여한 것이며 이를 structural identity라고 한다. 

### Structural Identity

> SwiftUI uses the structure of your view hierarchy to generate implicit identities for your views so you don't have to.
> ...
> We're using the relative arrangement of our subjects to distinguish them from each other -- that's structural identity.

View의 위치가 고정되어 있다면, 위치를 암시적인 id로 사용할 수 있겠다.

> SwiftUI leverages structural identity throughout its API, and a classic example is when you use if statements and other conditional logic within your View code.

SwiftUI에서는 API 전반에 걸쳐 구조적 동일성을 활용하며, 대표적인 예로 View 코드 내에서 if 문 및 기타 조건부 논리를 사용하는 경우를 들 수 있습니다.

#### Structural Identity의 예: If 문

> The structure of the conditional statement gives us a clear way to identify each view.
> 
> ...
> 
> However, this only works if SwiftUI can statically guarantee that these views stay where they are and never swap places. SwiftUI accomplishes this by lookig at the type structre of your view hierarchy. When SwiftUI looks at your views, it sees their generic types -- in this case, our if statement translated into a _ConditionalContent view, which is generic over its true and false content.

```swift
@ViewBuilder // implicit
var body: some View {
	if rescueDogs.isEmpty {
		AdoptionDirectory(selection: $rescueDogs)
	} else {
		DogList(rescueDogs)
	}
}

// generic type
some View = 
	_ConditionalContent<
		AdoptionDirectory,
		DogList
	>
```

if문의 두 분기는 각각 `AdoptionDirectory` 뷰와 `DogList` 뷰로 나뉜다. 전자는 반드시 조건문이 참일 때, 후자는 조건문이 거짓일 때 보여지는 뷰이다. 즉, 조건문 아래 뷰는 참/거짓일 때 실행된다는 점에서 언제 어떤 뷰가 실행될 지 명확하게 식별 가능하다.

SwiftUI가 봤을 때 특정 뷰가 제자리에 유지되고 절대 자리를 바꾸지 않도록 정적으로 보장할 수 있는 경우에 특정 뷰에 구조적 동일성이 작동한다.

그렇다면 SwiftUI는 이를 어떻게 볼까?

#### SwiftUI가 Structural Identity


SwiftUI는 코드에서 generic type을 살펴봄으로써 뷰 계층 구조의 유형 구조(type structure)를 확인한다.
Swift의 result builder인 ViewBuilder가 코드를 Generic type으로 변환한다.

View 프로토콜은 암시적으로 body 프로퍼티를 ViewBuilder로 감싸고, ViewBuilder는 body의 논리 문(if statement)에서 단일 제네릭 뷰를 구성한다.
body 프로퍼티의 반환 유형인 some View는 정적 복합 유형을 나타내는 자리 표시자로, 코드가 복잡해지지 않도록 숨겨져 있다.
이 제네릭 유형을 사용하면 SwiftUI에서 참일 때 뷰는 항상 AdoptionDirectory가 되고 거짓일 때 뷰는 항상 DogList가 되어 암시적이고 안정적인 ID를 배후에서 할당할 수 있다.

#### Good Dog, Bad Dog 예제에서
```swift
VStack {
	if dog.isGood {
		PawView(tint: .green)
		Spacer()
	} else {
		Spacer()
		PawView(tint: .red)
	}
}

// Recommended!
PawView(tint: dog.isGood ? .green : .red)
	.frame(
		maxHeight: .infinity,
		alignment: dog.isGood ? .top : .bottom
	)
```


위 코드에는 각 조건 분기마다 다른 보기를 정의하는 if 문이 있음
이렇게 하면 SwiftUI가 if 문의 각 분기별로 고유한 정체성을 가진 다른 뷰라고 이해하기 때문에 뷰가 안팎으로 전환


하나의 단일한 view에 modifier만 바꾸면, 계속 동일한 뷰로 인식되어서 원하던 부드러운 animation을 볼 수 있음.

이 두 가지 전략 모두 효과가 있을 수 있지만 SwiftUI에서는 일반적으로 두 번째 접근 방식을 권장함.
기본적으로 아이덴티티를 유지하고 보다 유동적인 전환을 제공하도록 함.
이는 또한 뷰의 수명과 상태를 보존하는 데 도움이 되며, 이에 대해서는 나중에 자세히 설명할 것.

이제 구조적 아이덴티티를 이해했으니 이제 그 사악한 천적인 애니뷰에 대해 이야기해보자.
#### AnyView[11:50]