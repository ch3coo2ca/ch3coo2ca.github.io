---
layout: post
title: "useRef의 3가지 정의와 타입 알아보기 (feat. MutableRefObject, RefObject)"
description : "useRef의 타입 MutableRefObject, RefObject에 대해서 알아보자."
tags: [React, TypeScript]
---

타입스크립트에서 useRef를 사용하다 보면 2가지 타입을 마주할 수 있는데, `MutableRefObject`와 `RefObject`의 차이에 대해서 알아보고 각 타입을 언제 활용할 수 있는지 알아보자. 

<br/>

## useRef 란?

useRef는 리액트 훅의 하나로, 인자로 받은 값을 ref 객체의 `.current` 프로퍼티에 저장한다. 주로 DOM을 참조해 직접적으로 엘레먼트를 제어하거나, 컴포넌트 라이프사이클 동안 특정 값을 저장해 리렌더링이 필요하지 않은 경우에 로컬 변수용으로도 사용된다. 

useRef는 2가지 리턴 타입을 가지고 있는데 `MutableRefObject`와 `RefObject`의 차이는 다음과 같다. 

`MutableRefObject`는 이름에서도 알 수 있듯이 current 의 값이 **변경 가능한 객체**이고, `RefObject`는 current 프로퍼티의 값이 **readonly로 값을 변경할 수 없다**. 

```tsx
interface MutableRefObject<T> {
    current: T;
}

interface RefObject<T> {
    readonly current: T | null;
}
```
<br/>

## useRef 3가지 정의

useRef는 초깃값으로 넘어온 인자의 타입에 따라서 정의를 3가지로 나눌 수 있다.  특히 개발할 때  ref 타입을 제대로 알지 못하면 많은 에러를 마주할 수 있는데, 예시와 함께 각각의 사용 경우에 대해서 알아보자. 

### useRef<T>(initialValue: T): MutableRefObject<T>

제네릭 타입 T와 초깃값이 T로 일치하는 경우, 리턴 타입은 `MutableRefObject<T>` 가 된다.
ref 객체의 **`.current` 프로퍼티를 직접 변경**할 수 있다. 

<br/>

### useRef<T>(initialValue: T | null): RefObject<T>

초깃값이 null인 경우, 리턴 타입은 `RefObject<T>` 로 ref 객체의 **`.current` 프로퍼티 값을 직접 변경할 수 없다**.

<br/>

### useRef<T = undefined>(): MutableRefObject<T | undefined>

제네릭 타입이 undefined 즉, 타입을 지정하지 않은 경우 리턴 타입은 `MutableRefObject<T | undefined>` 가 된다.

<br/>

## useRef 사용 예시

### 1. useRef를 로컬 변수로 사용하기

다음 예시는 useRef를 로컬 변수로 사용하는 경우이다. 초깃값으로 number 타입과 일치하는 0을 주고, 버튼이 클릭될 때마다 `count.current` 값을 1씩 증가시킨다. 

여기서 `.current` 프로퍼티를 직접 수정할 수 있는 이유는 useRef의 제네릭 타입과 일치하는 초기 값을 넘겨주어 리턴 타입이 `MutableRefObject<number>` 가 되었기 때문이다. 

```tsx
import { useRef } from "react";

export default function App() {
  const count = useRef(0);

  const handleClickButton = () => {
    count.current += 1;
    console.log("count", count);
  };
  return (
    <div>
      <button onClick={handleClickButton}> click </button>
    </div>
  );
}
```

<br/>

### 2. useRef로 DOM 엘레먼트 직접 제어하기

다음 예시는 useRef로 엘레먼트를 직접 제어하는 경우이다. 

RefObject의 경우 current 프로퍼티가 `readonly` 로 되어있기 때문에, 이 경우 current에 값을 주입하는 용도로는 사용할 수 없게 된다. 그런데 아래 예시에서 `inputRef.current.value` 의 값을 변경할 수 있는 이유는 뭘까? 

정의 상 `current` 프로퍼티만 읽기 전용이고, readonly가 **얕은 복사**이기 때문에 current의 하위 프로퍼티는 여전히 수정할 수 있다. 

```tsx
import { useRef } from "react";

export default function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleClickButton = () => {
    inputRef.current.value = "";
  };

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={handleClickButton}> click </button>
    </div>
  );
}
```

<br/>

## 정리

- useRef를 로컬 변수로 사용할 땐 제네릭 타입 T와 일치하는 초깃값을 넘겨준다.
    
    ```tsx
    const localRef = useRef<number>(0); //MutableRefObject<number>
    ```
    
- useRef를 DOM 엘레먼트 직접 제어용으로 사용할 땐 null을 초깃값으로 넘겨준다.
    
    ```tsx
    const inputRef = useRef<HTMLInputElement>(null); //RefObject<HTMLInputElement>
    ```
    
<br/>

## 참고

- [useRef의 3가지 종류로 살펴보는 타입 정의 확인의 중요성](https://darrengwon.tistory.com/865)
- [TypeScript React에서 useRef의 3가지 정의와 각각의 적절한 사용법](https://driip.me/7126d5d5-1937-44a8-98ed-f9065a7c35b5)