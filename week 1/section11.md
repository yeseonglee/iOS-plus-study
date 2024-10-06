### State

---

Int Example

```swift
import SwiftUI

struct ContentView: View {
    //struct 내에서 property를 변경 x, 따라서 따로 명시
    @State private var count: Int = 0
    
    var body: some View {
        VStack {
            Text("Hello World!")
            Text("\(count)")
                .font(.largeTitle)
            Button("Increment") {
                count += 1
            }
        }
    }
}
```

State 를 이용하면 바뀌는 값을 사용하는 뷰만 rerender 하므로 뷰가 빠르게 작동

Boolean Example

```swift
import SwiftUI

struct ContentView: View {
    
    @State private var isOn: Bool = false
     
    var body: some View {
        VStack {
            Toggle(isOn: $isOn, label: { //dollar sign 으로 바인딩
                Text(isOn ? "ON": "OFF")
                    .foregroundStyle(.white)
            }).fixedSize()
        }.frame(maxWidth: .infinity, maxHeight: .infinity)
            .background(isOn ? .yellow: .black)
    }
}
```

### Search

---

```swift
import SwiftUI

struct ContentView: View {
     
    @State private var search: String = ""
    @State private var friends: [String] = ["John", "Mary", "Steven", "Steve", "Jerry"]
    
    @State private var filteredFriends: [String] = []
    
    var body: some View {
        VStack {
            List(friends, id: \.self) { friend in
                Text(friend)
            }
            .listStyle(.plain)
            .searchable(text: $search) //서치 바
            .onChange(of: search) {
                
            }
            Spacer()
        }.padding()
            .onAppear(perform: {
                filteredFriends = friends 
            })
    }
}
```

```swift
import SwiftUI

struct ContentView: View {
     
    @State private var search: String = ""
    @State private var friends: [String] = ["John", "Mary", "Steven", "Steve", "Jerry"]
    
    @State private var filteredFriends: [String] = []
    
    var body: some View {
        VStack {
            List(filteredFriends, id: \.self) { friend in
                Text(friend)
            }
            .listStyle(.plain)
            .searchable(text: $search)
            .onChange(of: search) {
                if search.isEmpty {
                    filteredFriends = friends
                } else {
                    filteredFriends = friends.filter { $0.contains(search) }
                }
            }
            
            Spacer()
        }.padding()
        .onAppear(perform: {
            filteredFriends = friends
        })
        .navigationTitle("Friends")
    }
}
```

### Binding

---

```swift
import SwiftUI

struct LightBulbView: View {
    
    @Binding var isOn: Bool
    
    var body: some View {
        VStack {
            Image(systemName: isOn ? "lightbulb.fill": "lightbulb")
                .font(.largeTitle)
                .foregroundStyle(isOn ? .yellow: .black)
            Button("Toggle") {
                isOn.toggle()
            }
        }
    }
}

struct ContentView: View {
    
    @State private var isLightOn: Bool = false
    
    var body: some View {
        VStack {
            LightBulbView(isOn: $isLightOn)
        }
        .padding()
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(isLightOn ? .black: .white)
    }
}
```

childView 의  @Binding 요소를 parentView 에서 $으로 참조

### Global State

---

```swift
import SwiftUI

@main
struct LearnSwiftUIApp: App {
    
    @StateObject private var appState = AppState()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(appState)
        }
    }
}
```

```swift
import SwiftUI

// Pre iOS 17
class AppState: ObservableObject {
    @Published var isOn: Bool = false //값이 바뀔 때마다 참조하는 요소들에 noti
}

struct LightBulbView: View {
    
    @EnvironmentObject private var appState: AppState
    
    var body: some View {
        VStack {
            Image(systemName: appState.isOn ? "lightbulb.fill": "lightbulb")
                .font(.largeTitle)
                .foregroundStyle(appState.isOn ? .yellow: .black)
            Button("Toggle") {
                appState.isOn.toggle()
            }
        }
    }
}

struct LightRoomView: View {
    
    var body: some View {
        LightBulbView()
    }
}

struct ContentView: View {
    
    @EnvironmentObject private var appState: AppState
    
    var body: some View {
        VStack {
          LightRoomView()
        }
        .padding()
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(appState.isOn ? .black: .white)
    }
}

#Preview {
    ContentView()
        .environmentObject(AppState())
}
```

class 로 global state 선언, 각 뷰에서 @EnvironmentObject 로 참조 후 state 접근 방식으로 이용

Binding, global state 상황별 이용

### Obserable

---

```swift
import SwiftUI
import Observation

@Observable
class AppState {
    var isOn: Bool = false
}

struct LightBulbView: View {
    
    @Environment(AppState.self) private var appState: AppState
    
    var body: some View {
        
        @Bindable var appState = appState //참조 가능하게 만들어 줌
        
        VStack {
            Image(systemName: appState.isOn ? "lightbulb.fill": "lightbulb")
                .font(.largeTitle)
                .foregroundStyle(appState.isOn ? .yellow: .black)
            
            Toggle("IsOn", isOn: $appState.isOn)
            
            /*
            Button("Toggle") {
                appState.isOn.toggle()
            } */
        }
    }
}

struct LightRoomView: View {
    
    var body: some View {
        LightBulbView()
    }
}

struct ContentView: View {
    
    @Environment(AppState.self) private var appState: AppState
    
    var body: some View {
        VStack {
          LightRoomView()
        }
        .padding()
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(appState.isOn ? .black: .white)
    }
}

#Preview {
    ContentView()
        .environment(AppState())
}
```
