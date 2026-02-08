## 3. 리액트 훅 깊게 살펴 보기 ( 목차 + 28P )

📝 💡

### 📝 3.1 리액트의 모든 훅 파헤치기

**💡`useState`**

- 함수 컴포넌트 내부에서 상태를 정의하고, 관리할 수 있게 해주는 훅

```jsx
import { useState } from "react";
const [state, setState] = useState(initialValue);
```

> - **`useState`훅의 결괏값은 어떻게 함수가 재실행 되어도 그 값을 유지하고 있는가??** > <br/> > <a href="./Extra/RoughImplOfUseState.md"> useState의 대략적인 구현 </a> <br/>
>   실제 리액트 코드에서는 `useReducer` 훅을 이용해 구현되어 있다.
>   함수의 실행이 끝났음에도 함수가 선언된 환경을 기억할 수 있는 방법은 `클로저`를 이용하는 것이다.
>   <br/> (예제에서 `roughReact` 컨텍스트 안에 `global`과 `index`변수를 선언하여 `useState`함수 내부에서 참조 및 수정)

**💡게으른 초기화**

- `useState`훅의 인수로 특정한 값을 반환하는 함수를 넘길 수 있는데, 이를 `게으른 초기화`라고 부른다.

```jsx
/* 일반적인 useState 사용 */
const [count, setCount] = useState(
  Number.parseInt(window.localStorage.getItem(cacheKey))
);

/* 게으른 초기화 */
const [count, setCount] = useState(() =>
  Number.parseInt(window.localStorage.getItem(cacheKey))
);
```

- 리액트 공식 문서에서 이러한 `게으른 초기화`는 `useState`의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용하라고 되어 있다.
- 이 `게으른 초기화`함수는 오로지 state가 처음 만들어질 때만 사용된다. 만약 이후에 리렌더링이 발생한다면 이 함수의 실행은 무시된다.

**💡💡💡`useEffect`**

```jsx
/* [ 사용법 ] */
useEffect(() => {
  /*callBack Function*/
}, [dependency]);

/* clean Up 함수 */
useEffect(() => {
  /*callBack Function*/

  return () => {
    /* clean up Function */
  };
}, [dependency]);
```

- 첫 번째 인수로는 콜백 함수, 두 번째 인수로는 의존성 배열을 받는다.
- 의존성 배열에 할당한 값이 변경 되면 첫 번째 인수로 받은 콜백 함수를 실행한다.
- 의존성 배열을 빈 배열로 두면 콜백 함수를 컴포넌트가 마운트(생성)될 때만 실행한다.
- 콜백 함수 내에서 return 이후로 작성하는 클린업 함수를 작성하면 컴포넌트가 언마운트(삭제) 될 때 실행된다.

> **`어떻게 의존성 배열이 변경된 것을 알고 실행 되는가?`**
>
> 함수 컴포넌트는 렌더링 시마다 고유의 state와 props 값을 가지고 있다.
> useEffect는 렌더링 시마다 의존성 배열에 있는 값을 보면서 이 의존성 배열의 값이 이전과 다른 것이 있다면 부수 효과를 실행하는 평범한 함수라고 볼 수 있다.
> 따라서 useEffect는 state와 props의 변화 속에서 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라고 볼 수 있다.

- <a href="./Extra/CounterExample.md"> Counter 예제 </a>
- 클린업 함수는 이전 state를 참조하여 실행된다!
- `useEffect`는 그 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행한다.
  <br/> `위의 Counter 예제에서 increment 버튼 클릭을 통해 counter 상태가 변경되어 리렌더링이 발생할 때 로그를 보면 클린업 함수가 먼저 실행되며 이전 상태 값을 찍고`
  <br/> `이후 useEffect에 첫 번째 인수로 전달된 함수가 실행되며 현재 상태 값이 찍힌다.`
  <br/> 새로운 상태 값을 기반으로 렌더링된 뒤에 실행되지만 변경된 새 상태 값을 읽는 것이 아니라 함수가 정의되었을 당시에 선언됐던 이전 값을 보고 실행된다.
- 생명주기 메서드의 언마운트 개념과는 차이가 있는 것이, 언마운트는 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하지만 , 클린업 함수는 언마운트보다는 함수 컴포넌트가 리렌더링 되었을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것이 옳다.

