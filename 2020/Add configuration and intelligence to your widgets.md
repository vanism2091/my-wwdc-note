
> [영상](https://developer.apple.com/videos/play/wwdc2020/10194)
> Widgets are at their best when they show up on someone's Home screen or in the Today View at the right time and provide actionable, relevant information. We'll show you how to build configurable widgets to let people create a personalized Home screen experience, and that take advantage of system intelligence to help people get what they want, when they want it. Learn how to customize your widget's configuration interface, and how to appear within Siri Suggestions or at the top of a widget Smart Stack based on user behavior or by letting the system know when there's new, timely information. For more on widgets, watch "Meet WidgetKit". And for more on system intelligence, watch the collection "Design for Intelligence."

# Introduction

## abstract
- 위젯을 구성 가능하게 만드는 방법
- 구성 가능한 위젯이 시스템의 intelligent에 도움을 주는 방법

## Widgets
iOS 14 업데이트 이후로 위젯은 많은 곳에 존재할 수 있다.
- Pages of home screen
- Stacks
- Today view
- macOS Notification Center

## WidgetKit
Widget을 구동하는 것은 WidgetKit이다.

카드 앱의 예. 카드 앱에서 하는 일
- 잔고 확인
- 최근 거래 확인

앱 전반에서 예화되는 다음 두 위젯
- Recent purchases: 카드 중 하나의 구매 목록을 표시
- Due date: 다음 지불 기한과 지불 금액을 표시 
	- systemSmall
	- 한 번에 하나의 카드 내용을 볼 수 있다. → configuration으로 설정
	- configuration 추가 하면 하나의 정의로 여러 카드의 위젯을 생성할 수 있다.

# Contents
- **The basics** : 위젯에 구성을 추가하는 방법에 대한 기본 사항
- **Types of data entry** : 
	- 사용자에게 입력을 요청할 수 있는 데이터 타입
	- 앱의 데이터로 인터페이스를 채우는 방법
- **Configuration experience** : 위젯 뒷면에 제목, 설명, 색상을 커스텀하는 방법  
- **System intelligence** : 시스템이 위젯을 스택에 표시하는 시각을 더 잘 예측할 수 있는 방법


# The Basics
## Widget Extension
시스템의 첫 번째 부분은 `Widget Extension`이다.
Widget extension contain 
- **SwiftUI code** that renders the visual appearance of the widget.
- some **metadata** about the widget itself

> Meet WidgetKit [[Meet WidgetKit]] [video](https://developer.apple.com/videos/play/wwdc2020/10028/)
> WidgetKit과 SwiftUI로 widget extension을 설정하는 방법

### widget configuration view
![위젯 설정 뷰](https://private-user-images.githubusercontent.com/107124308/337868840-84461c44-01fa-42dc-b859-cf75d67b0d14.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4NDM4MDksIm5iZiI6MTcxNzg0MzUwOSwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODY4ODQwLTg0NDYxYzQ0LTAxZmEtNDJkYy1iODU5LWNmNzVkNjdiMGQxNC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQxMDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0yZjdlYzlmZjJkYjk5ZDBmMGI2YzdhZjVmZGQwOWZmMWJlODBmNzY2MzJmMWFmM2I0ZGY2YzgwOTdjNzMzNzFhJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.RzFBHRWhYYg5ozNZ6gUdgsMOH7lwCizyHsyO1i4Xl8I)

개발자는 Parameter를 정의하고, 해당 parameter를 선택하는 화면은 시스템이 렌더링한다.

### Intents

parameter를 정의하려면, `Intent`를 사용해야 한다. - siri와 shortcut을 추가하는 데 사용하는.
Intent에는 변수의 ordered list가 있어서, 시스템은 `Parameter` 순서대로 구성 UI의 행으로 표시한다.
### Intent definition
Intent Definition File
![Intent Definition File](https://private-user-images.githubusercontent.com/107124308/337869451-0b8a5fd8-fed7-4731-91d2-b5a008da3aff.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4NDQ1OTAsIm5iZiI6MTcxNzg0NDI5MCwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODY5NDUxLTBiOGE1ZmQ4LWZlZDctNDczMS05MWQyLWI1YTAwOGRhM2FmZi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQxMDU4MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT03OGZhNDc1Yjc4ZGNhOThiZDk1YmEwNmNlMjQ3MjU5YzExNTE4YmI4NTNiMzIxY2M3MmMxODc3Yjk3MzAxMGIzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.p5T_sbiJIEb7xLHWRiupA3KVjY7QQLFmNLLwFPMqfug)

- 모든 Intent, Parameter, 해당 Intent가 지원하는 시스템 부분.
- 이 파일을 컴파일 하면 시스템은 인텐트에 대한 정보를 읽을 수 있음
- Xcode는 정의된 Intent 별로 각 변수에 대한 속성을 포함하는 `INIntent` class 생성한다.

> 나중에 [App Intents](https://developer.apple.com/documentation/appintents)(iOS 16+)가 나오면 별도의 정의 파일 없이 코드 베이스로 가능하다. 그러나 구버전 호환성을 위해 지금의 내용을 잘 알아둬야.

![인텐트와 인텐트 클래스](https://private-user-images.githubusercontent.com/107124308/337869469-4d3b5169-fc6f-4805-b0f8-270f5be2c958.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4NDQ1OTAsIm5iZiI6MTcxNzg0NDI5MCwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODY5NDY5LTRkM2I1MTY5LWZjNmYtNDgwNS1iMGY4LTI3MGY1YmUyYzk1OC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQxMDU4MTBaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1lZjA2MzM3ZjU3NTQ4OGNjNzRkNTQ0Y2MxZWMwZWQ0NjZmNjEyMjM0NDRmNWQ4NGQzNGQzMGU3YWVjMGUyZGNjJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.fKewmFcQyO6_VPKWgkozrnMuMNh6c5No5EzE60HR73o)


```Swift
class **ViewRecentPurchasesIntent**: INIntent {
	var card: Card?
	var category: Category?
}

func snapshot(
	for intent: **ViewRecentPurchasesIntent**,
	with context: Context,
	completion: @escaping (CardRecentPurchasesEntry) -> ()) {

	let card = intent.card
	let category = intent.category

	let entry = CardRecentPurchasesEntry(...)
	completion(entry)
}

```

- Intent class의 인스턴스는 실행 시간에 widget extension에 전달된다.
- 이는 시스템이 위젯 구성 화면을 그리는데 활용된다.

## 정리
Intent Definition File로 Intent를 정의하면, intent의 parameter는 위젯 구성 UI에서 구성할 수 있는 요소가 된다.

# Types of data entry [4:25~]
인텐트에 추가할 수 있는 매개변수 타입

| type                    | view                     |
| ----------------------- | ------------------------ |
| String                  | text Field               |
| Boolean                 | switch                   |
| Integer                 | number fields / steppers |
| Decimal                 | number fields / sliders  |
| contact, location, etc. | Pickers                  |
| Enumeration             | list                     |
- 이 외에도 `Date component`, `Duration`, `URL`, `Measurement`, `Currency amount`, `Payment method` 등을 지원한다.
- parameter는 n개의 값을 가질 수 있다. (n개의 캘린더의 일정을 보여주는 위젯)
- iOS14+, Intents는 fixed-size array를 지원한다.
	- 위젯의 크기에 따라 항목 수 지정 가능

## Adding Configuration to a Widget [6:00~]
1. identifying what you want to make configurable
	- 무엇을 parameter로 정의할 것인가?
	- e.g. 카드, 카테고리

live coding[6:51~]
1. StaticConfiguration → IntentConfiguration
	1. Intent 정의하기 by Intent Definition File
		1. ✅ Intent is eligible for widgets
		2. Intent 카테고리는 View.
		3. Parameter 추가하기
			1. 매개변수 이름 지정하기. 위젯 구성 UI에 표시됨.
			2. type 설정하기. 
				1. 커스텀 타입인 Card.
				2. 커스텀 enum, Category -> default value
			3. ✅ options are provided dynamically 
				- 로그인한 사람에 따라 다른 카드가 제공되기 때문.

## Dynamic Options and Search [9:42~]
dynamic option을 구현해야 한다.
동적 옵션 활성화하면, 시스템에게 알린다. 사용자가 선택할 값을 받아오기 위해 앱을 참고해야 한다고.
### 동적 옵션을 활성화하면 변경되는 2가지
1. 매개변수의 UI: 모달 옵션 목록을 여는 버튼
2. 구현해야 하는 메소드 2개가 생성된다.
	- `IntentHandler` protocol의 method임
```swift
// Provide possible options for card parameter
func provideCardOptionsCollection(
	for intent: ViewRecentPurchasesIntent,
	with completion: (INObjectCollection<IntentCard>?, Error?) -> Void)
{
	let cards = CardList.shared.cards.map { card in
		IntentCard(card: card)	
	}
	let collection = INObjectCollection(items: cards)
	completion(collection, nil)
}

// Provide default value for card parameter
func defaultCard(for intent: ViewRecentPurchasesIntent) → IntentCard? {
	guard let primaryCard = CardList.shared.primaryCard
		else { return nil }
	return IntentCard(card: primaryCard)
}

extension IntentHandler: ViewRecentPurchasesIntentHandling {
	// .... 위에 정의한 두 함수는 이 안에 들어감.
}
```

### Intent handler
- Protocol, generated by Xcode
- Implement in app, or Intents extension
	- 앱이나 intents extension에서 이 프로토콜을 준수하는 클래스를 만들어야 함.
- Provides data to the system while the user is configuring

### Sectioned content
content를 단순한 1차원 리스트 뿐만 아니라 섹션별로 제공할 수도 있다.
- 섹션 ![섹션](https://private-user-images.githubusercontent.com/107124308/337872784-e82e4aea-9cba-4bc4-9f04-979157f20cae.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4NDg2MTksIm5iZiI6MTcxNzg0ODMxOSwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODcyNzg0LWU4MmU0YWVhLTljYmEtNGJjNC05ZjA0LTk3OTE1N2YyMGNhZS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQxMjA1MTlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT05MjliOTY2ZDM3ZWUyMmQwOGM5ZTM2OWU1ZTQ2MjIyODcwMjNjYWZlYjVkYWJlZTI3YjdjNzJlMWJjZTI5ODA3JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.fG-KHZQbm8xN8KpNnC_0h_BUj263fOK8AoOoOAiEc-g)
- UI에서는 이렇게 보임 ![UI](https://private-user-images.githubusercontent.com/107124308/337872803-b79ea7a9-f023-4ce5-b86c-79f049013e3e.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4NDg2MTksIm5iZiI6MTcxNzg0ODMxOSwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODcyODAzLWI3OWVhN2E5LWYwMjMtNGNlNS1iODZjLTc5ZjA0OTAxM2UzZS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQxMjA1MTlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1lOGYzOTVlMzE4MjE1MzRjNmZmMTY2MWNiNmU2MTJhZDA4YTdhYjg3YzMxMDY0OTIwMDgxYWViZmE2YTliMDM1JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.xAuZm1GUMPJ8xoEf-fAFfwk8YhpFNFUP0ZpJDLPXKug)

사용자가 입력하는 대로 검색 결과를 제공할 수 있다. Intent Definition file에서, [12:00~]
- ✅ Intent handler provides search results as the user types
- Prompt Label 정의하기
- `provideCardOptionsCollection` 구현하기
	- type을 하면 업데이트된 `searchTerm`을 사용해 method가 다시 호출된다.
```swift
func provideCardOptionsCollection(
	for intent: ViewRecentPurchasesIntent,
	searchTerm: String?
	with completion: (INObjectCollection<IntentCard>?, Error?) -> Void)
```


### quick demo
1. ViewRecentPurchases intent의 intent handler 설정하기
	1. Intent Extension 안에 IntentHandler 구현
	2. ViewRecentPurchases가 intent handler protocol 준수하게끔.
		1. provideCardOptionsCollection
		2. default
			- default 값은 위젯을 처음 추가할 때 값. 가능하다면 의미있는 값을 하는 게 중요하다.
2. IntentConfiguration 채택
	- Intent 생성, Intent handler를 구현했기에 가능.
	1. Widget body 내부 구현 IntentConfiguration으로 변경
	2. TimelineProvider → IntentTimelineProvider
		- method 매개변수로 intent가 추가됨.
	3.  

```swift
struct Provider: IntentTimelineProvider {
	...
	func timeline(for intent: ViewREcentPurchasesIntent, ...) {
		let card = intent.card
		let category = intent.category
		...
	}
}

@main
struct CardRecentePurchasesWidget: Widget {
	private let kind: String = "CardRecentePurchasesWidget"
	
	public var body: some WidgetConfiguration {
		IntentConfiguration(kind: kind, intent: ViewRecentPurchasesIntent) ...
	}
}
```


# Configuration experience [15:14~]
위젯 configuration UI의 모양을 사용자 정의할 수 있는 몇 가지 방법
## title and description
코드로.

## background, accent color
1. widget extension의 asset catalog에 color 추가.
2. 프로젝트 세팅 > Build Setting > target:Extension > Asset Catalog Compiler - Options
	1. Global Accent Color Name : 
	2. Widget Background Color Name :

## parameter visibility[16:00~]
다른 parameter 값에 따라 특정 parameter의 가시성 조정
- Intent Definition file에서 parameter간에 관계를 지정함
	1. param A가 param B에 의존한다면, B는 A의 부모 parameter이다.
		1. B를 A 위에 두고, A의 부모 parameter를 B로 설정한다.
	2. Show If parent ~ 정의


# System Intelligence [17:00~]
iOS14에서 smart stack을 제공한다.
시스템이 위젯을 자동으로, 시기 적절하고 유용한 위젯을 최상단으로 배치한다. 

## How do stacks behave intelligently?
좋은 스마트 스택을 만드는 요소
Provide timely, glanceble information with obvious value to the user

시스템이 위젯을 보여주는 기준
1. user behavior-based
	- 사용자가 특정 시간에 일반적으로 찾는 정보를 제공하는 위젯을 표시한다.
2. relevant information from your app
	- 앱은 시스템에 관련성 높은 업뎃이 있음을 알릴 수 있음
	- 그러면 시스템은 위젯을 스택 상단에 표시하는 것을 고려함

## How can I appear at just the right time? [19:10~]
내 앱도 스마트 스택에서 잘 보이게 하려면 나는 뭘 구현해야 할까?
### user behavior
iOS 12에서, Shortcut, custom intent donation을 도입했음. 시스템이 앱에서 유저가 뭐하는지 알 수 있는 방법 제공.
이 정보를 활용해서 시스템은 spotlight에서 유저가 하려는 작업에 대한 예측을 제공함.
- 유저가 뭘 했는지를 spotlight에서 보여준다.

#### Leveraging user behavior
- These donations can now help surface your widget to the top of the stack on the home screen
	- Introduction to Siri shortcuts WWDC18
	- Design for Intelligence: Make Friends with `the system` WWDC20

we have to set this same intent for donation by our host app
- 사용자가 앱에서 특정 신용카드를 확인할 때 이 인텐트를 기부해서 시스템에 알린다.
#### 설정 방법 - 개발자의 몫
1. Intent Definition File에서, 
	- Custom Intent > Suggestions - ✅ Intent is eligible for Siri Suggestions
	- Suggestions > Supported Combinations 추가
		- 특정 card 정보를 도네이팅 하고 싶으므로 card만 추가한다.
	  
```swift
// Donate INIntent on view
.onAppear {
	let intent = ViewRecentPurchasesIntent()
	intent.card = Card(identifier: self.card.id.uuidString, display: self.card.name)

	//
	let interaction = INInteraction(intent: intent, response: nil)
	interaction.donate { error in
		if let error {
			print(error.localizedDescription)
		}
	}
}
```
2. View 에서 최근 구매를 볼 때마다 호스트 앱이 이를 기부하도록 해야 한다.
	- INInteraction 객체를 생성
	- 기부 method를 호출해서 intent 전달.
	- intent에는 category param도 있지만, 지원 조합은 카드만 있기 때문에 시스템은 카드만 고려한다.

#### 시스템이 내부적으로 donation을 처리하는 방법 [21:40~]
- host app이 donating한 정보를 바탕으로, 시스템이 예측한다. 예를 들어, 카드 앱에서 월요일과 화요일 정오즈음에 `AcmeCard`를 사용했다는 정보를 바탕으로 시스템은 정오에 `AcmeCard` 정보를 보여주는 위젯을 스마트 스택에 띄운다.
- 그런데 `Supported Combination`에 카테고리도 추가한다면, 카드명과 카테고리가 동일한 위젯만 display의 대상이 된다.

> Supported Combinations is the way to communicate to the system which configurable parameter truly represents the information people are looking for
`Supported Combinations`는 사람들이 찾고 있는 정보를 실제로 나타내는 구성 가능한 변수를 시스템에게 알릴 수 있는 방법임
#### Leveraging user behavior 요약
- Set up configuration for your widget with an INIntent
	- 인텐트를 사용해 위젯을 구성함
- Mark it as eligible for Siri Suggestions
	- 그래야 시스템이 예측할 수 있음
- Configure Supported Combinations if you have optional parameters
	- 시스템이 예측할 매개변수만 구성하기
- Donate the intent in your host app
	- 앱에서, 유저가 볼 때마다 시스템에 donate하기

### Relevant information (app-side)[23:20~]
중요한 관련 정보가 있을 때 시스템에게 이를 알린다.

#### Quick recap
위젯킷을 사용해서 타임라인 항목을 제공할 수 있음.
타임라인은 날씨 앱처럼 시간에 따라 예측하는 위젯에도 잘 작동한다.
#### Recent purchases widget
타임라인은 실시간으로 새로운 정보에 반응해야 하는 경우에도 작동한다.

50$가 넘는 요금이 청구되면 알림을 받길 원한다고 가정하자.
위젯에 이러한 정보가 있음을 `TimelineEntry` + `TimelineEntryRelevance` object로 시스템에 알려줄 수 있음.
#### Adding relevance information to TimelineEntry
- TimelineEntry
	- Date
		- 항목이 렌더링 되어야 하는 시기
	- View
		- 렌더링되어야 하는 보기
	- Relevance: `TimelineEntryRelevance`
		- 관련성
- TimelineEntryRelevance
	- score
	- duration
#### code
```swift
// Providing relevance information in TimelineEntry

import WidgetKit

public struct TimelineEntryRelevance: Codable, Hashable {

	// A value that indicates the relevance of an entry compared to other entries in the past
	public let score: Float

	// The length of time following the entry's date that the widget has the relevance score set
	public let duration: TimeInterval

}
```
- score
	- 해당 항목이 과거의 모든 항목에 비해 얼마나 관련성이 있는지
	- 시스템은 상대적인 score만 고려. 범위와 척도는 개발자의 몫
	- 0 이하 == 관련 정보가 없으므로 표시되면 안된다고 시스템에 알림
- duration
	- 관련성 점수가 고정되는 기간
	- 특별히 그런 기간이 없다면, 0으로 남겨두자
		- 다음 Relevance가 수신될 때까지 관련성 점수가 지속된다.-

#### Basketball widget example
- nil이면?[26:22~]
	- 게임중에 TimelineEntryRelevance를 nil로 설정하면, 관련성에 영향을 주지 않고 TimelineEntry 업데이트를 수행한다.
	- 관련성을 위해 이 업데이틀르 무시하도록 시스템에게 지시한다.. ??/ 먼소리임??
	- 이전 관련성을 계승한다는건가?? 모든 다른 관련성에 대해 우선한다는건가??

### wrap-up
스마트 스택으로 스택 위에 위젯을 표시할 수 있다.
방법은 아래와 같다.
- Donate INIntents that match your configuration intent
- Provide TimelineEntryRelevance for important information
