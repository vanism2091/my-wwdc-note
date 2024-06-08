# Meet WidgetKit

> 🔗 [https://developer.apple.com/videos/play/wwdc2020/10028/](https://developer.apple.com/videos/play/wwdc2020/10028/)
> Meet WidgetKit: the best way to bring your app's most useful information directly to the home screen. We'll show you what makes a great widget and take a look at WidgetKit's features and functionality. Learn how to get started creating a widget, and find out how WidgetKit leverages the power of SwiftUI to provide a stateless experience. Discover how to harness your existing proactive technologies to make sure your widget surfaces relevant material. And create a Timeline that ensures your content is always fresh. For more on creating widgets, check out "Build SwiftUI views for widgets" and "The widgets code-along."

#widget

## What makes a great widget experience?
- Glanceable
- Relevant
- Personalized

### Glanceable 한 눈에 보기 좋아야 한다
위젯으로 상호작용하지 않는다. 단순히 정보 전달만 한다. 필요한 정보만 전달한다.
**widgets are not mini-apps.**
> 그러나 2023년에는 interactive 위젯이 두둥등장..! [[Bring widgets to life]] [video](https://developer.apple.com/videos/play/wwdc2023/10028/)
### Relevant. 관련성 있는.
적절한 위젯을 적절한 때에 - 스마트 스택
- Stacks use on-device intelligence
- Siri Shortcuts donation
- WidgetKit API

### Personalization 개인화
- 유저 경험에 적합한 가능한 한 많은 규격을 지원하는 것이 좋다.[3:57]
- 위젯 킷으로 위젯 옵션(Widget Configuration) 설정

**훌륭한 위젯 경험의 목표는 한 눈에 알아볼 수 있고 관련성이 높으며 개인화된 경험**

## 위젯킷
- 유저가 홈화면을 보는 횟수는 대략 하루 90회, 머무는 시간은 굉장히 짧다.[5:23]
- 위젯의 UI와 WidgetKit은 SwiftUI로 구축됨

![how widgetkit works](https://private-user-images.githubusercontent.com/107124308/333068178-f9bc912b-5143-4285-b2f4-2698df062819.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTY0NDY5MTYsIm5iZiI6MTcxNjQ0NjYxNiwicGF0aCI6Ii8xMDcxMjQzMDgvMzMzMDY4MTc4LWY5YmM5MTJiLTUxNDMtNDI4NS1iMmY0LTI2OThkZjA2MjgxOS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNTIzJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDUyM1QwNjQzMzZaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01MTUzMjkyMWQ0MTEyMTgwYWI5MTE3YzE4OTg2NmM3NzI1MTg4NzQ1Y2ExMDkzYjU1OGVmN2MzOWM0NWE4Mzc5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.FWc68i4qsu96FzAcJk5QSqt411kTb1pUzcEiI1C9rMs)

- Widget extension은 시스템에 `Array<View, Date>`인 타임라인을 제공한다. 시스템은 이를 디스크에 저장 후 적절한 시간에 보여준다. SwiftUI의 선언적 특성 덕분에, 앱 런처 프로세스 없이 위젯을 로드하고 이 뷰를 위젯 추가 화면 같은 시스템 화면에서도 재활용할 수 있다.

### **How Widgetkit works**
- Timeline allows views to be ready up front
- Can refresh from your main app
- Schedule updates from extension

위젯 확장 프로그램이 위젯의 타임라인을 렌더링한다.
앱에서 의존하는 데이터가 변경되면, 위젯 확장 프로그램이 재실행되고(앱이 API를 사용해서 타임라인을 로드함) 새로운 타임라인을 반환한다. 
그러므로 앱에서 변경사항이 있어도 위젯은 항상 최신 상태로 유지된다.


## 위젯 만드는 방법 [7:30]

좋은 위젯을 만들기 위한 4가지 방법
- Defining a widget
- Creating a glanceable experience
- Engine of the widget: Views, timelines, and reloads
- Personalization and intelligence
## Defining a widget
### Widget definition
- kind (종류)
- configuration (구성)
- supportedFamilies (지원되는 크기)
- placeholder (자리 표시자)

하나의 Widget Extension이 여러 종류의 위젯을 지원할 수 있는 매커니즘으로 설계했다.
WidgetKit extension은 SwiftUI, AppKit, 카탈리스트 기반 macOS 앱을 지원한다.

#### Kind
Kinds of widgets can also express which type of configuration they support.
지원하는 구성 유형 표현할 수 있다.
특정 위젯이 Static 혹은 Intent Configuration을 지원하는지 표현한다.

#### Configuration
- StaticConfiguration
	- 사용자 커스텀을 지원하지 않음
	- e.g. 피트니스 위젯
- IntentConfiguration
	- 사용자 개인화.
	- e.g. 리마인더 위젯 - 목록 선택

#### supportedFamilies
- systemSmall
- systemMedium
- systemLarge
- 기본적으로 widget은 모든 유형을 지원한다.
 하나의 kind는 하나 이상의 supportedFamilies를 지원한다.
#### Placeholder
- 각 위젯은 placeholder를 제공해야 한다.
- placeholder UI == **default content** of the widget. 위젯의 kind를 나타낸다.
- **No user data**
- Queried on environment chage
	- 희소하게 호출된다. 언제 발생하는지 보장할 수 없다.
	- 일반적으로 디바이스 환경이 변경될 때
		- 디바이스의 동적 유형(dynamic type) 설정이 변경되는 경우
- 위젯의 종류를 잘 보여준다.   ![placeholder][https://private-user-images.githubusercontent.com/107124308/337855647-04060caa-6dc3-4a76-939c-bcb355b988ce.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTc4Mjg5MTQsIm5iZiI6MTcxNzgyODYxNCwicGF0aCI6Ii8xMDcxMjQzMDgvMzM3ODU1NjQ3LTA0MDYwY2FhLTZkYzMtNGE3Ni05MzljLWJjYjM1NWI5ODhjZS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNjA4JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDYwOFQwNjM2NTRaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kYmEyZmMxMGJhNGUxMjIzMGYwNjU2ZDEzMDY4ZWQxZTUyZTE4ZTBjODhjMzBhNzdlY2Y5Mzk2MDhhNjhmMWFiJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.fE6S26I4ShuLG-ckolXSf-wk_DKQzEomw2aL9s5iARw]

#### Code

```swift
@main
public struct SampleWidget: Widget {
	// 1
    private let kind: String = "SampleWidget"

	// 2
    public var body: some WidgetConfiguration {
        StaticConfiguration(kind: kind,
                            provider: Provider(),
                            placeholder: PlaceholderView()) { entry in
                                SampleWidgetEntryView(entry: entry)
                            }
        .configurationDisplayName("My Widget")
        .description("This is an example widget.")
    }
}
```

위의 네 핵심 구성 요소(kind, configuration, supportedFamilies, placeholder)는 코드의 위젯 정의에서 찾아볼 수 있다.
1. Kind
2. configuration, placeholder
- supportedFamilies를 커스텀하는 방법은 아래에 있음.

#### 번외, 2024년의 위젯 샘플 코드
아래는 Widget Extensions target을 추가했을때 자동으로 생성되는 위젯 샘플 코드이다. 
```swift
struct SampleWidgetExtension: Widget {
    let kind: String = "SampleWidgetExtension"

    var body: some WidgetConfiguration {
        StaticConfiguration(kind: kind, provider: Provider()) { entry in
            if #available(iOS 17.0, *) {
                proto2WidgetExtensionEntryView(entry: entry)
                    .containerBackground(.fill.tertiary, for: .widget)
            } else {
                proto2WidgetExtensionEntryView(entry: entry)
                    .padding()
                    .background()
            }
        }
        .configurationDisplayName("My Widget")
        .description("This is an example widget.")
    }
}
```

---
## Creating a glanceable experience [11:30~]
한눈에 보기 좋은 위젯을 만들어보자.
### StatelessUI
- Widgets are not mini-apps
- No scrolling
- No videos or animated image
- Tap interactions
	- 탭하면 앱으로 딥링크
#### Deep links
- widget 크기에 따라 딥링크는 n개일 수 있다.
	- systemSmall에는 1개의 탭 타겟
	- 그 외는 n개
#### Tap Interactions
- `widgetURL` API를 사용하여 위젯 전체를 URL 링크와 연결할 수 있다.
- mid, large 위젯에서 하위 링크를 생성하기 위해, SwiftUI에서 `Link` API를 사용할 수 있다.

---
## Views, timelines and reloads [13:12~]

### Views
View 파트에서 고려해야 할 세 종류의 UI 경험은 아래와 같다.
- Placeholder
- Snapshot
	- Single entry in time
	- Return quickly
- Timeline

#### Snapshot
- Single entry in time
- Return quickly

시스템이 1개의 entry를 빠르게 보여줘야 할 때 쓴다. 

- 디자인 시간에 제공해야 하는 스크린샷이나 이미지가 아니라 렌더링된 뷰이다.
- 대부분 타임라인의 첫 번째 항목이다.
	- 따라서 위젯 갤러리에 표시되는 뷰는 사용자가 위젯을 추가할 때 보이는 뷰와 동일하다.

### Timeline
- 시간에 따른 적절한 뷰들(a combination of views and dates that are returned)
	- 다크모드, 라이트모드 모두 반환되어야 함
	- WidgetKit 확장이 반환한 결과 정보의 뷰 계층 구조는 디스크에 직렬화된다.
	- 각 개별 항목은 just-in-time 렌더링 된다.
	- 일반적으로 며칠 분량의 콘텐츠((days' worth of content))에 대해 반환되어야 함
	- 위젯이 최신 정보를 반환해야 한다면, reload 개념으로 처리한다

### Reloads [15:08~]

Reloads are where the system will wake up your extension and ask for a new timeline for each widget placed on the device.
`리로드`는 시스템에서 확장 프로그램을 깨우고 디바이스에 배치된 각 위젯에 대해 새 타임라인을 요청하는 기능입니다.

  

다시 로드는 사용자에게 항상 최신 콘텐츠를 제공하는 데 도움이 됩니다. 여러분은 어떤지 모르겠지만 저는 항상 코드가 새로운 주제를 배우는 가장 쉬운 방법이라고 생각합니다. 그럼 시작해 보겠습니다. 타임라인 프로바이더 프로토콜은 다음과 같습니다. 여기에는 주로 날짜, 환경 정보를 제공하는 컨텍스트, 시스템에서 항목을 요청하는 컨텍스트로 구성된 TimelineEntry가 있습니다.
### code
**TimelineProvider**
```swift
public protocol TimelineProvider {
	// TimelineEntry - constits mainly of a date
	associatedtype Entry : TimelineEntry
	// Context - provides environment information. 
	// and the context for which the system is asking you for entries
	typealias Context = TimelineProviderContext

	// the system asks for a single entry
	func snapshot(with context: Self.Context,
					completion: @escaping (Self.Entry) -> ())

	// the system asks for a series of entries
	func timeline(with context: Self.Context,
					completion: @escaping (Timeline<Self.Entry>) -> ())
}
```
**TimelineProvider를 conform하는 예**
```Swift
// TimelineProvider를 conform하는 예
public struct Provider: TimelineProvider {

    public func snapshot(with context: Context, 
                         completion: @escaping (SimpleEntry) -> ()) {
        let entry = SimpleEntry(date: Date())
        completion(entry)
    }

    public func timeline(with context: Context, 
                         completion: @escaping (Timeline<Entry>) -> ()) {
        let entry = SimpleEntry(date: Date())
        // policy: 로드 정책. 다음 타임라인을 언제 요청할 지 시스템에게 알릴 수 있음
        let timeline = Timeline(entries: [entry, entry], policy: .atEnd)
        completion(timeline)
    }
}
```

#### 2024년의 샘플 코드
```swift
struct Provider: TimelineProvider {
    func placeholder(in context: Context) -> SimpleEntry {
        SimpleEntry(date: Date(), emoji: "😀")
    }
    
    func getSnapshot(in context: Context, completion: @escaping (SimpleEntry) -> ()) {
        let entry = SimpleEntry(date: Date(), emoji: "😀")
        completion(entry)
    }
    
    func getTimeline(in context: Context, completion: @escaping (Timeline<Entry>) -> ()) {
        var entries: [SimpleEntry] = []
        // Generate a timeline consisting of five entries an hour apart, starting from the current date.
        let currentDate = Date()
        for hourOffset in 0 ..< 5 {
            let entryDate = Calendar.current.date(byAdding: .hour, value: hourOffset, to: currentDate)!
            let entry = SimpleEntry(date: entryDate, emoji: "😀")
            entries.append(entry)
        }
        let timeline = Timeline(entries: entries, policy: .atEnd)
        completion(timeline)
    }
}
```
  
#### ReloadPolicy
- atEnd
	- 제공한 타임라인이 끝날 때
- after(date: Date)
	- 특정 Date 이후
- never
	- 리로드 안함
  
#### system reloads
- ReloadPolicy
	- 시스템은 로드 정책을 고려해서 알아서 로드하기 좋은 시간을 결정한다.
- Widgets viewed frequently receive more reloads
	- 사용자가 자주 보는 위젯은 더 자주 리로드된다.
- Environment changes
	- e.g. 시간이 크게 바뀌는 경우

#### App-driven reloads
앱이 시스템에 리로드를 요청하는 경우가 있을 수 있다.
- 앱이 background notification을 받은 경우
	-  WidgetCenter를 통해 WidgetKit API를 사용하여 타임라인을 다시 로드하여 확장 프로그램을 깨울 수 있습니다.`WidgetCenter - reloadTimelines(ofKind:)`
- foreground, 사용자가 앱의 데이터를 변경한 경우
	- 변경 사항이 위젯에 반영되어야 하는 경우에만 위젯을 리로드하세요.
앱 프로세스나 확장프로그램 내에서 타임라인을 다시 로드할 수 있음.
#### WidgetCenter
- `reloadTimelines(ofKind:)`
	- 위젯 종류별로 타임라인을 다시 로드
- `reloadAllTimelines`
	- 모든 타임라인 다시 로드
- `getCurrentConfigurations(completion:)`
	- 현재 위젯들의 configuration list 얻기

#### URLSession
자세한 정보를 얻기 위해 서버에 쿼리해야 하는 경우도 있다. 
- Background sessions
	- 백그라운드 URL 세션 작업으로 이를 수행할 수 있음.
- `onBackgroundURLSessionEvents`
	- 의 매개변수로 payload를 전달할 수 있음
- Batch requests
	- 네트워킹 사용량을 최적화하기 위해 서버 요청은 일괄 처리 ㅊㅊ

#### Reloads
- background reloads are budgeted
	- 앱 프로세서 위젯이 백그라운드에서 실행시, 리로드 횟수는 시스템에서 예산을 책정한다.
- Be efficient

Widgets are not in every second operation.
Widgets are not about creating a live running experience on the Home Screen.

#### 위젯 리로드 유도하는 방법
- Networking
- Timeline
- App-based
각 위젯에 따라 적합하고 효율적인 환경과 방법으로 해당 위젯을 리로드해야 한다.

---
## Personalization and intelligence [18:40~]
개인화와 인텔리전스는 Intent, Relevance 두 주요 개념에 의해 좌우된다.
### Intents
사용자가 위젯을 구성할 수 있도록 하는 매커니즘으로 사용됨
used as a mechanism to allow a user to configure a widget

#### How WidgetKit works
- Intents framework
- Parameters
	- 사용자에게 묻는 질문인 매개 변수들
	- e.g. 날씨 위젯에서, 위치 
- Used with Siri, Shortcuts, and now widgets(iOS 14)

1. Intents는 앱 내의 정보를 쿼리해서 유저에게 선택할 수 있도록 보여준다
intent framework는 실제 앱 내의 주식 list와 동일한 list를 유저에게 보여주고, 유저는 여기서 한 개의 stock을 선택해서, 유저는 위젯의 구성을 설정할 수 있다.

2. 앱에 존재하지 않는 정보도. `Intents dynamic options`
	- 사용자가 configuration UI에서 검색
	- 시스템은 Stocks Intents Extension을 실행 - stock symbol들 형태로 리턴할 수 있는
	- iOS14에서는 이런 문제를 해결할 수 있는 in-app Intent handling이 지원된다.

> [**What's new in sirikit and shortcut**](https://developer.apple.com/videos/play/wwdc2020/10068) 을 참고하라.
#### 인텐트를 지원하는 위젯 만드는 방법
인텐트를 지원하려면, IntentConfiguration을 지정하고, 연관된 Intent를 지정한다.
##### Code
```swift
@main
public struct SampleWidget: Widget {
    private let kind: String = "SampleWidget"

    public var body: some WidgetConfiguration {
	    // static configuration말고 intent~
	    // intent도 지정~
        IntentConfiguration(kind: kind,
                    intent: ConfigurationIntent.self
                            provider: Provider(),
                            placeholder: PlaceholderView()) { entry in
                                SampleWidgetEntryView(entry: entry)
                            }
        .configurationDisplayName("My Widget")
        .description("This is an example widget.")
    }
}

// IntentTimelineProvider로 교체~
public struct Provider: IntentTimelineProvider {

    public func timeline(for configuration: ConfigurationIntent, with context: Context, 
				         completion: @escaping (Timeline<Entry>) -> ()) {
        let entry = SimpleEntry(date: Date(), configuration: configuration)
        // generate a timeline based on the values of the Intent
       completion(timeline)
    }
}
```

  
### 위젯 스택을 위한 Intelligence

#### Relevance
개발자가 스택의 인텔리전스에 알릴 수 있음.
allows the developer to inform the intelligence in the stack.

시스템은 가장 관련성이 높은 위젯을 지능적으로 보이게 회전시킨다. 내 위젯이 보이게 하는 방법은?
- shortcuts donation
	- 숏컷을 만들어서 기부하잖아? 그럼 인텐트가 실행될거 아냐. 위젯이 동일한 인텐트에 의해 지원되는 경우, 스택에서 해당 위젯이 보일거임.
- `TimelineEntryRelevance`
	- `TimelineEntryRelevance API`을 이용해서 타임라인 항목에 주석을 달 수 있음.

> [Add Configuration and Intelligence to Your Widgets](https://developer.apple.com/videos/play/wwdc2020/10194)

### TimelineEntryRelevance
- **score**: Float value that you provide relative to all entries you have ever provided.
	- 특정 시간에 다른 entry에 비해서 특정 entry가 더 관련되어 있는 관련성 점수
- **duration**: time interval ƒor when that particular entry is relevant.
	- 특정 시간부터 얼마만큼 오래 관련성 있는지.
시간이 적절하고 해당 항목이 가장 관련성이 높다고 생각되면 점수와 기간을 반환하여 시스템에 특정 위젯으로 로테이션하도록 알릴 수 있다.
- Relative to all entries you have ever provided.
	- 내 위젯 entry 안에서의 상대값. 시스템은 이거 뿐만 아니라 다른 모든 위젯의 값을 고려해서 스택에서 보일 올바른 위젯을 결정함.

# wrap up
- widgets are not mini-apps
- Glanceable
- Timeline, reloads, and intelligence