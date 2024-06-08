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
> 그러나 2023년에는 interactive 위젯이 두둥등장..! [[Bring widgets to life]]
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

