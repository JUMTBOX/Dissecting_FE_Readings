## 3. 리액트 훅 깊게 살펴 보기 ( 목차 + 28P )

📝 💡

### 📝 3.1 리액트의 모든 훅 파헤치기

**💡`useState`**

- 함수 컴포넌트 내부에서 상태를 정의하고, 관리할 수 있게 해주는 훅

```jsx
import { useState } from "react";
const [state, setState] = useState(initialValue);
```

> - **`useState`훅의 결괏값은 어떻게 함수가 재실행 되어도 그 값을 유지하고 있는가??**
> <br/> 
> <a href="./Extra/RoughImplOfUseState.md"> useState의 대략적인 구현 </a> <br/>
> 실제 리액트 코드에서는 `useReducer` 훅을 이용해 구현되어 있다.
> 함수의 실행이 끝났음에도 함수가 선언된 환경을 기억할 수 있는 방법은 `클로저`를 이용하는 것이다.
  <br/> (예제에서 `roughReact` 컨텍스트 안에 `global`과 `index`변수를 선언하여 `useState`함수 내부에서 참조 및 수정)  

**💡게으른 초기화**
- `useState`훅의 인수로 특정한 값을 반환하는 함수를 넘길 수 있는데, 이를 `게으른 초기화`라고 부른다. 
```jsx
/* 일반적인 useState 사용 */
const [count, setCount] = useState( Number.parseInt( window.localStorage.getItem(cacheKey) ) );

/* 게으른 초기화 */
const [count, setCount] = useState( () => Number.parseInt( window.localStorage.getItem(cacheKey) ) );
```
- 리액트 공식 문서에서 이러한 `게으른 초기화`는 `useState`의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용하라고 되어 있다.
- 이 `게으른 초기화`함수는 오로지 state가 처음 만들어질 때만 사용된다. 만약 이후에 리렌더링이 발생한다면 이 함수의 실행은 무시된다.

**💡`useEffect`**
```jsx
/* [ 사용법 ] */
 useEffect(( ) => { /*callBack Function*/ } , [ dependency ]);

/* clean Up 함수 */
useEffect(() => {/*callBack Function*/ 
	
  return () => {/* clean up Function */}
}, [dependency]);
```
- 첫 번째 인수로는 콜백 함수, 두 번째 인수로는 의존성 배열을 받는다.
- 의존성 배열에 할당한 값이 변경 되면 첫 번째 인수로 받은 콜백 함수를 실행한다.
- 의존성 배열을 빈 배열로 두면 콜백 함수를 컴포넌트가 마운트(생성)될 때만 실행한다.
- 콜백 함수 내에서 return 이후로 작성하는 클린업 함수를 작성하면 컴포넌트가 언마운트(삭제) 될 때 실행된다.

>   **어떻게 의존성 배열이 변경된 것을 알고 실행 되는가?**
>   
>   함수 컴포넌트는 렌더링 시마다 고유의 state와 props 값을 가지고 있다. 
>   useEffect는 렌더링 시마다 의존성 배열에 있는 값을 보면서 이 의존성 배열의 값이 이전과 다른 것이 있다면 부수 효과를 실행하는 평범한 함수라고 볼 수 있다.
>   따라서 useEffect는 state와 props의 변화 속에서 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라고 볼 수 있다.

- <a href="./Extra/CounterExample.md"> Counter 예제 </a>
- 클린업 함수는 이전 state를 참조하여 실행된다! 
- `useEffect`는 그 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행한다.
 <br/> `위의 Counter 예제에서 increment 버튼 클릭을 통해 counter 상태가 변경되어 리렌더링이 발생할 때 로그를 보면 클린업 함수가 먼저 실행되며 이전 상태 값을 찍고`
 <br/> `이후 useEffect에 첫 번째 인수로 전달된 함수가 실행되며 현재 상태 값이 찍힌다.`
 <br/> 새로운 상태 값을 기반으로 렌더링된 뒤에 실행되지만 변경된 새 상태 값을 읽는 것이 아니라 함수가 정의되었을 당시에 선언됐던 이전 값을 보고 실행된다.
- 생명주기 메서드의 언마운트 개념과는 차이가 있는 것이, 언마운트는 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하지만 , 클린업 함수는 언마운트보다는 함수 컴포넌트가 리렌더링 되었을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것이 옳다.

**💡`useEffect`훅의 의존성 배열**