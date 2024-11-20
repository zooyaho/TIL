## 트리 셰이킹(Tree Shaking)란?

> 트리 셰이킹은 **사용되지 않는(dead code) 코드를 제거하여 최종 번들 크기를 줄이는 최적화 기법**입니다. JavaScript 번들러(예: Webpack, Rollup, esbuild 등)가 코드의 종속성(Dependency)을 분석하여 실제로 사용되는 코드만 남기고 나머지를 제거합니다.

### 트리 셰이킹의 주요 특징

1. **코드 크기 최적화**: 불필요한 코드를 제거해 번들 크기를 줄임.
2. **ES Module 기반**: `import/export` 구문으로 작성된 모듈에서 정적 분석이 가능해야 작동.
3. **부작용 없는 코드**: 코드가 부작용(Side Effects)을 발생시키지 않아야 제거 가능.

<hr>

## 트리 셰이킹의 작동 원리

트리 셰이킹은 **ES Module(ESM)**의 **정적 분석**을 기반으로 작동합니다.

- 정적 분석이란?
  코드를 실행하지 않고 소스 코드를 읽어서, 모듈 간 종속성과 사용 여부를 분석하는 과정.

### 예제

**코드 작성**

```js
// math.ts
export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}

// main.ts
import { add } from "./math";

console.log(add(2, 3)); // subtract는 사용되지 않음
```

**번들링 후 결과 (트리 셰이킹 적용)**

```js
// 번들링된 main.js
function add(a, b) {
  return a + b;
}
console.log(add(2, 3));
```

- `subtract` 함수는 사용되지 않으므로 제거됩니다.
- 결과적으로 불필요한 코드가 번들에서 제외되어 파일 크기가 줄어듭니다.

<hr>

## TypeScript `enum`이 트리 셰이킹에 미치는 영향

`enum`은 TypeScript에서 런타임 객체로 변환되기 때문에 트리 셰이킹이 제대로 작동하지 않을 수 있습니다.

### `enum`의 문제점

1. 런타임 객체 생성: `enum`은 컴파일된 JavaScript 코드에서 객체로 변환됩니다.
2. 전체 객체 유지: `enum`의 일부 값만 사용하더라도 객체 전체가 번들에 포함됩니다.
3. 역매핑 지원: 숫자 기반 `enum`의 역매핑(숫자 <-> 이름) 기능이 추가적인 코드 생성을 유발합니다.

**예제: `enum` 사용**

```typescript
// priority.ts
export enum Priority {
  Low = "low",
  Medium = "medium",
  High = "high",
}

// main.ts
import { Priority } from "./priority";

console.log(Priority.Low);
```

**컴파일된 JavaScript 코드**

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
exports.Priority = void 0;
var Priority;
(function (Priority) {
  Priority["Low"] = "low";
  Priority["Medium"] = "medium";
  Priority["High"] = "high";
})(Priority || (Priority = {}));
console.log(Priority.Low);
```

- `Priority`는 런타임 객체로 존재하므로, 번들러는 객체 전체를 남겨둡니다.
- 사용되지 않은 `Priority.Medium`과 `Priority.High`도 번들에 포함됩니다.

<hr>

## `const` 객체와 `type`을 사용한 최적화

런타임 객체가 필요하지 않은 경우, `const` 객체와 `type` 조합을 사용하는 것이 더 효율적입니다.

**`const` 객체 + `type` 방식**

```typescript
// priority.ts
export const PRIORITY = {
  LOW: "low",
  MEDIUM: "medium",
  HIGH: "high",
} as const;

export type Priority = (typeof PRIORITY)[keyof typeof PRIORITY];

// main.ts
import { PRIORITY } from "./priority";

console.log(PRIORITY.LOW);
```

**컴파일된 JavaScript 코드**

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
exports.PRIORITY = void 0;
exports.PRIORITY = {
  LOW: "low",
  MEDIUM: "medium",
  HIGH: "high",
};
console.log(exports.PRIORITY.LOW);
```

- `PRIORITY`는 정적 객체로 남음.
- 사용되지 않는 값(`MEDIUM`, `HIGH`)은 트리 셰이킹 과정에서 제거될 수 있습니다.

<hr>

## 트리 셰이킹을 위한 베스트 프랙티스

1. **ES Module(ESM) 사용**

- `import/export` 구문으로 작성하여 번들러가 정적 분석할 수 있도록 합니다.

2. **런타임 객체가 필요하지 않은 경우 `const` 객체 + `type` 사용**

- 단순한 상수 값 집합은 enum 대신 const와 type을 사용합니다.

3. **부작용(Side Effects)이 없는 코드 작성**

- 전역 상태를 변경하거나 예기치 않은 동작을 유발하는 코드는 트리 셰이킹 대상에서 제외됩니다.

4. **최신 번들러 사용**

- Webpack, Rollup, esbuild와 같은 최신 번들러를 사용하고 트리 셰이킹 옵션을 활성화합니다.

<hr>

## 결론

- `enum`은 런타임 객체로 변환되므로, 트리 셰이킹이 제대로 작동하지 않을 수 있습니다.
- 런타임 객체가 필요하지 않은 단순한 상수 값 집합에는 **`const` 객체와 `type` 조합을 사용하는 것이 더 가볍고 효율적**입니다.
- 트리 셰이킹을 최대한 활용하려면 코드의 구조를 정적 분석이 가능하도록 설계하고, 부작용을 최소화하는 것이 중요합니다.
  > **Tip**: 현대적인 TypeScript 개발에서는 트리 셰이킹과 코드 최적화를 위해 `const` 객체와 `type` 조합이 점점 더 많이 사용되고 있습니다!
