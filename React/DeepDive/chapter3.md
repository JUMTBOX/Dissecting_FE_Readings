## 3. 리액트 훅 깊게 살펴 보기 ( 목차 + 28P )

📝 💡

### 📝 3.1 리액트의 모든 훅 파헤치기

**💡`useState`**

- 함수 컴포넌트 내부에서 상태를 정의하고, 관리할 수 있게 해주는 훅

```jsx
import { useState } from "react";
const [state, setState] = useState(initialValue);
```

- `useState`훅의 결괏값은 어떻게 함수가 재실행 되어도 그 값을 유지하고 있는가??
- <a href="./Extra/RoughImplOfUseState.md"> useState의 대략적인 구현 </a>
- 실제 리액트 코드에서는 `useReducer` 훅을 이용해 구현되어 있다.
- 함수의 실행이 끝났음에도 함수가 선언된 환경을 기억할 수 있는 방법은 `클로저`를 이용하는 것이다.
  <br/> (예제에서 `roughReact` 컨텍스트 안에 `global`과 `index`변수를 선언하여 `useState`함수 내부에서 참조 및 수정)  

**💡게으른 초기화**
