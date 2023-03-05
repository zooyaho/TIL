# 컴포넌트 등록 및 스타일링

## 전역 컴포넌트와 지역 컴포넌트

- createApp().compoment() 메서드로 추가한 자식컴포넌트들은 전역 컴포넌트로 애플리케이션이 처음 로드될 때 결국 vue가 컴포넌트 전부를 로드(브라우저가 처음에 모든 컴포넌트에 대한 코드를 다운로드 하는 것)하게 된다.
- 다양한 곳에서 사용하는 범용 컴포넌트의 경우 전역 컴포넌트로 등록한다.

### 지역 컴포넌트로 등록하는 법

- components프로퍼티를 사용한다.
- `components: {'html요소': 컴포넌트 포인터}` -> ex.`<the-header></the-header>` 셀프 클로징 태그 지원❌
- `components: {컴포넌트 포인터: 컴포넌트 포인터}` -> ex.`<TheHeader />` 셀프 클로징 태그로 작성할 수 있다.

## styling - scoped

- `<style> 태그`에 작성한 스타일링은 `스타일링을 추가한 위치와 관계없이 항상 앱 전체에 영향을 주는 전역 스타일로 취급된다.`
- 보통 App.vue에 글로벌 스타일링을 작성한다.
- 일잔적으로 스타일의 범위를 해당 컴포넌트로 지정해서 사용하는데 `<style>`태그에 `scoped 속성`을 추가한다. => 자식 컴포넌트, 형제 컴포넌트에도 적용되지 않는다.

## slots

- 코드를 여러 컴포넌트로 분할할 때 사용하는 옵션
- ⭐️ 자체 컴포넌트를 동적 콘텐츠, 즉 다른 HTML콘텐츠의 래퍼로 사용할 때 사용한다.

> card look(card component): 그림자와 모서리가 둥근 형태의 사각형 컴포넌트를 말함.

### 이름있는 slot

- `name`속성을 사용하여 래핑요소의 이름을 설정할 수 있다.
- 설정한 slot을 설정하기 위해 해당 슬롯에 들어갈 콘텐츠가 포함된 `template태그`에 `v-slot`디렉티브를 추가하여 Vue에 특정 콘텐츠가 어디로 가야할지 알려준다.
- template태그는 화면에 렌더링 되지 않는다.
- ⭐️ v-slot의 축약은 `#`을 사용한다.

### slot스타일

- slot을 설정한 컴포넌트는 template에 작성된 요소에만 스타일이 적용된다. 만약 header관련 스타일을 설정하고 header를 slot을 통해 추가하였다면 해당 스타일이 적용되지 않는다. 그 이유는 `vue가 다른 컴포넌트에 콘텐츠를 보내기 전에 템플릿을 분석하고 컴파일하고 평가하기 때문이다.`

👾 예제

● BaseCard.vue - 래퍼 컴포넌트

```vue
<template>
  <div>
    <header>
      <slot name="header"></slot>
    </header>
    <slot></slot>
  </div>
</template>
<style scoped>
div {
  margin: 2rem auto;
  max-width: 30rem;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 1rem;
}
</style>
```

● UserInfo.vue

```vue
<template>
  <section>
    <base-card>
      // 래퍼로 사용
      <template v-slot:header>
        // 이름있는 slot사용
        <h3>{{ fullName }}</h3>
        <base-badge :type="role" :caption="role.toUpperCase()"></base-badge>
      </template>
      <p>{{ infoText }}</p>
    </base-card>
  </section>
</template>

<script>
export default {
  props: ["fullName", "infoText", "role"],
};
</script>

<style scoped>
section header {
  /* header가 래퍼 객체에 있어 해당 스타일은 적용되지 않는다. */
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
```

### $slots

- Vue에서 제공하는 `내장 프로퍼티`로 컴포넌트가 다른 슬롯에 대해 `수신하는 슬롯 데이터에 관한 정보`를 공유한다.
- 위에서 예시를 참조하면 $slots.header를 사용하여 header슬롯을 사용하지 않는 컴포넌트에서는 `undefined`를 반환한다.
- 내장 프로퍼티를 사용하여 사용하지 않는 슬롯에 해당 태그가 아예 렌더링 되지 않게 할 수 있다.

👾 예제

● BaseCard.vue - 래퍼 컴포넌트

```vue
<template>
  <div>
    <header v-if="$slots.header">
      // header 슬롯을 사용할 경우에만 header태그가 렌더링된다.
      <slot name="header"></slot>
    </header>
    <slot></slot>
  </div>
</template>
```

### 범위가 지정된(scoped) slot

- 다른 사람, 동료와 공유하고 싶은 컴포넌트를 구축하거나 큰 프로젝트에서 다른 방식으로 사용되는 컴포넌트를 구축할 때, 어떤 컴포넌트의 요소를 사용자가 지정할 수 있게 만들 때 사용한다.
- ⭐️ 범위가 지정된 slot이란, `슬롯을 정의한 컴포넌트 내부에서 슬롯에 대한 마크업을 전달한 컴포넌트에 데이터를 전달할 수 있게 한다.`

👾 예제

● NameList.vue - 자식 컴포넌트

```vue
<template>
  <ul>
    <li v-for="(name, idx) in names" :key="idx">
      <slot :item="name" :idx="idx"></slot>
    </li>
  </ul>
</template>
<script>
export default {
  data() {
    return {
      names: ["mark", "joon"],
    };
  },
};
</script>
```

● App.vue - 부모 컴포넌트

```vue
<template>
  <name-list>
    <!-- default은 기본값이며 name slot을 지정해서 사용할 수 도 있다. -->
    <template #default="slotProps">
      <!-- slotProps은 NameList를 나타내며 객체 형태이다. -->
      <h2>
        {{ slotProps.item }}
        <strong>{{ slotProps.idx }}</strong>
      </h2>
    </template>
  </name-list>
</template>
```

![](https://velog.velcdn.com/images/zooyaho/post/84ecace2-9b0d-45a7-a836-4ee812d0c22a/image.png)
