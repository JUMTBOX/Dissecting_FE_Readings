```tsx
type State = {
  count: number;
};

type Action = {
  type: "up" | "down" | "reset";
  payload?: State;
};

// 무거운 연산이 포함된 `게으른 초기화` 함수
function init(count: State) {
  return count;
}

// 초깃값
const initialState: State = { count: 0 };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "up":
      return { count: state.count + 1 };
    case "down":
      return { count: state.count - 1 > 0 ? state.count - 1 : 0 };
    case "reset":
      return init(action.payload || { count: 0 });
    default:
      throw new Error(`Unexcpected action type ${action.payload}`);
  }
}

export default function App() {
  const [state, dispatcher] = useReducer(reducer, initialState, init);

  const handleUpBtnClick = () => {
    dispatcher({ type: "up" });
  };

  const handlerDownBtnClick = () => {
    dispatcher({ type: "down" });
  };

  const handleResetBtnClick = () => {
    dispatcher({ type: "reset", payload: { count: 1 } });
  };

  return (
    <div>
      <h1>{state.count}</h1>
      <button onClick={handleUpBtnClick}> Up </button>
      <button onClick={handlerDownBtnClick}> Down </button>
      <button onClick={handleResetBtnClick}> Reset </button>
    </div>
  );
}
```
