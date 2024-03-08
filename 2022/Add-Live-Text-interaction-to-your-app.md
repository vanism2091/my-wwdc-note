# Add Live Text interaction to your app

> 🔗 [https://developer.apple.com/videos/play/wwdc2022/10026/](https://developer.apple.com/videos/play/wwdc2022/10026/)
> 
> Learn how you can bring Live Text support for still photos or paused video frames to your app. We'll share how you can easily enable text interactions, translation, data detection, and QR code scanning within any image view on iOS, iPadOS, or macOS. We'll also go over how to control interaction types, manage the supplementary interface, and resolve potential gesture conflicts. To learn more about capturing and interacting with detected data in live camera feeds, watch "Capture machine-readable codes and text with VisionKit" from WWDC22.


## What is Live Text?
> Live text analyzes an image and provides features for the users to interact with its content, such selecting and copying text, perform actions like lookup and translate, providing data-detection workflows, such as mapping an address, dialing a number, or jumping to a URL. Live Text even allows for QR code interaction.


- Text interactions
- Translate
- Data detection
- QR codes

## Live Text API

input: still or static images, paused video frames.
일반적으로는 정적인 이미지에서 live text 가능.
VisionKit은 live 카메라 프리뷰에서도 동작하는 데이터 스캐너가 있음.

@Capture machine-readable codes and text with VisionKit WWDC22

OS는 iOS 16.0, iPadOS 16.0 / macOS 13.0 이상부터, 2018년 Apple Neural Engine이 있는 기기 이후 사용 가능.

1. 분석할 이미지를 필요에 따라 가공한다
2. 이를 ImageAnalyzer에 넣으면
3. ImageAnalysis가 async하게 수행된다.
4. 분석 완료 후 플랫폼에 따라 다음 두 결과 값을 얻게 됨 the resulting ImageAnalysis object
   1. iOS) ImageAnalysisInteraction
   2. macOS) ImageAnalysisOverlayView

## Adopting Live Text

데모 앱을 보여주는데, 간단한 Image Viewer + zoom/pan 기능

```swift
import UIKit
import VisionKit

class LiveTextDemoController: BaseController, ImageAnalysisInteractionDelegate, UIGestureRecognizerDelegate {
   
    let analyzer = ImageAnalyzer()
    let interaction = ImageAnalysisInteraction()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        imageview.addInteraction(interaction)
    }
    
    override var image: UIImage? {
        didSet {
            interaction.preferredInteractionTypes = []
            interaction.analysis = nil
            analyzeCurrentImage()
        }
    }
    
    func analyzeCurrentImage() {
        if let image = image {
            Task {
               let configuration = ImageAnalyzer.Configuration([.text, .machineReadableCode])
                do {
                    let analysis = try await analyzer.analyze(image, configuration: configuration)
                    if let analysis = analysis, image == self.image {
                        interaction.analysis = analysis;
                        interaction.preferredInteractionTypes = .automatic
                    }
                }
                catch {
                    // Handle error…
                }
            }
        }
    }
}
```

영상 내에서 소개된 코드는 위와 같다.
BaseController는 부모 VC인데, image와 imageview가 정의되어있다.
`imageview.addInteraction(interaction)` 으로 인터렉션을 등록 한 후,
BaseController에서 image가 set될 때, `analyzeCurrentImage()`가 실행되고 비로소 analysis는 시작된다.

live text button을 누르면
- 분석 한 모든 텍스트에 하이라이팅이 적용된다. 
- 가능한 quick action 들이 좌측 하단에 위치한다
  - long press 가능

## Tips and tricks [5:20~]

### Preferred interaction types
사진 내 live text로 분석 후 나오는 결과 대상 중 어디에 초점을 맞출 것인지 결정하는 프로퍼티이다.
```swift
interaction.preferredInteractionTypes = .automatic
interaction.preferredInteractionTypes = .textSelection
interaction.preferredInteractionTypes = .dataDetectors
interaction.preferredInteractionTypes = []
```
- .automatic
  - built-in app과 같다
