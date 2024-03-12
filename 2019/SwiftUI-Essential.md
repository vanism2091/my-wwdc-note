# SwiftUI Essential
🔗 [https://developer.apple.com/wwdc19/216](https://developer.apple.com/wwdc19/216)

## Getting started: Views and modifiers
### Views
- A view defines a piece of UI

| SwiftUI | UIKit  | AppKit |
| ------- | ------ | ------ |
| View    | UIView | NSView |

SwiftUI 코드와 View Hierarchy graph는 매우 흡사하다.
왜냐하면, SwiftUI는 View를 명령형인 UIKit과 달리 선언적으로 정의하기 때문이다.

#### Imperative와 Declarative 비교 with Avocado toast의 예. 
명령형 - 방법의 나열.. 아무튼 선언적인게 더 편함.

#### View Container Syntax
```swift
container {
	content
	content
	...
}
```

**view builder clousre.** 
`addSubview(:View)` 함수를 호출하는 대신 ViewBuilder 덕분에 선언적 코딩을 할 수 있다. 

![image1](https://private-user-images.githubusercontent.com/107124308/311976173-8b061811-a5f2-4a92-ac01-fd0fd7cbdaa9.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTAyMjc4ODQsIm5iZiI6MTcxMDIyNzU4NCwicGF0aCI6Ii8xMDcxMjQzMDgvMzExOTc2MTczLThiMDYxODExLWE1ZjItNGE5Mi1hYzAxLWZkMGZkN2NiZGFhOS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzEyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMxMlQwNzEzMDRaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0xOTdmN2M4YjA3MDMxZDY1NzNiY2I2OTI5ZDdkZTU2YTA4NGUxMGQ3MDYwMTU0N2UxZDk4ZGI2ZTA1MWJiNWFmJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.b5BgvDYrrgrNUZVSUVh0jP3HEa-SgXYgiVAN1biG574)
> The Swift Compiler knows how to translate a closure marked by this attribute into a new closure that returns a single view representing all of the contents within our stack.

Swift Compiler는 `@ViewBuilder` attribute가 붙은 closure를 1개의 view로 변환시킨다.

#### Binding Syntax

> The leading dollar sign indicates that we're passing a binding to the control instead of just a normal value.

parameter 앞의 `$` 는 일반적인 값이 아니라 binding을 넘긴다는 의미!

바인딩이란?
SwiftUI는 `@State` 값을 View 뒤에서 관리한다. view의 children view에서 `@State`로 관리하던 속성을 변경할 수 있을 때, read-only value가 아니라 변경할 수 있는 값을 넘기는데 이를 Binding으로 넘긴다. Truth of source는 `@State`이나, `@Binding`이 업데이트 되면 parent view의 `@State`값도 변경되는 것이다.


### Modifiers
`Text("Avocado Toast").font(.title)` 에서, `Text(...)`는 View, `.font(.title)`은 modifier이다.

a modifier is just a method that creates a new view from an existing view.

![image2](https://private-user-images.githubusercontent.com/107124308/311979411-7e74f52e-5277-4288-bbae-5e5f5638e762.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTAyMjg2MzIsIm5iZiI6MTcxMDIyODMzMiwicGF0aCI6Ii8xMDcxMjQzMDgvMzExOTc5NDExLTdlNzRmNTJlLTUyNzctNDI4OC1iYmFlLTVlNWY1NjM4ZTc2Mi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwMzEyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDMxMlQwNzI1MzJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04ZmFkMzAxY2UxZTdmMmQ3MDRiNTA5MzcyZmI2MzY3YTgwMzI0ZmVkYzU2ZGM5YzlmZmRmOTI5MDUwNmExZDBiJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.npMq6ZvZckexfG33rgV9BPGis_W0EIyYCYIjuQYAYQs)


뷰 계층 diagram은 왼쪽과 같다.
modifier가 추가될 수록, 현재 view를 wrapping하는 새로운 view가 추가되며 다른 modifier를 체이닝할 수 있다.
SwiftUI는 view modifier가 많더라도 이를 효율적인 구조로 축소해서 렌더링한다.

#### Modifier Chaining 구문이 갖는 장점
> 1. modifier chains enforce a deterministic ordering of visual effects. \
>    modifier 체인은 시각적 효과의 결정론적 순서를 강제한다

1로 인해 modifier의 순서가 중요해진다.
```swift
// A
Text("Avocado toast")
	.padding(.all)
	.background(Color.green)

// B
Text("Avocado toast")
	.background(Color.green)
	.padding(.all)

// C
let textView = TextView("Avocado toast")
textView.background = Color.green
textView.padding = .all
```
A와 B는 서로 다르게 그려진다. 그런데, background와 padding이 text view의 property라고 가정해보자.(UIKit마냥) 이 때, C는 A와 B 중 어떤 view와 같을까? documentation을 읽거나, 직접 시도해보지 않는 이상 상상할 수 없다. A, B 처럼 chaining을 사용하면 순서가 명확하기 때문에 쉽게 그릴 수 있다.

> 2. modifiers can be shared across views.\
>    중복되는 modifier를 공유할 수 있다.

```swift
VStack {
	AView.opacity(0.5)
	BView.opacity(0.5)
	CView.opacity(0.5)
}

VStack {
	AView
	BView
	CView
}
	.opacity(0.5)
```

> General principle of SwiftUI: Prefer smaller, single-purpose views\
> 이해하기 쉽고, 유지하기 쉬운

> Build large views using composition

> The entire SwiftUI framework is oriented around composition of small pieces and you should organize your code in the same way.\
> SwiftUI 프레임워크는 작은 조각의 합성을 지향한다.

## Building custom views [19:33~]
커스텀 View를 만드는 방법
```swift
// custom view의 예
struct OrderHistory: View {
	let previousOrders: [CompleteOrder]

	var body: some View {
		List(previousOrders) { order in
			VStack(alinment: .leading) {
				Text(order.summary)
				Text(order.purchaseDate)
					.font(.subheadline)
					.foregroundColor(.secondary)
			}
		}
	}
}
```

1. `struct OrderHistory: View { ... }` 
	- View protocol을 채택하는 struct `OrderHistory`.
2. property: `let previousOrders`
3. `var body: some View { ... }`
	- 계산 속성 `body`, return 값은 View를 채택하는 특정 타입으로 나중에 컴파일러가 결정한다.
4. `List(previousOrders) { order in ... } `
	- 각 주문마다 후행 클로저의 ViewBuilder에 의해 각 뷰가 새로 그려진다.

> **Views in UIKit**\
> UIKit의 커스텀 VIew는 UIView를 상속받는다. UIView는 common view property를 상속 받는다. 예를 들어 `alpha: CGFloat`, `backgroundColor: UIColor` 등. 
> OrderHistory가 UIKit처럼 UIView를 상속받는다고 가정하자. 그러면 OrderHistory는 UIView의 property를 모두 갖고 있을 것이다. 이에 더하여 자신이 필요한 메소드 혹은 속성을 추가로 정의한다.
> 그런데 SwiftUI는 이러한 뷰의 특성을 분리된 modifier로 적용한다. 그리고 modifier는 새로운 뷰를 생성한다. 즉, 이러한 속성에 대한 저장소가 모든 개별 뷰에서 상속되는 것이 아니라 각 수정자 뷰의 뷰 계층 구조에 분산된다. 따라서 뷰는 더 가벼워지고, 메모리를 최적화할 수 있다. 
> common storage template이 필요하지 않으므로 protocol을 채택한다. 

### View Protocol
<details>
<summary>영어 script
</summary>
And in this world, it makes a lot of sense that view just becomes a protocol because it's no longer needing to serve a common storage template for all of your views. But what does this view protocol actually do? Well, let's remember our conceptual definition of a view. Which is that a view defines a piece of our UI and we build bigger views by composing together smaller views. And that's all that the view protocol does. It defines a piece of our view hierarchy, giving it a name so that it can be composed and reused across your entire app. And each concrete type of view is just an encapsulation of some other view representing its contents in its body property and all of the inputs required to create that view represented by its properties.

Now the actual protocol just defines that one body property returning just another kind of view.
</details>
View의 개념적 정의: A view defines a piece of UI

View 프로토콜은 View 계층 구조의 일부를 정의하고 이름을 지정하여 전체 앱에서 구성 및 재사용할 수 있도록 한다. 그리고 각각의 구체적인 View 유형은 body 속성으로 내용을 나타내는 다른 뷰의 캡슐화일 뿐이며, 해당 뷰를 생성하는 데 필요한 모든 입력은 해당 property으로 표현됩니다.(OrderHistory에서 previousOrders)

```swift
// A piece of user interface
public protocol View {
	// The type of view representing the body of this view
	associatedtype Body: View

	// Declares the content and behavior of this view
	var body: Body { get } 
}
```

View protocol은 body 속성을 정의하고 body는 다른 view를 반환한다.

Q. 이러한 정의가 recursive 한 거 아냐?
View가 `var body: some View`를 부르고.. 이를 반복한다면, body를 부르는 것이 끝없을 것 같은데, SwiftUI는 많은 형태의 primitive view를 제공한다. 따라서 body call chain의 끝에는 primitive view. recursive는 끝나기 마련이다! 
swiftui는 많은 형태의 기본 뷰를 제공한다. (atomic building block)

### Primitive View
- Text
- Color
- Spacer
- Image
- Shape
- Divier


### View - struct
<details>
<summary> 영어 script
</summary>
Now we also saw that our Custom View is defined as a struct instead of a class. And this goes back to how views are defined declaratively in SwiftUI.

In this case, that means our views are not persistent objects that we update over time using imperative event-based code.

Instead, our views are defined declaratively as a function of their inputs.

So whenever one of our inputs changes, SwiftUI will call our body property again to fetch an updated version of our view.

Now List that we're using here -- List is a great example of the power of declarative code. If our previousOrders collection changes, SwiftUI will compare the old and new versions of our list and efficiently update the rendered result onscreen just based on what's changed.

For example, I've been working on cloud sync for my app. And it's really important to me that all of my avocado toast data is available on all of my devices. So let's see what happens if another device starts adding and removing orders from our history.

What you see on the right is SwiftUI automatically diffing the changes in our collection and synthesizing insertions and deletions and then rendering them with appropriate default animations. And this is all functionality that you get for free without writing any additional code.  It's pretty awesome. And the reason this works is because you don't have to manage that persistent render state yourselves. Instead, you can just generate new values for your view based on your current data in that body property.

And you can let SwiftUI generate the necessary changes between those two versions on your behalf.

And that's the power of declarative code.

So let's build out the rest of our orderHistory view. And if you recall, our original design included these icons for any extra toppings that I included in my order, like salt and red pepper flakes. So let's start by showing that icon for salt. First, we'll add a horizontal stack with a Spacer after our text.

And then I'll show my SaltIcon view but only if our order contains salt.

As you can see in the code here, that ViewBuilder syntax that we talked about earlier, it lets us use natural control flow like if statements to declaratively define when a view should be included in our stack.

And using if statements like this in our declarative code feels really natural. But there are also other ways to write conditional code within your views. And it's important to choose the right tool to get the correct result onscreen. So let's look at a quick example to see what I mean. I built another screen for our app which lets you choose between a normal and flipped AppIcon.

And my first pass at this was writing a custom view that takes a flipped state as an input and conditionally applies a rotation modifier based on my state.

However, this produces an ugly crossfade animation when we actually try to flip that icon. This is because our code is telling SwiftUI to switch between two different kinds of views. A view wrapped in that rotation modifier versus our AppIcon just by itself. And by default, SwiftUI fades in and out views when they're added and removed. Which is why we get this crossfade effect.
</details>


SwiftUI에서 뷰는 
- 명령형 이벤트 기반 코드를 사용해 시간이 지남에 따라 업데이트 하는 영구적인 객체 X
- 입력의 함수로 선언적으로 정의된다.
따라서, input이 변하면 SwiftUI는 body를 다시 호출하고, 업데이트를 뷰에 반영한다.

예시에서, PreviousOrders가 변경되면, 변경된 내용에 따라 SwiftUI는 효율적으로 리렌더링한다. 리렌더링을 위한 추가적인 코드를 직접 작성할 필요가 없다.
### View 내에서 조건부 코드를 작성하는 방법
1. ViewBuilder 안에서 if문 등 제어문을 사용할 수 있다. - 서로 다른 뷰, 트랜지션
2. modifier 내에서 삼항 연산 조건문을 이용한다. - 1개의 뷰, 애니메이션

```swift
struct IconOrientationEditor: View {
	let flipped: Bool

	var body: some View {
		ZStack {
			Color.gray
			
			// 이렇게 하면 ugly함 :)
			// fade-in out되면서 view가 사라졌다 나타나길 반복한다.
			// 이 경우 두 뷰는 서로 다른 뷰이다.
			if flipped {
				AppIcon()
					.rotationEffect(.degree(180))
			} else {
				AppIcon()
			}
			
			// 이렇게 하면 1개의 view가 flipped 값에 따라 rotation된다.
			// 위보다 더 좋은 animation
			AppIcon()
				.rotationEffect(.degrees(flipped ? 180 : 0))
		}
	}
}
```


### OrderHistory에서 OrderCell 빼내기

```swift
// 기존의 OrderHistory
struct OrderHistory: View {
	let previousOrders: [CompleteOrder]

	var body: some View {
		List(previousOrders) { order in
			HStack {
				VStack(alinment: .leading) {
					Text(order.summary)
					Text(order.purchaseDate)
						.font(.subheadline)
						.foregroundColor(.secondary)
				}
				Spacer()
				if order.includeSalt {
					SaltIcon()
				}
			}
		}
	}
}
```

```swift
// refactoring
struct OrderHistory: View {
	let previousOrders: [CompleteOrder]

	var body: some View {
		List(previousOrders) { order in
			OrderCell(order: order)
		}
	}
}

struct OrderCell: View {
	let order: CompleteOrder

	var body: some View {
		HStack {
			VStack(alinment: .leading) {
				Text(order.summary)
				Text(order.purchaseDate)
					.font(.subheadline)
					.foregroundColor(.secondary)
			}
			Spacer()
			
			if order.includeSalt {
				SaltIcon()
			}
			if order.includeRedPepperFlakes {
				RedPepperFlakesIcon()
			}
		}
	}
}
```

선언적 코드를 사용하면 SwiftUI가 백그라운드에서 최적화하므로 새 wrapper 뷰를 추가하는 것(OrderCell을 만드는 것)은 사실상 성능에 부담이 되지 않는다. 따라서 여기서 중요한 것은 더 이상 자신에게 가장 적합한 방식으로 뷰 코드를 구성하는 것과 앱에서 최상의 성능을 얻는 것 사이에서 타협할 필요가 없다.

### ForEach
새로운 토핑이 생성될 때마다 if문을 직접 추가하기 보다 동적으로 추가되는 방법은 없을까? ForEach를 써보자.
List와 같이, ForEach도 data의 collection을 바탕으로 각 data를 viewBuilder의 view와 매핑시킨다.
그러나 List와 달리 ForEach는 딱 ViewBuilder에 정의된 view만 추가한다.
```swift
struct OrderCell: View {
	let order: CompleteOrder

	var body: some View {
		HStack {
			VStack(alinment: .leading) {
				Text(order.summary)
				Text(order.purchaseDate)
					.font(.subheadline)
					.foregroundColor(.secondary)
			}
			Spacer()
			ForEach(order.toppings) { topping in
				ToppingIcon(topping)
			}
		}
	}
}
```

모든 액션에 대해서 우리가 작성하지 않아도 된다. SwiftUI가 자동으로 해주는 것이 많다.
- 데이터의 변경에 따른 view 변경. 
- font dynamic type
- dark mode
- 등등

---
## Composing controls [33:12]




---
## Navigating your app



<details>
<summary>
영어 script 33:12~
</summary>


Hello, everybody. At this point, we have a pretty good start to our app, with Matt building out the initial order form and history screens. But one thing that stands out is that this doesn't quite look like iOS apps we're used to. They're usually not these simple vertical stacks of controls. And typically, this type of UI looks something more like you'd see on the right. And one of the biggest differences is the container around the controls themselves having this standardized group list style. Now in SwiftUI we refer to this as a form. And a form is a container just like VStack, but one built specifically for building these sections of heterogeneous controls, giving the overall result a standard look and feel no matter what the platform.

Now we've already defined the exact set of functionalities we want in our app. The title, Toggles, stepper and button.

And all we're doing is changing the container itself from the existing VStack into a form. And then we can easily add in some sections to divide up that content.

Now just as Matt previously discussed, our code continues to reflect the resulting UI. And since the core definition of our controls didn't change, our code really didn't have to either. Just by changing the container from a VStack to a form resulted in the controls automatically adapting to that context. From the overall background and scrollability to the lines separating each of the controls, to even the styling of things like button. This is yet again SwiftUI taking care of the details for what exactly it takes to render those elements, and allowing us to focus on the functionality of our app.

Now one subtle change that happened isn't visible from this static screenshot. Focusing on the buttons, you can see that the alignment, padding and decoration has all changed around the button, but the press state has even taken on the special full bleed effect that you would expect from this type of UI, all the while showing the same exact definition of being a button.

Like you might expect, this same definition works in other contexts or other platforms, having a wide variety of possible looks and feel. button also demonstrates the same inherent ability for composability that we've seen in other views. The label is of course not constrained to just being a text but could also be an image. It really could be any type of view that we could define, even an explicit vertical stack of an image and a text.  And this inherent composability enables a wide variety of possibilities while at the same time enabling button to be distilled down to two fundamental properties. The action it performs when activated and the label describing what that action is.

And that's the entire API surface of button. This is of course not to say that these are the only two ways that buttons can be customized. Like we saw before and will continue to see, both context and modifiers enable adding many more rich behaviors from disabled state to the styling of the button to even control sizes on macOS. But this core definition plus adaptive behaviors enables any type of button. And over time and across the different platforms, we've seen a lot of different buttons. Not only did they vary based on how they look but also in how we interact with them, from a click to a tap, to being selected using the switch control or the Siri Remote, but they can all be distilled down to having an action and a label.

Now just like button, every control in SwiftUI carries the same ability to have this adaptive behavior.

Controls describe the purpose or the role that they serve instead of just how they look. And this allows them to be reused across these different contexts and platforms and adapt to those situations. And this also helps them have that smaller API surface catered to that exact role. And at the same time still having fewer controls rather than need a control for every context you might need to use it in.

And all the while still enabling really powerful customization such as completely redefining how buttons should look in your app. Now we saw how this adaptivity allowed us to quickly transform from a simple stack of controls into the standard look and feel of a system form. But this same adaptivity also enables us to take these controls to other platforms such as the Watch, so we can quickly order our toast on the go.

Now the other control we're already using is Toggle. And you've already seen how Toggle in SwiftUI is more than just a literal switch. And this is true regardless of the platform it's on. And like button, Toggle has two fundamental properties, whether it's on or off, and the label describing the overall purpose of the Toggle. And again, that's reflected in the construction itself.

Now one notable difference from button is that it doesn't take an action, but instead takes a binding to a Boolean value. And this binding is a direct read/write connection to some piece of state or model in your application and allows the Toggle to reflect and update that without manually needing to respond to an action, pull the value out and then set it in your model. It takes care of it all for yourself.

 Now Toggle and the other controls are also adaptive in one other very important way. For some people, UI's are a visual experience while others might predominantly use their other senses to experience that exact same UI. For instance, people with impaired vision are able to use VoiceOver to navigate and interact with your app using audio. And for those of you who haven't heard it, this is what it sounds like to begin using VoiceOver. VoiceOver On.

Now VoiceOver is just one of the system-wide features that are able to take your UI and surface it in these alternate forms. And because Toggle and the other controls are defined based on their purpose and include that human interpretable label, they can automatically adapt for these features. So when we navigate to this Toggle using VoiceOver -- Include Salt, Switch button, On Double-Tap to Toggle Setting. It is able to reflect that same label. And this is true even when the label isn't text. Now for images, if the image name isn't descriptive enough, you can explicitly provide a label directly alongside the image.

And of course even for completely custom --  It's really exciting, yeah.  And of course even for completely custom views, you can always explicitly provide the label using the accessibility label modifier.

Now in addition to VoiceOver, this information also admits use for other features, like the new Voice Control on iOS and macOS so that we can say, "Tap Include Salt," and our UI behaves as we expect.

And making sure your app is accessible means it will work with all these different technologies and means that everyone can use your app. And SwiftUI is here to help.

There's a great talk this year that will go into a lot more detail about how you can make sure that your SwiftUI app is fully accessible.

Now at this point we've been able to quickly build up this initial basic interface that has all the behaviors we expect: dynamic type, Dark Mode and accessibility. But we've really only added a few customization options for the toast itself. And of course everyone knows that a professional artisanal toast repertoire comes with a variety of different bread types, methods to prepare the avocado and of course a variety of spreads and add-ons. To add in these more advanced configuration options, we can look for some inspiration from the flexibility that is macOS. Or we might want to have a little utility window to allow us to order toast right from our desk.

You can see here that the existing controls we're already using take on the expected look for macOS -- the Toggles, the stepper, the button. But we also have a few additional controls that allow us to pick from the type of bread, the spread to add, and how to prepare the avocado.

Now these are all examples of the Picker control in SwiftUI. Picker is built for the purpose of selecting one value out of a set of options.

Now Picker is obviously a little more complicated than the other controls and in fact has three core properties instead of two.

The options that you can pick from, the current selection from those options and the label describing the overall purpose of the Picker. Now the selection is a binding, just like Toggles is on property. Which allows us to directly connect it again to our modeler state. And the type of this binding corresponds to the tag values associated with each of these options. When one of the options is selected, that tag value is written back into the selection and back into our model, all with no work.

Now of course Pickers on macOS don't always manifest as pop-up buttons. In this single window, we can see two different styles of Picker, both a pop-up button and a radio group. While SwiftUI automatically provides a default style that's adaptive to where controls are used, controls also inherently have the ability to customize their styling, both to system-provided styles and even custom-built ones. In this case, we want to override the default style and impose an explicit radio group since we know that we are only picking from two options.

Now we can consider doing the same for our spreads.

But what might start out as a humble set of four possible spreads could quickly grow into a wide variety. So when it comes to building our Picker, we obviously wouldn't want to splay out each of these options one by one, just as we wouldn't want to build a UI that displays them all as radio buttons.

We've already seen using ForEach to build data-driven views. And since each of these options are views themselves, we can use it here as well.

This is a lot better.

Here we're going through each of the cases of spread and creating a new option with the spread's name and the spread itself as the tag.

Now --  Now obviously Pickers exist on more than just macOS. And then isolation -- a Picker on iOS looks like the traditional wheel-style Picker. However, since we're building up a form, SwiftUI will automatically adapt Picker to take on another really common style of this type of UI.

Here we can see that the spread Picker is now represented by a navigation row displaying both its label and currently selected value. Tapping on that row brings us to a list of all of our options. And tapping one of those selects it and brings us back.

 You stole my punch line. This is SwiftUI taking care and creating that entire interaction just with our simple creation of a Picker.  Making it trivial to build out the rest of our three Pickers. And just like in macOS, we still have explicit control over the ultimate style. If we wanted a wheel-style Picker here, we could again just impose that.

Now we have a pretty nice set of apps at this point. But it's one thing to order toast at our desk or while on the go, and it's another thing entirely to have heated debates with friends and family about what exactly makes the best avocado toast.

The form on the right side consists of the same content that we saw in the other apps and taking a look at the code that's used to build it, it's not a surprise that it's using the same structure and control creation that we used before. And again, the difference is that automatic adaptation. For instance, Toggle being represented using on/off buttons instead of switches.

And this gets to the heart of something really important across all of SwiftUI. The idea that you can learn a concept once and apply it anywhere.

SwiftUI is not just a means to write once and run anywhere, but it's a framework that enables you to learn these core concepts and use them in a variety of different contexts and platforms. This scales from the modifiers and ViewBuilder syntax to the shared core types like color, image and ForEach, to even these higher-level controls. One example that really illustrates to me this reuse of knowledge is a slightly platform-specific example of building a contextMenu. The contextMenu itself can be attached to an associated view using a modifier. And this modifier uses the ViewBuilder syntax to define its menu contents. Now if we take a look at the menu, we can see a few familiar concepts. Some elements that on click perform an action and have a label describing that action, and others that specifically get turned on and off. So it's not a surprise that the contents themselves are built up using the same controls we've already learned how to use. buttons, dividers and Toggles. But still, automatically taking on the expected look and feel for our macOS menu, from the hover and accelerated gesture handling, to the special highlight and selection styling.

From these few examples, you can already tell that controls in SwiftUI are a little bit special. They're defined based on their purpose, the role that they serve, their connection to your app's model, rather than specifically to their visual appearance. And this means that they're inherently reusable across a variety of historic contexts, and the appropriate look and feel can be determined based on that context, platform or other information. And at the same time, they're customizable, both in their use of views as labels and options as well as being able to arbitrarily style these controls from the system styles like you saw with Picker to even completely custom-built styles. And no matter what the style, still having accessibility support built right in.

Now earlier Matt showed a few examples of using modifiers to impose additional behavior on views. And the same is true for controls as well.

One example that those of you on iOS will already be familiar with is changing the tint or accent color for your UI, which affects how many different system controls appear. And if we want to apply this to our entire app, we can apply the accentColor modifier to our outermost view and it will be inherited by the entire hierarchy such as this button. Now when it comes to disabling controls, we can use the disabled modifier. For instance, disabling the Order button when maybe there are no toasts being ordered. But there also might be scenarios when we need to disable entire groups of controls. For instance, when we're unable to connect to the toast network to even place our order, we probably want to disable each and every control in our form. But this looks a little tedious and error-prone if we ever add additional controls. But like you saw with modifiers in general, we can instead lift this modifier up and apply the modifier to our entire form, just like we did with the accentColor modifier.

 Now all the controls in our form will be disabled based on this single statement. And all of this adaptivity and inherited behavior is pretty powerful and potentially comes as a surprise since we're using these simple value-type views. But let's take a little look under the hood for how some of this works. These examples are built on top of something called the environment. And the environment consists of all the context for where your views appear in.

These are things that you might have previously thought of as being shared global state, part of our trait collection or properties on your view, or maybe even had to reach up to some ancestor object to pull the value out. But now this is all packaged up into the environment. And it's accessible to any of you that might want to access it. And each view inherits that environment from its parent.

Now as an example, when running in an Arabic locale, the environment at the root of our app has a right-to-left layout direction. And every view inherits that layout direction. But at any given point, the environment can also be overridden for a subtree of views. So if we were building up some media playback controls, we'd want to ensure that they're laid out left-to-right. And so by using the environment modifier, we can impose that on that hierarchy.

Now the environment is also one of the important technologies that helps make previews so powerful. It enables showing the same exact UI in a variety of these different contexts so we can really preview our app against all the ways people might be using them.

Now you've seen how the environment automatically affects various system views, and custom views are able to use the environment as well. So I've been working on a little control for our next update, which allows deciding exactly where on top of our toast an egg should be placed. You can see it's built up using a simple ZStack of two images: a toast on the bottom and an image being positioned with a dragGesture on top. With that, we can tap and drag the egg into just the right spot.

Now if we go to use our Egg View, there may be some cases we need to disable it. Maybe the shop ran out of eggs.

But since we're using a system dragGesture, it will automatically be disabled by the disabled modifier. So if somebody comes in and tries to drag that egg, it won't budge.

Of course, we should also offer some visual feedback that it's disabled as well, and thankfully that's pretty easy.

We can add an environment property that's connected to the isEnabled value from the environment. And we can use its value just like any other property. For instance, reducing the saturation of our overall construction when it's disabled.

And if the egg placement view ever becomes no longer disabled, SwiftUI will automatically recall our view's body and re-render it to the now undisabled state. And again, this is SwiftUI automatically managing our dependencies on the environment so we can just express our view's relationship to it and not have to worry about observing for when things change.

Now we've covered a number of controls and how to compose those all together. But we're still missing one really important piece of every app, and that's navigating between these screens, from the order form to the egg placement Picker to the order history. Now let's start in with the order form. Now a problem that some of you might have already noticed is the look of the title in the form. It doesn't use the standard navigation bar styling. So we can first wrap our Orderform in a NavigationView as the content of our app. NavigationView provides the ability to navigate through screens of our app revealing more nested or detailed information. On iOS, NavigationView also adds in the standard navigation bar Chrome. And then we can use the NavigationBarTitle modifier to produce that large beautiful title for our form. Now this modifier is a little bit special. It provides information that's able to be interpreted by a NavigationView ancestor. We saw earlier examples of modifiers that have information flow down the view hierarchy using the environment, and this is an example of one that flows information upwards using something called preferences. Now we're not going to go into too much detail on that, but you'll see other similar examples later.

So focusing on the form, the next thing we want to do is add support for including an egg in our order. So we can add a little Toggle here and then whenever somebody opts into including an egg, we can add a navigation row which takes us to our EggLocationPicker. So let's expand out the form to see how this works. It's built using a Toggle bound to whether or not our order includes an egg. And then it uses the same ViewBuilder conditional that Matt showed us earlier to optionally include that navigation row. Now the really cool thing is that we provided an animated binding to the Toggle. So whenever somebody taps that switch, our navigation row will be animatedly inserted in for the formList just with the setup.

And expressing the navigation row is also amazingly simple. It's using a specialized control called a Navigationbutton which allows us to provide some destination content to navigate to when interacted. Navigationbutton automatically comes with all of the right look and feel such as the disclosure indicator on the trailing edge. Now because views are lightweight, we don't have to worry about having created the EggLocationPicker here. SwiftUI takes care to only render these views once they're actually presented. Now inside the EggLocationPicker we can use our PlacementView, customize the navigation bar so that once it's presented, the title reflects its current state. We could also add a trailing BarItem to quickly reset the egg back to its start state. Like you hopefully expect at this point, the items here are the same views we've already learned how to use, so we can just provide a button. And that's all it takes to create this complete navigation experience.

Now we can turn our attention to the OrderHistory. Now we want to navigate to this, but it isn't more detailed or nested information of the form, but it's instead an entirely different section of our app.

This is more appropriate for the use of a TabbedView.

As such, we can wrap our form in a TabbedView just like we did NavigationView and then add the OrderHistory as another child.

Both have tabItemLabel modifiers that it described to the TabbedView how to label them in the TabBar.

Now we can quickly jump over to our OrderHistory. But at this point we've a pretty simple level of detail for the OrderHistory and we might want to expand this into a much more detailed set of information that we navigate to from our history list. This is another case of nesting or showing more detailed information like we saw earlier with NavigationView and button. So we can replace the contents of our OrderHistory list so instead of it being in list with the OrderDetail displayed inline, we can instead use this new OrderDetail as the destination for our NavigationButtons. And really it's this simple to build a data-driven list that's able to navigate to additional content.

This works great on the iPhone but if we take a look at the iPad, we want this to be set up using a master detail with a SplitView. Unlike NavigationStacks on iPhone that push onto a single RootView, here we know we have two points of navigation: the Master which is able to push content onto the Detail. So while our NavigationView behaved correctly with just the single RootContent on iPhone, we want to indicate that it intrinsically has these two pieces of content: the OrderHistory Master and the DetailView. Here we can use an OrderDetailPlaceholder View to act as the placeholder for when nothing is selected. Now with this, when a Navigationbutton is interacted with in the OrderHistory, it will automatically get pushed onto the OrderDetail. This will behave as we expect on the iPad and other wide-size classes using a SplitView. And for narrow-size classes, will automatically collapse into a single NavigationStack. And of course, this works on macOS as well, resulting in a SplitView there. And this isn't really write once and run anywhere; there are still these additional design considerations such as the increased information density on macOS.

But SwiftUI is automatically taking care of a base level of platform look and feel from how the SplitView behaves to the height of the table rows, et cetera. So that we can learn how to use these different concepts once and then apply them anywhere. And then we can focus our time on those exciting and custom features that make each of your apps great.

Now we've covered a reasonable amount of breadth in this last hour and there are a number of other talks that go into a lot more detail. We showed how state and bindings will change how you interact with controls, but data flow in SwiftUI will make you rethink altogether about data-driven UI updates.

We built up a few custom views using layout adjusters, but Custom Controls in SwiftUI will go into a deep dive on advanced used of layout, graphics and animations and has the most awesome demo.

We know that many of you are going to be eager to jump into SwiftUI right away and might be wondering if you can integrate this into your existing app. And the good news is yes, SwiftUI is designed to be integrated seamlessly alongside your existing views and models. And we have an entire talk showing you how to do that.

We touched upon how SwiftUI is designed to make your app accessible to everyone out of the box. Of course, there will always be some additional considerations and this talk will go into additional detail. And finally, last but certainly not least, we've shown how SwiftUI raises the bar for how much you can share across platforms. SwiftUI on all devices takes that as a baseline and goes into additional detail on how you can make a great app on any platform. There are a few additional talks such as WatchOS Specifics for more details on what's driving this and What's New in Swift. And finally, thank all of you for watching. We are so excited. 
</details>
