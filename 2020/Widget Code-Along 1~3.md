
# Widget Code-Along 1

> 🔗 [https://developer.apple.com/videos/play/wwdc2020/10034](https://developer.apple.com/videos/play/wwdc2020/10034)
> Take your app on a most wondrous adventure to the home and Today screens of iPhone, iPad, and Mac. Grab the starter project and code along with us! We will guide you through the process of creating a widget for your app from start to finish so that you can provide people with beautiful views and glanceable information in an easily-accessible place. Discover how to create a widget project, learn fundamental concepts for widgets and their structure, configure the widget and its provider, and start exploring timeline concepts. Once you're finished, travel onward with us to part two of the Widgets Code-along where we'll learn more about timelines, system intelligence, and configuration.

- What is a widget?
- Building our first widget
### What is a widget
- 위젯은 SwiftUI view임 - 멀티 플랫폼 코드 공유 ㄱㄴ
- 시간에 따라 업데이트되는 SwiftUI view임
	- 어떻게 업데이트 되나?
	- 언제 업데이트 되나?

### Code-along
#### 1. widget target 만들기
- file > new > target
- 일단 live activity랑 intent는 끄고 만들어씀.
- activate
- 위젯에서 쓰는 파일을 targetMembership에서 체크 하기

TimelineEntry - entry의 property를 바탕으로 위젯의 내용을 재구성한다
snapshot, timeline, placeholder에 entry 수정

- supportedFamilies
	- small, medium, large 중 필요한 것만
	- 
### 2020과 2024가 다른 점
- 별도의 struct인 PlaceholderView가 사라지고 provider안으로 들어갔음.
- `isPlaceholder` modifier가 지금 없어진듯?
  


