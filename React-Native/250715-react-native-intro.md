# 📱 React Native란?

React Native는 **React.js**와 **Native** 플랫폼을 연결하여 **실제 네이티브 모바일 앱(iOS, Android)**을 만들 수 있도록 해주는 프레임워크입니다.

## 🔷 React.js란?

- 사용자 인터페이스(UI)를 만들기 위한 **JavaScript 라이브러리**입니다.

- 보통 웹 개발에 사용됩니다.

- 웹 지원은 `react-dom`이 추가해주는 기능입니다.

👉 **React 자체는 플랫폼에 구애받지 않는(agnostic)** 라이브러리입니다.

## 🔷 React Native란?

- 모바일 앱 개발을 위한 React 컴포넌트 모음입니다.

- iOS나 Android에서 제공하는 **기기의 기능들**(예: 카메라, 위치 정보, 진동 등)을 **JavaScript 코드**로 다룰 수 있게 해줍니다.(내부적으로는 Java, Swift 코드랑 연결해줘요)

- 컴포넌트들은 네이티브 모바일 UI 요소로 컴파일됩니다.

컴포넌트들은 실제로는 iOS와 Android가 각각 제공하는 네이티브 UI로 바뀌어 렌더링됩니다.

예를 들어,

```jsx
<Text>Hello</Text>
```

라고 작성하면, Android에선 `TextView`, iOS에선 `UILabel`로 바뀌어 실제 화면에 나타납니다.
즉, React 문법으로 진짜 네이티브 앱을 만들 수 있는 것이죠.

👉 즉, React Native는 `react-dom`과 비슷하게, 특정 플랫폼에 React를 연결해주는 역할을 합니다.

## 🔷 React.js + React Native = Real Native Mobile Apps

React Native를 사용하면, React의 문법을 기반으로 **iOS와 Android에서 실행 가능한 진짜 네이티브 앱**을 만들 수 있습니다.

## 🔷 그렇다면 "로직(Logic)"은 어떻게 처리될까?

React Native에서 UI는 네이티브로 컴파일되지만, 우리가 작성하는 **로직(Logic)** 부분은 다르게 처리됩니다.

React Native는 크게 **두 영역**으로 나뉩니다:

| 영역            | 설명                                                               |
| --------------- | ------------------------------------------------------------------ |
| **UI Elements** | 화면에 보이는 버튼, 텍스트 등 UI 구성 요소들                       |
| **Logic**       | 이벤트 처리, 상태 관리, API 호출 등 우리가 작성하는 앱의 동작 논리 |

### ✅ UI Elements

- React Native가 제공하는 컴포넌트들 (`<View>`, `<Text>`, `<Button>` 등)은  
  내부적으로 **각 플랫폼의 네이티브 UI 요소로 변환(compiled)** 됩니다.
- 예: `<Text>` → Android에선 `TextView`, iOS에선 `UILabel`로 변환됨

### ✅ Logic (앱 동작을 제어하는 코드)

- 우리가 직접 작성하는 **JavaScript 코드**입니다.
- 예를 들어, 버튼 클릭 시 상태 변경, API 요청 등
- 이 로직은 **앱 내의 JavaScript 스레드에서 실행되며, 컴파일되지 않습니다!**
- React Native가 내부적으로 이 JavaScript 스레드를 실행시켜 주는 구조입니다.

> 📌 **즉, UI는 네이티브로 컴파일되지만, 로직은 그대로 JS로 실행됩니다.**

---

### 🧠 정리

- React Native의 핵심 구조는 **"네이티브 UI + JavaScript 로직"** 조합입니다.
- **UI는 네이티브로 변환되고**,  
  **로직은 JS 스레드에서 실행되며 컴파일되지 않습니다.**
- 이 덕분에 우리는 React 문법으로 모바일 UI를 구성하고, JavaScript로 앱 로직을 제어할 수 있습니다.
