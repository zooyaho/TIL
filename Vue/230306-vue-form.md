# v-model

- @input="", value=""의 축약어
- `<input type="number" v-model="age" ref="refAge"/>`일때 console로 찍어보면 입력값이 age는 `숫자 타입` refAge는 `문자 타입`으로 찍히는데 이는 v-model이 자동으로 타입을 확인한 후 바꾸기 때문이다.
- 수동으로 바꾸는 방법은 `v-model.number=""`등 프로퍼티를 사용한다.
- 다양한 수식어가 있는데 `lazy 수식어`는 바인딩한 프로퍼티를 업데이트하는 방법을 바꿀 수 있다. 낮은 빈도로 값을 업데이트 한다.
- `trim`수식어는 입력된 텍스트의 앞뒤의 공백을 제거한다.
- `select요소`에서도 사용할 수 있다. option의 value값이 v-model에 지정한 data프로퍼티에 할당 된다.
- `체크박스`에서 input이 여러개일 경우 각각에 value요소를 추가하여 개별적인 값을 설정하고 선택된 값들은 v-model에 바인딩한 데이터 프러퍼티에 추가된다. 이때 값이 여러개일 경우 데이터 프러퍼티는 배열 타입이어야하고, 한개일 경우에는 boolean등을 초기값으로 설정하여 사용할 수 있다.
- `라디오`에서도 각각에 value요소를 추가하여 개별적인 값을 설정해야 동작이 정상적으로 된다.

## 커스텀 컴포넌트에 v-model사용하기

- 커스텀 컴포넌트에 v-model을 사용하면 vue는 특정한 프로퍼티를 설정한다.
- 커스텀 컴포넌트에서 발생시키는 특정한 이벤트를 수신하게 된다.
- v-model을 사용하면 내부적으로 `value와 커스텀 이벤트를 수동으로 바인딩`한다.

👾 예제

● TheForm.vue - 부모 컴포넌트

```vue
<template>
  <div class="form-control">
    <!-- <RatingControl v-model="rating" :model-value="" @update:model-value=""/> -->
    <RatingControl v-model="rating" />
  </div>
</template>

<script>
export default {
  components: {
    RatingControl,
  },
  data() {
    return {
      rating: null,
    };
  },
};
</script>
```

● RatingControl.vue - 커스텀 컴포넌트

- 버튼을 클릭하면 해당 버튼 활성화, 그리고 해당 버튼의 값이 부모 컴포넌트에 전달됨.

```vue
<template>
  <ul>
    <li :class="{ active: modelValue === 'poor' }">
      <button type="button" @click="activate('poor')">Poor</button>
    </li>
    <li :class="{ active: modelValue === 'average' }">
      <button type="button" @click="activate('average')">Average</button>
    </li>
    <li :class="{ active: modelValue === 'great' }">
      <button type="button" @click="activate('great')">Great</button>
    </li>
  </ul>
</template>

<script>
export default {
  // v-model을 사용하게 되면 자동으로 해당 프롭과 이벤트 함수를 받게 된다.
  props: ["modelValue"],
  emits: ["update:modelValue"], // modelValue를 업데이트 시키는 이벤트 함수
  methods: {
    activate(option) {
      // string
      // 새옵션을 선택할 때마다 이 이벤트를 발생시킴.
      this.$emit("update:modelValue", option);
    },
  },
};
</script>
```