**💡`useEffect`훅의 의존성 배열**

- 빈 배열을 둔다면 비교할 의존성이 없다고 판단하여 최초 렌더링 직후에는 더 이상 실행되지 않는다.
- 아예 두번째 인수를 넘기지 않는다면 의존성을 비교할 필요없이 렌더링마다 실행이 필요하다고 판단하여 렌더링이 발생할 때마다 실행된다.
  > - **`그렇다면 그냥 함수 컴포넌트의 몸체에서 실행되는 코드와의 차이점은 무엇인가?`** > <br/> -> `useEffect` 는 클라이언트 사이드에서 실행되는 것을 보장해준다. `useEffect`에서는 window 전역 객체에 접근하는 코드를 사용해도 무방하다.
  >   <br/> -> `useEffect` 는 컴포넌트 렌더링의 부수 효과, 즉 컴포넌트의 렌더링이 완료된 이후에 실행된다. 이와 반대로 함수 몸체에서 실행되는 코드는 컴포넌트가 렌더링 되는 중간에 실행된다.
  >   따라서 SSR인 경우에는 useEffect 에서 실행되는 코드와 달리 서버에서 실행된다. 이러한 작업은 함수 컴포넌트의 jsx element 반환을 지연하는 행위이며 성능에 악영향을 줄 수 있다.

**💡`useEffect` 사용시 주의할점**

- `useEffect`의 콜백 내부에서는 최대한 의존성 배열에 전달된 값만을 사용하여야 한다..는 의견
- `useEffect`의 콜백을 기명 함수로 작성하는 방법도 있다..는 의견
- `useEffect`의 콜백을 간단하게 유지하라는..의견

**💡`useMemo`**

```jsx
/* [ 사용법 ] */
useMemo(() => expensiveComputation(a, b), [a, b]);
```

- 비용이 큰 연산에 대한 결과를 저장(메모이제이션) 해두고 , 이 저장된 값을 반환하는 훅이다.
- 첫 번째 인수로는 어떠한 값을 반환하는 생성 함수를, 두 번째 인수로는 해당 함수가 의존하는 값의 배열을 전달한다.
- 렌더링시 의존성 배열의 값이 변경되지 않았다면 이전에 기억해 둔 값을 반환, 변경이 있다면 첫 번째 인수로 전달 받은 함수를 실행하여 값을 반환한다. (컴포넌트도 가능)

**💡`useCallback`**

```jsx
/* [사용법] */
useCallback(() => {
  /* memorize */
}, [dependency]);
```

- 인수로 넘겨 받은 콜백 함수 자체를 기억한다. 특정 함수를 새로 만들지 않고 재사용한다는 의미 , 의존성 배열 값이 변경되면 첫 번째 인수로 받은 함수를 재생성한다.
- useMemo 와의 차이는 메모이제이션 대상이 변수냐 함수냐의 차이다.
  <br/>`예제를 보면 useMemo에서는 함수의 '실행'을 넘겼고, useCallback 에서는 함수의 '선언'을 넘겼다.`

**💡`useRef`**

```jsx
 /* [사용법] */

// 일반적인 값을 사용할 때
 const count = useRef(0)

 // DOM 요소에 접근 할 때
 const inputRef = useRef(null)  -> <input type="button" ref = { inputRef } />

 if(inputRef.current) inputRef.current.style.display = "block";
```

- useState와 동일하게 컴포넌트 내부에서 렌더링이 일어나도 변경 가능한 상태값을 저장한다.
- 반환값 객체의 current 프로퍼티로 값에 접근 또는 변경할 수 있다.
- useRef 의 값이 변하더라고 렌더링을 발생시키지 않는다.
- useRef 의 최초 값은 인수로 넘겨 받은 값이다. ( DOM ref는 컴포넌트가 렌더링 되기 이전에 선언되었기 때문에 값은 null 인 상태)

> **`렌더링에 영향을 미치지 않는 고정값이라는 목적을 위해서라면, 함수 컴포넌트의 선언 밖에서 변수를 선언하여 관리하는 것과 차이가 없지 않을까?`** ><br/> -> 컴포넌트 렌더링 이전에도 변수가 존재하게되어 메모리 낭비가 발생한다.
> <br/> -> 만약 컴포넌트가 여러개 생성된다면 각 컴포넌트에서 가리키는 값이 하나가 된다.
> <br/> (일반적으로 각 컴포넌트마다 다른 고정 값이 필요한 경우가 많기 때문에 좋지 않다.)

