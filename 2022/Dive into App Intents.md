
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
개체의 인스턴스를 제공하기 위해 앱은 쿼리를 실행하고 intents의 결과로 entity를 되돌려준다.

- Suggested Entities
	- 앱이 제공한 제안된 개체들의 집합
- Search
	- 
Intent를 만들기 전에, Entity와 이에 상응하는 Query를 만들어야 한다.
### Entity 만들기
- Identifier
- Display Respresentation
- Entity Type Name

```swift
// 1
struct BookEntity: AppEntity, Identifiable {
	// 2
	var id: UUID
	// 3
	var displayRepresentation: DisplayRepresentation {
		DisplayRepresentation(title: "\(title)")
	}
	// 4
	static var typeDisplayName: LocalizedStringResources = "Book"
}
```

1. Entity를 만들기 위해, `AppEntity` 프로토콜을 채택한다
2. Identifier: `Identifiable` 프로토콜을 채택하고, id를 구현한다.
	1. App Intents는 이 id로 개체를 참고한다.
	2. stable, persistent해야 한다. (고객이 만든 단축어에 저장될 수 있기 때문)
3. `Display Representation` 를 구현한다
	1. 개체를 보여주는 데 사용됨
	2. subtitle과 image를 제공할 수도 있다.
4. `Type Display Name`
	1. 개체의 타입을 나타내는 문자열
5. Query 추가
6. 


### Queries
- Queries are an interface for retrieving Entities from your app
	- 앱 인터페이스를 시스템에 부여한다.
- Lookup entities 요구 사항
	- All Queries look up entities by ID (ID로 검색할 수 있다)
	- `StringQuery` looks up entities using a search string 
		- string은 검색을 지원한다.
	- `PropertyQuery` looks up entities based on other criteria
		- 더 유연한
	- Queries can provide **suggested entities** to pick from
		- 모든 쿼리가 제안된 개체를 제공할 수 있다.
		- 사용자에게 목록을 보여주고, 사용자는 선택할 수 있는.

- 시스템이 개체의 인스턴스를 찾을 수 있게, 모든 entity는 query와 연관되어야 함


```swift
// 1
struct BookQuery: EntityQuery {
	// 2
	func entities(for identifiers: [BookEntity.ID]) async throws -> [BookEntity] {
		identifiers.compactMap { identifiers in
			Database.shared.book(for: identifiers)
		}
	}
}

struct BookEntity: AppEntity. Identifiable {
	var id: UUID
	// ...
	// 3
	static var defaultQuery = BookQuery()
}
```

1. EntityQuery를 채택하는 Query 구조를 만든다.
2. 개체를 모두 찾는 기본적인 쿼리
3. 쿼리를 개체에 연결. `static var`

사용자가 단축어에서 책을 고르면 그 식별자는 단축어에 저장된다 
단축어가 실행되면 App Intents는 그 식별자를 쿼리에 전달해서 BookEntity instance를 회수한다.

```swift
struct OpenBook: AppIntent {
	//1
	@Parameter(titme: "Book")
	var book: BookEntity

	static var title: LocalizedStringResource = "Open Book"
	
	static var openAppWhenRun = true
	
	@MainActor
	func perform() async throws -> some IntentResult {
		Navigator.shared.openBook(book)
		return .result()
	}

	static var parameterSummary: some ParameterSummary {
		Summary("Open \(\.$book)")
	}
}
```

1. AppEntity 프로토콜을 따르는 BookEntity는 AppIntent의 paramater로 쓸 수 있다.

책 피커를 구성하기 위해 쿼리는 `Suggested Entities`도 제공해야 한다.

#### Suggested Entities
```swift
struct BookQuery: EntityQuery {
	func entities(for identifiers: [BookEntity.ID]) async throws -> [BookEntity] {
		identifiers.compactMap { identifier in
			Database.shared.book(for: identifier)
		}
	}

	// 1
	func suggetsedEntities() async throws -> [BookEntity] {
		Database.shared.books
	}
}
```
1. library에 추가된 모든 책을 반환하는 `suggestedEntities()` 구현
	1. 이 결과는 피커 리스트에 보여진다.

#### Search
search 바에서 검색을 하면 앱 내 로컬 데이터 베이스에서 직접 쿼리를 돌려야 한다. 이는 StringQuery API로 가능

```swift
// 1
struct BookQuery: EntityStringQuery {
	// ...
	
	func entities(matching string: String) async throws -> [BookEntity] {
		Database.shared.books.filter { book in
			book.title.localizedCaseInsensitiveContains(string)
		}
	}
}
```

1. `EntityStringQuery` sub protocol을 채택하면, `entities(matching string:)` method 얻음
	1. 문자열이 주어질 때, 결과를 되돌려줌
	2. 대소문자 미구분
	3. 저자, 시리즈 검색

책이 많다면, favorite books를 suggested에, 나머지 책들은 검색으로 하는 것도 하나의 용례.

앱에서 책을 여는 방법을 노출하기 위해 책 개체와 책 쿼리를 구현했다. 
동일한 개체와 쿼리를 사용해서 다른 intent를 만들 수도 있다.
library에 책을 추가하는 intent를 구축하자.
앱 직접 실행 없이 모델을 직접 수정하는 intent를 구축함으로써 사용자들에게 권한을 줄 수 있음(siri나 다른 앱 실행중에 수정 가능)

```swift
struct AddBook: AppIntent {
	static var title: LocalizedStringResources = "Add Book"
	
	// 1
	@Parameter(title: "Title")
	var title: String
	
	// 1
	@Parameter(title: "Author Name")
	var authorName: String?
	
	// 2
	@Parameter(title: "Recommemded By")
	var recommendedBy: String?

	// 3
	// 5
	func perform() async throws -> some IntentResult & ReturnsValue<Book> {
		guard var book = await BooksAPI.shared.findBooks(named: title, author: authorName).first else {
			throw Error.notFound
		}
		book.recommendedBy = recommendedBy
		Database.shared.add(book: book)
		return .result(value: book)
	}

	// 4
	enum Error: Swift.Error, CustomLocalizedStringResourceConvertible {
		case notFound
		// ...

		var localizedStringResource: LocalizedStringResource {
			switch self {
				case .notFound: return "Book Not Found"
				// ...
			}
		}
	}
}
```
1. `title`, `authorName` 은 parameter
2. optional note인 `recommendedBy`. 어떤 친구가 추천했는지 기록.
3. async await API 호출로 책을 찾아봄
4. error를 로컬화하기 위해 오류 타입을 구현한다.
	1. `CustomLocalizedStringResourceConvertible` : 로컬화된 문자열 키를 return, 문자열 파일에 추가하면 현지화됨.

add book intent가 다른 intent와 결합할 수 있다면 훨씬 더 유연해질 것이다. 이를 위해 API에서 intent의 결과를 다른 intent로 전달하는 도구, Result를 사용할 수 있다.
5. `perform()` method는 book을 return한다. 함수 정의부에서 리턴타입은 새로운 프로토콜 `ReturnsValue<${TYPE}>`을 채택한다.

이제, add book intent는 book을 parameter로 받는 다른 intent에 선행하여 실행될 수 있음.

`openIntent` [16:30~]