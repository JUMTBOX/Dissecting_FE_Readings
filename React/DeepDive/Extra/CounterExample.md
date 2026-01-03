```jsx
import React, { useEffect, useState } from "react";

export default function Counter() {
  const [counter, setCounter] = useState(0);

  function handleClick() {
    setCounter((prev) => prev + 1);
  }

  useEffect(() => {
    console.info(`===== COUNT: ${counter} =====`);

    return () => {
      console.info(`===== 클린업 함수 내부 COUNT: ${counter} =====`);
    };
  }, [counter]);

  return (
    <h1> {counter} </h1>
    <div>
      <button onClick={handleClick}> increment </button>
    </div>
  );
}
```