**💡`useContext`**

- `useContext`에 대해 이해하려면 먼저 리액트의 `Context`에 대해 알아야 한다.

**Context?**

- `props drilling` 없이 컴포넌트 트리의 여러 단계에 걸쳐 `공유 상태`를 `공급(Provider)`하고 `소비(Consumer/useContext)`하게 해주는 메커니즘

**Context를 함수 컴포넌트에서 사용할 수 있게 해주는 useContext 훅**

- 예제

```tsx
const Context = createContext<{ hello: string } | undefined>(undefined);

function ParentComponent() {
  return (
    <>
      <Context.Provider value={{ hello: "react" }}>
        <Context.Provider value={{ hello: "javascript" }}>
          <ChildComponent />
        </Context.Provider>
      </Context.Provider>
    </>
  );
}

function ChildComponent() {
  const value = useContext(Context);
  // value.hello 의 값은 "javascript"가 된다.
  return <>{value ? value.hello : ""}</>;
}
```

- 상위 컴포넌트에서 만들어진 `Context`를 사용할 수 있도록 만들어진 훅
- 만약 여러개의 `Provider`가 존재한다면 가장 가까운 `Provider`의 값을 가져오게 된다.
- `Context`가 초기화 되어 있는지 가드하기 위해 커스텀 훅으로 감싼 예제

  ```tsx
  function useMyContext() {
    const context = useContext(MyContext);
    if (context === undefined) {
      throw new Error(
        "useMyContext는 ContextProvider 내부에서만 사용할 수 있습니다."
      );
    }
    return context;
  }

  function ChildComponent() {
    const { hello } = useMyContext();
    // "javascript"
    return <>{hello}</>;
  }
  ```

**💡`useContext` 사용시 주의할점**

- `useContext`가 함수 컴포넌트 내부에서 사용된다면 `Provider`에 의존성을 가지게 되므로 아무곳이나 재활용하기 어려운 컴포넌트가 된다.
- 따라서 `useContext`를 사용하는 컴포넌트를 최대한 작게 하거나 혹은 재사용 되지 않을 만한 컴포넌트에서 사용해야 한다.
- 일부 리액트 개발자들이 `Context`와 `useContext`를 상태 관리를 위한 리액트 API라고 오해하고 있는데, 엄밀히 따지면 `Context`는 상태를 주입해 주는 API다. 상태 관리 라이브러리가 되기 위해서는 최소한 다음 2가지 조건을 만족해야 한다.
  - 어떠한 상태를 기반으로 다른 상태를 만들어 낼 수 있어야 함
  - 필요에 따라 이러한 상태 변화를 최적화 할 수 있어야 함
- 그러나 `Context`는 둘 중 어느것도 하지 못한다.

**💡💡💡`useReducer`**

- `useState`와 비슷한 형태를 띠지만 좀 더 복잡한 상태값을 미리 정의해 놓은 시나리오에 따라 관리할 수 있다.
  - 반환값은 `useState`와 동일하게 길이가 2인 배열이다. `[ state, dispatcher ]`
  - 두 번째요소 `dispatcher`는 `state`를 업데이트하는 함수지만 `state`를 변경할 수 있는 `action`을 넘겨준다는 것이 차이점이다.
  - `useState`와 달리 2개에서 3개의 인수를 필요로 한다.
    - `reducer`: 기본 `action`을 정의하는 함수다. 첫 번째 인수
    - `initialState`: 초깃값을 의미, 두번째 인수
    - `init`: `useState`의 인수로 함수를 넘길때처럼 `게으른 초기화`를 하고 싶을 때 사용하는 함수이다. 필수값이 아니며, 만약 여기에 인수로 넘겨주는 함수가 존재한다면 두번째 인수 `initialState`를 인수로 하여 `init`으로 전달된 함수가 실행된다.
- <a href="./Extra/UsageExampleOfUseReducer.md">사용예제</a>

