
[영상](https://developer.apple.com/videos/play/wwdc2020/10033)

# SwiftUI in widget [0:41~]
- Provide a timeline of content for the home screen to display
- Create widgets for macOS and iOS with a single Implementation
	- SwiftUI로 만들면 iOS와 macOS에 모두 적용 가능
- get opportunity to use and learn SwiftUI
	- 위젯은 iOS 14부터 되니까~ 스유 잡숴봐~

카페인 음료 기록해서 하루 카페인 섭취량을 추정할 수 있는 앱의 위젯을 만들어보자.

# Demo [2:20~]

**Notable**
- 총 카페인 뷰의 코너는 위젯과 동심원
- 마지막으로 마신 음료 시간은 항상 업데이트

위젯 view를 만드는 데 집중하기 위해 위젯 구성은 이미 설정했다.

> 최신 버전에는 많이 바뀌었으므로 타임 스탬프와 간단 요약만.

0. 프리뷰 구성하는 방법 [2:48~]
1. 데이터의 구조체를 정의[2:56~]
	- 해당 뷰를 저장하기 위해 위젯에 property 추가
2. widget body 구현 [4:00~]
	- `minimumScaleFactor`: 레이아웃에 필요한 경우 크기 줄일 정도 설정하는 modifier
	- `Text(data.drinkDate, style:.relative)` new Date provider API
		- 시간이 지남에 따라 자동으로 지정된 날짜부터 카운트 업/다운 된다.
		- ``Text("\(data.drinkDate, style:.relative) ago")``string interpolation을 사용할 수도 있음.
			- 그러면 date는 localize됨
	- `Spacer(minLength: 0)`
	- `padding(.all)`
		- 따로 패딩 값을 정하지 않으면, 적절한 기본값을 사용한다.
	- `background(ContainerRelativeShape())`
		- 패딩을 수정해도 자동으로 위젯과의 동심원 맞춰줌
		- 부모(뷰의 위치)에 따라 적절한 corner radius가 조절되는 새로운 shape
3. 다크모드 확인하기 [13:08~] 
	- preview 수정자 설정
	- asset 카탈로그에 설정된 대로 다크모드 지원됨
4. Dynamic type은 SwiftUI라 지원됨 [13:48~]
5. placeholder 구현 [14:35~]
	- `isPlaceholder(Bool)` modifier로. 
	- [ ] 이것도 추후에 바뀐걸로 알고있음. 업뎃 필요
6. 또 다른 위젯, system medium 위젯 구현하기 [16:00]
	- 기존에 정의했던 뷰 재사용 가능하다.
	- `@Environment(\.widgetFAmily) var widgetFamily`
		- conditional layout을 위해 environment 변수를 사용할 수 있음
# New APIs [18:40~]
## ContainerRelativeShape()
위젯에서 corner radius를 자동으로 지정해주는 shape

## Displaying date and time
```swift

// June 3, 2019
Text(date, style: .date)

// 11:23PM
Text(date, style: .time)

// 9:30AM - 3:30PM
Text(startDate...endDate)

// +2 hours
// -3 months
Text(date, style: .offset)

// 2 hours, 23 minutes - Automatically updating as time pass
Text(date, style: .relative)

// 36:59:01 - Automatically updating as time pass
Text(date, style: .timer)
```

# Wrap-up
- Compelling widget experiences
	- SwiftUI로 만들 수 있음!
- SwiftUI support for adaptive layouts
- New API for dates, shapes, and links
	- [ ] 링크는.. 갑자기 왜 나왔지..?