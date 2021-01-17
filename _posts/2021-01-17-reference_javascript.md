---
title: "JavaScript 메모장"
excerpt: "JavaScript 메모장"
category: Launguage Reference
tags: [javascript]
toc: true
toc_sticky: true
---

## 레퍼런스

### 비교(매우 중요!!)

```js
let a = "a";
let b = "b";
```

### 출력

```js
array = [1, 2, 3, 4, 5];

console.log(변수명); //  출력
console.log(array); // [1, 2, 3, 4, 5] 출력
console.log(array.toString()); // 1, 2, 3, 4, 5 출력
```

### Array 안에 값이 있는지

```js
let num = 57;
let array = [];

// 속도 includes() > indexOf()
array.includes(num); // array안에 num이 있으면 true, 없으면 false
array.includes(num, fromIndex); // index부터 array안의 num을 찾음, 없으면 false
array.indexOf(num); // array 앞에서부터 num을 찾아 인덱스 리턴, 없으면 -1 리턴
array.lastIndexOf(num); // array 끝에서부터 num을 찾아 인덱스 리턴, 없으면 -1 리턴
```

## 자료구조

### Stack

```js
let stack = [1, 2, 3, 4, 5];

stack.push(6); // 5를 push back
stack.pop(); // 6을 pop back
stack[stack.length - 1]; // top
```

### Queue

```js
let queue = [1, 2, 3, 4, 5];

queue.push(6); // 6을 push back
queue.shift(); // 1을 pop front
stack[0]; // front
stack[queue.length - 1]; // back
```

## 알고리즘

### 정렬(sort)

- `sort([compareFunc]);`

- JavaScript의 sort()는 stable_sort()가 아니다. --- Default: 퀵정렬
- 정렬할 개수가 2개 미만일 경우 "sort is not a function" 에러 발생
- `-1`을 리턴하면 바뀌는 원리

```js
let numbers = [1, 5, 2, 4, 3];
let fruits = [
  { name: apple, cost: 100 },
  { name: banana, cost: 400 },
  { name: strawberry, cost: 200 },
  { name: grape, cost: 300 },
];

numbers.sort((a, b) => {
  return a - b; // 오름차순 정렬 --- 1, 2, 3, 4, 5
});
numbers.sort((a, b) => {
  return b - a; // 내림차순 정렬 --- 5, 4, 3, 2, 1
});

fruits.sort((item1, item2) => {
  return item1.cost - item2.cost; // 비용으로 오름차순 정렬
});

fruits.sort((item1, item2) => {
  return item1.cost - item2.cost; // 비용으로 내림차순 정렬
});

completion.sort((person1, person2) => {
  // person2가 더 크거나 같으면 안바꿈(오름차순)
  return person1 < person2 ? 1 : person1 === person2 ? 0 : -1; // 사전 정렬
});
```