- .textSelection
  - text만 선택하고 싶은 경우. live text button은 사라지지 않음.
- .dataDetectors
  - live text button은 사라진다. 감지된 데이터들에 밑줄이 생기고 one-tap access가 가능해진다.
- []
  - 상호작용을 막는다.

기본적으로 `.automatic`과 `.textSelection`의 경우, 길게 눌러서 데이터 감지를 할 수 있다. 이 설정은 아래 프로퍼티에 종속된다.
```swift
interaction.allowLongPressForDataDetectorsInTextMode = false
```

### Supplementary Interface
아래쪽 버튼과 관련한 인터페이스
- live text button
- quick actions
  - any data detectors from the analysis
  - visible when the live text button is active
- size and position automatic
  - 사이즈와 위치, 보임 여부는 `interaction`에 의해 결정된다.
  - default 위치나 외형은 시스템에 맞춰 결정
- customizable
  - 유저가 UI를 그린 것에 따라 supplementary interface를 커스텀할 수 있음.
#### 커스텀할 수 있는 것들
```swift
// 1
interaction.isSupplementaryInterfaceHidden = false
// 2
interaction.supplementaryInterfaceContentInsets = UIEdgeInsets(top: 0, left: 12, bottom: 18, right: 12)
// 3
supplementaryInterfaceFont = UIFont.init(name: @"Copperplate", size: 0)
```
1. 보조 인터페이스 보이기/숨기기
2. Content insets를 설정해서 supplementary interface 위치 조정하기
3. 커스텀 폰트
  
### Match highlights to content [8:00~]
`UIImageView`를 쓰는 게 아니라면, Highlight가 어그러질 수 있다. 왜냐하면 `VisionKit`은 컨텐츠의 사각형을 계산하기 위해 `UIImageView`의 `ContentMode` property를 사용하기 때문이다. 
VisonKit can use its ContentMode property to calculate the contentsRect automatically.