> **`useState`와 `useReducer`는 실제로 어떤 차이가 있을까?**
>
> - 둘 다 세부 동작과 쓰임에만 차이가 있을 뿐, 결국 클로저를 활용해 값을 가두어 state를 관리한다는 사실에는 변함이 없다.

**💡`useImperativeHandle`**

- `useImperativeHandle`을 이해하기 위해서는 먼저 `React.forwardRef`에 대해 알아야 한다.
- 부모에게서 넘겨받은 `ref`를 원하는 대로 수정할 수 있는 훅이다.

```jsx
const input = forwardRef((props, ref) => {
  // useImperativeHandle을 사용하면 ref의 동작을 추가로 정의할 수 있다. 
  useImperativeHandle(
      ref,
      () => ({
        alert: () => alert(props.value),  
      }),
  // useEffect의 deps와 같다
  []);
  
  return <input ref={ref} {...props}/>
});

function App() {
  // input에 사용할 ref
  const inputRef = useRef();
  // input의 value
  const [text, setText] = useState("");
  
  const handleClick = () => {
    // inputRef에 추가로 정의한 alert라는 동작을 사용할 수 있다.
    inputRef.current.alert();
  }
  
  const handleChange = (e) => {
    setText(cur => e.target.value);
  }
  
  return (
      <>
        <Input ref={inputRef} value={text} onChange={handleChange}/>
        <button onClick={handleClick}>Focus</button>
      </>
  )
}
```
- 여기서는 전달 받은 `ref`에다 `useImperative`훅을 이용해 추가적인 동작을 정의했다. 이로써 부모는 단순히 `HTMLElement`뿐만 아니라 자식 컴포넌트에서 새롭게 설정한 객체의 키와 값에 대해서도 접근할 수 있게 되었다.
- `useImperative`훅을 이용하면 이 `ref`값에 원하는 값이나 액션을 정의할 수 있다.

**💡`useLayoutEffect`**
- `이 함수의 시그니처는 useEffect와 동일하나, 모든 DOM의 변경 후에 동기적으로 발생한다.`
- 여기서 중요한 사실은 `모든 DOM의 변경 후에 useLayoutEffect의 콜백 함수 실행이 동기적으로 발생`한다는 점이다.
- 여기서 말하는 `DOM 변경`이란 렌더링이지, 실제로 브라우저에 해당 변경 사항이 반영되는 시점을 의미하는 것이 아니다.
- 즉, 실행 순서는 다음과 같다.

1. React가 DOM을 업데이트
2. useLayoutEffect를 실행
3. 브라우저에 변경 사항을 반영
4. useEffect를 실행

- `동기적으로 발생한다는 것은 리액트는 useLayoutEffect의 실행이 종료될 때까지 기다린 다음에 화면을 그린다는 의미이다.`
- useLayoutEffect의 특징상 `DOM은 계산되었지만 이것이 화면에 반영되기 전에 하고 싶은 작업이 있을 때` <br/>
  (Ex. DOM요소를 기반으로 한 애니메이션, 스크롤 위치 제어)

**💡`useDebugValue`**
- 디버깅하고 싶은 정보를 이 훅에다 사용하면 리액트 개발자 도구에서 볼 수 있다.

**💡`훅의 규칙`**
1. 최상위에서만 훅을 호출해야 한다. 반복문이나 조건문, 중첩된 함수 내에서 훅을 실행할 수 없다. <br/>
   이 순서를 따라야만 컴포넌트가 렌더링될 때마다 항상 동일한 순서로 훅이 호출되는 것을 보장할 수 있다.
2. 훅을 호출할 수 있는 것은 `리액트 함수 컴포넌트, 혹은 사용자 정의 훅`의 두 가지 경우 뿐이다. 일반 자바스크립트 함수에서는 훅을 사용할 수 없다.

- `useState`의 구현에서 본 것처럼 훅에 대한 정보 저장은 리액트 어딘가에 있는 index와 같은 키를 기반으로 구현되어 있다. <br/>
  (실제로는 객체 기반 연결리스트에 더 가깝다.)
- `즉, useState나 useEffect는 모두 순서에 아주 큰 영향을 받는다.`

## 더 알아보기

#### 💥 `useEffect` 콜백의 내부에서 참조하는 모든 `reactive value`는 의존성 배열에 들어가야한다?

- ///
