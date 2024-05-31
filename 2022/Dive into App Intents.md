
# Dive into App Intents
https://developer.apple.com/videos/play/wwdc2022/10032
Learn how you can make your app more discoverable and increase app engagement when you use the App Intents framework. We'll take you through the powerful capabilities of this Swift framework, explore the differences between App Intents and SiriKit Intents, and show you how you can expose your app's functionality to the system. We'll also share how you can build entities and queries to create rich App Shortcuts experiences. To learn more about App Intents, watch "Implement App Shortcuts with App Intents" and "Design App Shortcuts" from WWDC22.

## Contents
- Introducing App Intents
- Intents and parameters
- Entities, queries, and results
- Properties, finding, and filtering
- user interactions
- Architecture and lifestyle

## Introducing App Intents

### App Intents의 세 핵심 요소
1. Intents
	- 앱에 내장된 동작들
	- 시스템 전체에서 사용될 수 있음
1. Entity
	- 앱의 개념을 나타내기 위해 intents가 사용하는 자료 구조
2. App Shortcuts
	- 자동화, 쉽게 찾을 수 있게 intent를 wrapping한 것

### Intent로 편의성을 제공하는 방법
1. Siri, shortcut
2. Spotlight
3. Focus Filters
	1. 사용 예, 특정 일 중에만 특정 캘린더 일정을 보여주고 싶은 경우
	2. https://developer.apple.com/videos/play/wwdc2022/10121
단축어를 활용하는 것은 짱짱 좋음 왜냐하면 짱 좋기 때문임. - 자동화.. 다른 숏컷을 지원하는 앱과 결합/통합될 수 있음
https://developer.apple.com/videos/play/wwdc2021/10283

유지 보수 편리 - swift code as source of truth. 별도의 편집기나 정의 파일의 필요성을 방지함.

이제부터 API를 살펴보자~

## Intents and parameters
### App Intents
- Single piece of app functionality
	- 시스템에 노출하는 앱의 분리된 단일한 기능 단위
	- e.g.
		- 새로운 달력 이벤트 만들기
		- 특정 화면 열기
		- 주문하기
- Performed manually or automatically
	- 사용자의 요청으로 실행되거나
		- 단축어를 누른다
		- Siri에게 부탁한다
	- 자동으로 실행된다
		- Focus filter
		- Shorcuts 자동화 사용
- Return a result or an error

### App Intents include
세 개의 핵심 요소가 포함된다
- Metadata
	- intent에 관한 정보, (로컬화된 제목)
- Parameters
	- intent가 실행될 때 사용할 수 있는 input
- Perform method
	- intent가 실행될 때 작업

Library app의 예.
기능은 아래와 같다.
읽었던 책, 읽고 싶은 책, 현재 읽는 책 추적하기
각각 별도의 탭에 책장으로 나뉘어져 있음

App Intent로 Currently Reading에 더 빨리 접근하게 하고 싶다..!

```swift
// 1
struct OpenCurrentlyReading: AppIntent {
	//3 
	static var title: LocalizedStringResource = "Open Currently Reading"
	
	// 2
	@MainActor
	func perform() async throws -> some IntentResult {
		Navigator.shared.openShelf(.currentlyReading)
		return .result()
	}
}
```
1. App Intent 프로토콜을 conform하는 structure를 정의한다.
2. Navigator는 메인 스레드에서 동작할 것이므로 `@MainActor`를 붙인다.
3. title이 필요하다. 문자열 파일에 키를 추가하면 자동으로 로컬화 된다.

Intent를 추가하면, 다음과 같은 곳에 다 사용될 수도 있다. (사용자가 shortcut에 추가한 후)
Menu Bar, Share Extensions, Terminal, AppleScript, Home Screen, Suggestions, Lock Screen, Shortcuts Widgets, Quick Actions, Voice, Apple Watch, HomePod, Automations, Shortcuts App, Keyboard, Spotlight

### App Shortcuts에 대한 지원 추가하기
https://developer.apple.com/videos/play/wwdc2022/10170

```swift
struct LibraryAppShortcuts: AppShortcutsProvider {
    static var appShortcuts: [AppShortcut] {
        AppShortcut(
            intent: OpenCurrentlyReading(),
            phrases: ["Open Currently Reading in \(.applicationName)"],
            systemImageName: "books.vertical.fill"
        )
    }
}
```
위의 코드를 작성하면, Spotlight와 Shortcuts 앱에서 자동으로 나타난다.

Parameter를 추가해서, input에 따라 적합한 책장을 열도록 intent를 일반화해보자.

### + Parameter

#### AppEnum 정의하기
기존의 Shelf enum에 AppEnum protocol을 확장한다.
```swift
// 2
enum Shelf: String {
    case currentlyReading
    case wantToRead
    case read
}

// 1
extension Shelf: AppEnum {
    static var typeDisplayRepresentation: TypeDisplayRepresentation = "Shelf"

	// 3
    static var caseDisplayRepresentations: [Shelf: DisplayRepresentation] = [
        .currentlyReading: "Currently Reading",
        .wantToRead: "Want to Read",
        .read: "Read",
    ]
}
```

1. intent 파라미터는 AppEnum을 채택해야 한다.
2. AppEnum은 String Raw value가 필요하다.
3. 각 case에 대한 로컬화 가능한 제목 
	- built time에 컴파일러가 읽으므로 dictionary literal로 제공해야 함.
4. enum 전체에 대한 사용자가 볼 수 있는 로컬화 가능한 이름

#### AppIntent + parameter 
```swift
struct OpenShelf: AppIntent {
    static var title: LocalizedStringResource = "Open Shelf"

	// 1
    @Parameter(title: "Shelf")
    var shelf: Shelf

    @MainActor
    func perform() async throws -> some IntentResult {
        Navigator.shared.openShelf(shelf)
        return .result()
    }

	// 2
    static var parameterSummary: some ParameterSummary {
        Summary("Open \(\.$shelf)")
    }

	// 3
    static var openAppWhenRun: Bool = true
}
```

1. Parameter는 `@Parameter(title: 파라미터 정보)` 프로퍼티 래퍼로 선언.
	- Parameter가 지원하는 타입들
	- Decimal, Integer, Boolean, Date, Person, File, ... AppEnum, Entities, ...
2. 만드는 모든 Intent에 대해 `parameterSummary`를 제공해야 한다. 
	- `ParameterSummary API`를 활용하면 숏컷의 뷰에 더 적합하게 UI를 커스텀할 수 있다.
	- 폴드 아래에 어떤 파라미터 인풋이 보이고 숨어 있을 지 정의할 수 있음.
	- when, otherwise, switch, case, default API
3. `openAppWhenRun` 설정.
	- intent가 실행될 때 앱을 열어야 한다면 true로
	- 기본값은 false

정적인 shelf가 아니라, 동적인 book을 열고 싶다면? 이를 위해 Entity가 필요하다.

## Entities, queries, and results [09:55~]



