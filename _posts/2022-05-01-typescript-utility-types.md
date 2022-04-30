---
layout: post
title: "TypeScript 유틸리티 타입"
tags: TypeScript
---

타입스크립트는 타입 변환을 용이하게 하는 몇 가지 유틸리티 타입을 제공한다. 예를 들어 A 타입의 부분 집합인 B 타입을 생성할 때 기존 타입을 재활용해서 생성할 때 유용하다.

익숙한 유틸 타입은 손에 익어서 자주 활용하게 되는데 몇 가지는 아직 낯설다. 나중에 쉽게 찾을 수 있도록 간단한 예시와 함께 정리했다.

<br>

## Partial\<T>

제네릭 타입 T의 **모든 프로퍼티가 옵셔널하게 설정**된 타입을 생성한다. 

```tsx
interface User {
    name: string; 
    age: number;
    address: string;
}

// ❗ Bad practice
interface PartialUser {
    name?: string;
    age?: number;
    address?: string;
}

// ✅ Good practice
type PartialUser = Partial<User>; 
```

<br>

## Required\<T>

제네릭 타입 T의 **모든 프로퍼티가 필수(required) 로 설정**된 타입을 생성한다. Partial의 반대이다. 

```tsx
interface User {
    name?: string;
    age?: number;
    address?: string;
}

// ❗ Bad practice
interface RequiredUser {
    name: string;
    age: number;
    address: string;
}

// ✅ Good practice
type RequiredUser = Required<User>; 
```
<br> 

## Readonly\<T>

제네릭 타입 T의 **모든 프로퍼티가 `readonly` 로 설정**된 타입을 생성한다. 생성된 타입의 프로퍼티는 값을 재할당 할 수 없다. 

```tsx
interface User {
    name: string;
    age: number;
}

// ❗ Bad practice
interface ReadonlyUser{
    readonly name: string;
    readonly age: number;
}

// ✅ Good practice
type ReadonlyUser = Readonly<User>; 
const user: ReadonlyUser = {name: 'abc', age: 99}; 
user.name = 'lee'; // Error: Cannot assign to 'name' because it is a read-only property.
```

<br>

## Record<K, T>

**키가 제네릭 타입 K이고, 값이 제네릭 타입 T인 객체 타입을 생성**한다. 한 타입의 프로퍼티를 다른 타입에 맵핑할 때 사용할 수 있다.

```tsx
interface CatInfo {
    age: number;
    breed: string;
}

interface Cats {
    cat1 : CatInfo;
    cat2 : CatInfo; 
}

// ✅ Alternative
type CatsRecord = Record<"cat1" | "cat2", CatInfo>;
```
<br>

## Pick<T, K>
제네릭 타입 T에서 프로퍼티의 조합인 제네릭 타입 K를 골라 타입을 생성한다. K는 **문자열 리터럴** 또는 **union의 문자열 리터럴**로 넘긴다.

```tsx
interface User {
    name: string;
    age: number;
    address: string;
}

// ❗ Bad practice
interface PartialUser {
    name: string;
    age: number;
}

// ✅ Good practice 
type PartialUser = Pick<User, "name" | "age">;
```
<br>

## Omit<T, K>

제네릭 타입 T에서 모든 프로퍼티를 선택하고 제네릭 타입 K를 제거한 타입을 생성한다. K는 **문자열 리터럴** 또는 **union의 문자열 리터럴**로 넘긴다.

```tsx
interface User {
   name: string;
   age: number;
   address: string;
}

// ❗ Bad practice
interface PartialUser {
   name: string;
   age: number;
}

// ✅ Good practice
type PartialUser = Omit<User, "address">;
```
<br>

## Exclude<T, U>

제네릭 타입 T에서 제네릭 타입 U와 겹치는 타입을 제외한 타입을 생성한다. (T - U)

```tsx
type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
     
type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // "c"
     
// string | number
type T2 = Exclude<string | number | (() => void), Function>;
```
<br>

## Extract<T, U>

제네릭 타입 T에서 제네릭 타입 U에 할당 가능한 타입을 생성한다. Exclude의 반대이다.

```tsx
type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
     
// () => void
type T1 = Extract<string | number | (() => void), Function>;
```

<br>

## NonNullable\<T>

제네릭 타입 T에서 `null` 과 `undefined` 를 제외한 타입을 생성한다.
```tsx
type Role = "ADMIN" | "USER" | null;

// ❗ Bad practice
type NonNullableRole = "ADMIN" | "USER";

// ✅ Good practice 
type NonNullableRole = NonNullable<Role>; // "ADMIN" | "USER"
```