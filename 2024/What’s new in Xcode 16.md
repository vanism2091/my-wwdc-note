[영상](https://developer.apple.com/videos/play/wwdc2024/10135/?time=33)

# Contents
- Edit
- Build
- Debug
- Test
- Profile
# Edit [0:29]
## Code completion [0:33]
- on device 머신러닝으로 Code completion
- 주위의 맥락을 이용. 함수 명과 주석으로 코드 자동 완성
- 애플 실리콘, 맥OS Sequoia의 Xcode 16부터 사용 가능
## Swift 6 [1:01]
- concurrency safety guarantee
- 데이터 레이스 문제를 런타임 문제가 아니라 컴파일 타임 문제로 바꾼다.
- 올바르고 안전한 코딩을 돕는다.
### 채택 방법
- Xcode 16
- Project > Build Setting > Swift Compiler - Upcoming Features
	- "Isolated Global Variables" - yes
		- concurrency safe 하지 않은 변수는 warning navigation에서 확인할 수 있다.
- Target > Build Setting > Swift Compiler - Languages > Swift 6

@Migration your app to Swift 6

## Previews [2:49]
2개의 새 API가 생겼어요~

### @Previewable
previewable macro.
State같은 property wrapper에 붙어서 프리뷰 블록 안에서 바로 호출할 수 있음.
#### Code
```Swift
struct RobotFaceSElectorView: View {
	@Binding var currentFace: RobotFace

	var body: some View {
	...
	}
}

#Preview {
    @Previewable @State var currentFace = RobotFace.heart
    
    RobotFaceSelectorView(currentFace: $currentFace)
}
```

#### 기존에는...
[참고한 글](https://forums.developer.apple.com/forums/thread/118589)
```Swift
// 기존에는.. 1
// Binding된 화면만 보기 위해서 저렇게 constant로 매개변수를 넘겨서, 프리뷰에서 변경을 관찰할 수 없었음
#Preview {
    RobotFaceSelectorView(currentFace: .constant(.heart))
}

// 기존에는.. 2
// 또 다른 프리뷰 래퍼뷰를 만들어야 했음
// https://forums.developer.apple.com/forums/thread/118589
struct BindingViewExamplePreviewContainer_2 : View {
	@State private var value = false 
	var body: some View { 
		BindingViewExample_2(value: $value) 
	} 
} 
	
#if DEBUG
struct BindingViewExample_2_Previews : PreviewProvider{ 
	static var previews: some View {
		BindingViewExamplePreviewContainer_2() 
	} 
} 
#endif
```

### PreviewModifier [4:03]
- 프리뷰 사이에 데이터나 environment를 공유하기 쉽게 해줌
- 프리뷰 시스템이 데이터를 캐시할 수 있게 해줌

```Swift
struct RobotNameSelectorView: View {
	@Environment(RobotNamer.self) private var namer
 
	var body: some View {
		@Bindable var namer = namer
		List(namer.names, id: \.self, selection: $namer.selected) { option in
			Text(option)
		}
	}
}
```
로봇 네임을 서버에서 받아온다. `PreviewModifier` 덕분에 이제는 프리뷰에서 로드될 때마다 서버에서 받아올 필요가 없다. 

```Swift
struct SampleRobotNamer: PreviewModifier {
	typealias Context = RobotNamer
	static func makeSharedContext() async throws -> Context {
		let url = URL(fileURLWithPath: "/tmp/local_names.txt")
		return try await RObotNamer(url: url)
	}

	func body(content: Context, context: Context) -> some View {
		content.environment(context)
	}
}
```
- `static func makeSharedContext() async throws -> Context`: 
	- data를 저장하고 불러온다. 
	- 비동기적으로 데이터를 불러오고, 에러를 처리할 수 있다.
	- 동일한 타입의 모든 모디파이어에 대해 이 메소드는 딱 한 번 불린다.
- `func body(content:context:) -> some View`
	- shared context와 프리뷰를 감싸준다.
	- 예제의 경우 environment modifier로 context-RobotNamer를 넘긴다.

modifier를 정의했다면, preview trait을 이용해 프리뷰에 모디파이어를 제공한다.
preview modifier를 자주 사용한다면, PreviewTrait을 확장해 코드 반복을 줄일 수 있다.
#### PreviewTrait
[공식 문서](https://developer.apple.com/documentation/developertoolssupport/previewtrait)
Customizations that you can apply to a preview.
```swift
extension PreviewTrait where T == Preview.ViewTraits {
    @MainActor static var sampleNamer: Self = .modifier(SampleRobotNamer())
}

#Preview(traits: .sampleNamer) {
	RobotNameSelectorView()
}
```
프리뷰 모디파이어는 SwiftData의 모델 컨테이너를 사용하는 등 프리뷰 사이에서 데이터를 공유할 때 특히 유용하다.
프리뷰는 동일한 프로젝트와 build products를 사용한다. 더이상 별도의 copy를 생성할 필요가 없음. (기존에는 어떻게 프리뷰를 렌더링한거지..?)
# Build [06:22~]
## Debug
## Test
## Profile