1. 이 경우에는 delegate method인 `contentsRect`를 정의해서 조정해주자.
2. 상호작용 경계(interaction's bounds)가 바뀔 때마다 `contentsRect(for:)`는 다시 호출되지만, contentsRect가 바뀌었고 상호작용 경계가 바뀌지 않았을 때에는, `setContentRectNeedsUpdate()`를 호출해서 interaction이 업데이트 되도록 요구할 수 있다.

```swift
// 1
func contentsRect(for interaction: ImageAnalysisInteraction) -> CGRect {
  return CGRectMake(0.2, 0, 0.6, 1)
}
// 2 
interaction.setContentRectNeedsUpdate()
```

### Interaction Placement
live text 상호작용을 어디에 적용하면 좋울까?
`whichView.addInteraction(interaction)`
- Over image content
- UIImageView
  - 여기에 추가하면 굿. 자동으로 rect를 잘 계산해준다.
- 근데 scroll view 안에 UIImage view 인경우는?
  - 계속해서 변화하는 contentRects를 관리하기 힘들기 때문에 상호작용을 scroll view에 추가하지 말고,
  - 안쪽의 UIImageView에 놓는 것을 추천한다.

### Handling gesture conflicts
상호작용이 제스처 상호작용과 겹치는 경우가 있다. 이 경우 3가지 방법을 사용한다.

1. `interaction(shouldBeginAt point,for interactionType)`  함수를 이용한다.
   - return false면 interaction은 수행되지 않는다.
   - 원하는 location, 원하는 조건이 아니라면 interaction은 시작하지 않는다.
2. `gestureRecognizerShouldBegin` delegate method
   - 자신의 interaction이 제스처에 반응하지 않는것 같다면, 제스처 인식자 그 제스처를 처리하고 있는 경우가 있다. 
   -  `gestureRecognizerShouldBegin` delegate method로 제스처 인식자가 작동하는 경우를 설정할 수 있다.
3. UIView의 `hitTest:withEvent`를 override [11:55~]
   - 이전과 같이 적절성 체크한 후, 이번에는 적절한 view를 리턴한다.
```swift
func interaction(_ interaction: ImageAnalysisInteraction,
    shouldBeginAt point: CGPoint, for interactionType: InteractionTypes) -> Bool {
    // 인터렉션이 특정한 포인트에 있다
    // active 선택된 텍스트가 있다.
    return interaction.hasInteractiveItem(at: point) ||
           interaction.hasActiveTextSelection   
}
```
```swift
func gestureRecognizerShouldBegin(_ gestureRecognizer: UIGestureRecognizer) -> Bool {
  // 스크롤 뷰 내에서 확대가 적용되어 있다면 이 코드가 필요할 수 있다.
  // 제스처 인식자의 위치를 창 좌표 공간으로 전환
  let windowPoint = gestureRecognizer.location(in: nil)
  // 상호작용 뷰로 전환
  let point = interaction.view.convert(windowPoint, from: nil)

  let cancelForLiveText = interaction.hasInteractiveItem(at: point) ||
                          interaction.hasActiveTextSelection   
  return !cancelForLiveText
}
```

```swift
override func hitTest(_ point: CGPoint, with event: UIEvent?) -> UIView? {
  let windowPoint = self.convert(point, to: nil)
  let interactionPoint = self.convert(windowPoint, to: interaction.view)

  if let bounds = interaction.view?.bounds, 
         bounds.contains(interactionPoint) {
    if interaction.hasInteractiveItem(at: point) ||
       interaction.hasActiveTextSelection {
      return interaction.view
    }
  }

  return super.hitTest(point, with: event)
}
```

### Maximize performance[12:12~]
- Use one `ImageAnalyzer` in your app (이상적으로)
- Minimize image conversions
  - native type을 그대로 전달함으로써, image conversion을 최대한 줄여라.
  - 다양한 타입 중 CVPixelBuffer이 제일 효율적임 
- Analyze only when image appears on screen
  - 시스템 자원 활용을 극대화하기 위해 이미지가 화면에 나타날 때 혹은 나타나기 직전에 분석을 시작해야 한다.
- Wait for scrollviews to stop scrolling
  - 스크롤이 들어가는 앱이라면, 스크롤이 멈췄을 때에만 분석을 시작하라. 스크롤중일 때 분석하지 말라.


### Other Live Text availability [12:50~]
시스템 프레임워크 내에서 자동으로 제공된다.
- 예를 들어, `UITextField`, `UITextView` 키보드 입력용 Camera를 쓰는 live text 지원이 있다.
- `WebKit`, `QuickLook`

### AVKit (new in iOS16)
live text 지원에 AVKit을 추가했다.
- AVPlayerView, AVPlayerViewController
- `allowsVideoFrameAnalysis` 속성, 기본값 true
- 일시 정지된 프레임에서 자동으로 live text를 지원한다.
- non-FairPlay protected content에만 이용할 수 있다.

> FairPlay?
> [참고](https://pallycon.com/ko/blog/%EB%A9%80%ED%8B%B0-drm-%EA%B5%AC%EC%84%B1-%EC%9A%94%EC%86%8C%EC%9D%98-%EC%9D%B4%ED%95%B4-3%EB%B6%80-%EC%95%A0%ED%94%8C-fairplay/)

### AVPlayerLayer

- Must get frame after video pauses
- `let frame = playerLayer.currentlyDisplayedPixelBuffer()`
- Will only return a value if video paused
- Do not write to resulting pixcel buffer
- non-FairPlay protected content에만 이용할 수 있다.

직접 analysis와 interaction을 책임져야 한다. 그 중 가장 중요한 것은 현재 frame을 아래와 같은 method로 얻어야 한다. 이 메소드가 적절한 frame이 분석되고 있음을 보장하는 유일한 방법이다. 
오직 비디오가 정지 되어있을 때에만 유효한 값을 반환하며, 이 값은 얕은 복사본이기 때문에 이 값에 쓰는 것은 안전하지 않다.

