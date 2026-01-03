```jsx
const roughReact = (function () {
  const global = {};
  let index = 0;

  function useState(initialState) {
    if (!global.states) {
      /** 앱 전체의 states 배열을 초기화, 최초에는 빈 배열로 초기화 */
      global.states = [];
    }
    /**
     * states 배열을 조회하여 현재 상태 값이 있는지 확인하고
     * 없다면 인수로 받은 초기값(or undefined)을 할당
     */
    const currentState = global.states[index] || initialState;
    /* states의 값을 위에서 조회한 현재 값으로 업데이트 */
    global.states[index] = initialState;

    /* 즉시 실행함수로 setter를 만든다. */
    const setState = (function () {
      /**
       * 현재의 index를 클로저로 가두어 나중에도 동일한 index에 접근할 수 있도록 한다.
       */
      let currentIndex = index;

      return function (value) {
        console.log("??? > ", value);
        global.states[currentIndex] = value;
        /**
         * 컴포넌트를 렌더링하는 부분... (생략)
         */
      };
    })();

    /**
     * useState를 쓸 때마다 index를 하나씩 올린다. 이 index는 setState에서 사용한다.
     * 즉, 하나의 state마다 index가 할당되어 있어 그 index가 배열의 값(global.states)을 가리키고
     * 필요할 때마다 그 값을 가져오게 한다.
     */
    index += 1;

    return [currentState, setState];
  }

  // 사용처
  function ExampleComponent() {
    const [num, setNumber] = useState(1818);
    //...
  }
})();
```
