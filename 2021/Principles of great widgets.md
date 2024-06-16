
# Principles of great widgets

> [📺][https://developer.apple.com/videos/play/wwdc2021/10048]
> Explore the foundations of great widgets by keeping them relevant and customizable. Learn how to keep widgets up to date with timeline entries and TimelineReloadPolicies. Discover how to adapt your widget to different presentation environments and physical location. And lastly, find out how to create customizable widgets that someone can personalize to their liking.


- Relevant
- Customizable
훌륭한 위젯은 관련성 있는 정보를 , 사용자 정의할 수 있음.

# Relevant
연관성 관련해서 중요한 3가지 토픽이 있다.
1. Time 적절한 시간에
2. Presentation 적절한 표현을
3. Location 적절한 장소에서

타임라인의 몇 가지 예와 그 안에 포함될 수 있는 컨텐츠 유형
## Timeline entries
Time relevancy는 타임라인으로 구현할 수 있다.
- single timeline
	- **스크린 타임** - 미래 데이터를 예상, 예측할 수 없고 과거 기반 기록이기 때문
- multiple entries in timeline
	- **날씨** - 현재 날씨 그 다음 타임라인은 예측임
		- 위젯 리로드가 정확한 시간에 다시 된다는 보장이 없기에, 추가 예측 데이터는 유용하다.
		- 리로드가 된다면 정확한 당시의 시간을, 그렇지 않다면 타임라인 예측 시간을 보여주겠군
	- **사진** - 예측 가능한 데이터가 아닌 경우
		- 시간 별로 랜덤 추천 사진 보여줌 -> 생동감, 놀라움, 즐거움

- [ ] 지금 하려는 게 스크린 타임과 다른 점 없나? 싱글이 아니라 멀티플 타임라인이 맞는 방향일까?

### 타임라인의 multiple entries를 잘 활용할 수 있는 경우
- 미래 지향적인 컨텐츠이다
- 중요한 날짜나 데드라인이 있는 경우
- 컨텐츠를 미래에 예측할 수 있다

시스템이 위젯에게 자원 할당량을 결정하는 방식 - 더 많이 실행된 위젯이 더 많은 업데이트 예산을 갖는다.
## Widget reloads
- Individual background reload budgets
- Budget updates throughout the day
- Influenced by viewing habits
- Variable update cadence per widget
- Updates are withheld until budget availability

**Update budget (업데이트 예산)** : 위젯 업데이트의 '형평성(자주 사용할 수록 자주 업데이트 가능)'을 위해 부여한 요소. 유저의 보기 습관에 큰 영향을 받음.
자주 보는 위젯은 하루에 40 ~ 70회 백그라운드 업데이트를 받을 수 있음. 업데이트 주기는 균일할 필요 X. 요구 사항에 따라 유동적임.
예산이 확보되지 않으면 위젯 업데이트는 보류됨

Reloads are on the order of **minutes and hours**, not seconds

위젯 리로드 주기는 몇 초마다 ㄴㄴ. 홈화면에서 실행되는 화면 만드는 것이 목표 아님.

## 위젯 reload 방법 [5:50~]

### `TimelineReloadPolicy` API
- WidgetKit의 API
- Budgeted
- Scheduled dbackground updates
	- atEnd
	- afterDate:
	- never
- 위젯 자동 리로드 핵심 매커니즘
- timeline과 함께 reload policy도 제공해야함.
- 백그라운드에서 위젯을 자동으로 새로 고치려는 시기를 시스템에 알림.

### `WidgetCenter` reload API
- Budgeted
	- 일반적으로 예산이 소모됨.
- Free, 예산 없이 즉시 리로드 수행할 수 있는 일부 경우
	- 앱이 foreground에 있음
	- 사용자 세션에 참여하고 있음 (내비게이션 혹은 재생 중인 오디오)
- Event-based triggers: 기존 데이터를 무효화 하는 이벤트가 발생했을 때 위젯을 새로 고침
	- .reloadTimelines(ofKind:)
	- .reloadAllTimelines

### Significant location changes
- Free
	1. 시스템이 중요 위치 변경 감지
	2. 위젯이 위치 사용
	- 1, 2를 만족할 때, budget-free update 가능
	- 리로드 시점: 위치 변경이 발생한 때 X. 사용자가 위젯을 확인한 때 O

### System updates
- Free
- Presentation environment 변경
	- dynamic text 혹은 글자 굵기 등 접근성 기본 설정 변경
	- 언어, 지역 변경
	- iCloud 또는 App Store 계정 변경
	- 시간 변경
- Staleness
	- 시스템이 데이터가 오래되었다고 판단하는 경우
	- 예산을 충분히 받지 못한 위젯은 자체 업데이트 할 수 없다.
	- 시스템은 타임라인의 날짜, 사용자가 선호하는 새로 고침 시간, 사용자의 일반적인 조회 기록을 알고 있다.
	- 이 정보를 바탕으로 데이터의 유효성을 판단한다.

### Reload Policies [08:25~]
- `atEnd`
	- 타임라인이 종료될 때 위젯을 새로 고침할 수 있게 된다
	- 정확히 특정 시각에 위젯이 리로드된다는 보장 없음
	- 단일 입력 타임라인의 atEnd인 경우 -> 시스템에서 적합한 시간을 선택
	- Recommended for **timelines with sliding windows** on your content [09:45~]
		- 컨텐츠는 무한히 이어져있고, sliding window를 통해 컨텐츠를 볼 수 있는 경우
			- 미리 알림, 캘린더, 사진, 팁 등
	- Not recommended for
		- single-entry timelines
			- 시스템이 리로드 시간을 임의로 정하기 때문
		- data that loses accuracy over time (eg. weather)
			- 이 정책으로 한다면 타임라인 끝날 때까지 위젯은 업데이트 안되고, 관련성 없는 데이터를 계속 보여줄 것.
			
- `afterDate:`
	- 지정된 날짜 이후에 위젯 리로드
	- 딱 그 시간에 리로드되지 않더라도, 그럴 여지가 생김
	- Recommended for
		- **unpredictable data** 하루종일 예측할 수 없음
		- data whose **accuracy changes regularly** 정확도나 관련성이 주기적으로 변경됨
	- eg. 주식, 날씨, 뉴스, 메일
	- 주의점
		- Be cautious of very frequent reloads
			- 예를들어 1분 내외의 날짜를 지정한다면, 예산을 빨리 소모해서 짧은 기간 동안만 리로드 가능하다.
		- Watch for unexpected server load with time-alignment
			- 여러 디바이스에서 위젯 새로 고침 날짜를 맞추는 경우
			- eg. 미국 주식이 열리는 9:30에, 수백만 대의 디바이스에서 리로드가 실행될 것.
			- 해결책
				- Considering adding jitter to the date
					- 특정 날짜에 +- alpha 를 추가해서, 요청이 서버에 한 번에 몰리지 않도록.
				- Use caching servers
- `never` [13:05~]
	- 가장 간단한 정책, 자동으로 리로드 안함
	- Recommended for content
		- that **only changes through user interact** 유저 인터렉션만으로 변경됨
		- that **doesn't change** 변하지 않음
		- **gated on conditional access** 특정 조건이 충족될 때
			- eg. 로그인, 특정 컨텐츠 구매
			- 이런 조건들이 충족되지 않을 때 의미 있는 정보를 위젯에서 볼 수 없다면 `never` ㄱ
	- Use `WidgetCenter` API to refresh from foreground application or events
		- 위젯 센터 API가 유일한 리로드 방법
	- eg. TV, 메모, 음악, 팟캐스트, 연락처 등
		- `컨텐츠 변경 <- 유저 인터렉션 or 푸시` 인 경우

### Widget Reloads 요약 [14:23~]
- Leverage timeline entries to your advantage
	- 타임라인 항목을 잘 활용하자. (싱글 / 멀티)
- Automatic reloads via `TimelineReloadPolicy`
	- 적합한 리로드 정책을 택하자
- Event-based reloads via `WidgetCenter`

## Presentation [14:35~]
