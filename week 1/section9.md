- SWIFTUI 구조체

구조체 선언 → view 프로토콜 → 이름 설정

view 내부의 body (뷰의 구성)

```swift
struct viewName: View {
	var body: some View {
	Text("Hello World")
	}
}
```

#Preview → 뷰의 프리뷰를 제공

```swift
#Preview {
	viewName()
}
```

- text customizing
VStack (vertical Stack) → 수직 정렬
- (spacing: nn) ⇒  nn pt만큼
HStack (Horizontal Stack) → 수평 정렬

```swift
Text("FirstLine")
	.font(.largeTitle)
	.foregroundstyle(.cyan)
```

text style 요소

1. .font(.style)
2. .foregroundstyle(.colorName)
Stack 자체에도 적용 가능, but 가장 가까이에서 적용된 스타일이 우선순위

- add an image
    
    Asset → 이미지 추가
    
    ```swift
    Image("imageName")
    		.resizable() // 사이즈 조정
    		.aspectRatio(contentMode: .fit) // 비율 설정
    		.clippedShape(Circle()) // 이미지를 원 모양으로 clip
    		.padding(nn) // .padding([.top, .bottom], nn)
    ```
    
    URL 이미지 display
    
    ```swift
    AsyncImage(url: URL(string: "example.image")) { image in
    		image.resizable()
    } placeholder: {
         ProgressView("Downloading...")   
         }
    ```