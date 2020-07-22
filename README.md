# Redux 101

## 0. Introduction

### # 0.4 Project Setup

- 생성

  ```bash
  $ npx create-react-app vanilla-redux
  ```

## 1. Pure Redux: Counter

### # 1.0 Vanilla Counter

``` html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vanilla Redux</title>
  </head>
  <body>
    <button id="add">Add</button>
    <span></span>
    <button id="minus">Minus</button>
  </body>
</html>

```

```js
const add = document.querySelector("#add");
const minus = document.querySelector("#minus");
const number = document.querySelector("span");

let count = 0;
number.innerText = count;

const updateText = () => {
  number.innerText = count;
};

const handleAdd = () => {
  count = count + 1;
  updateText();
};

const handleMinus = () => {
  count = count - 1;
  updateText();
};

add.addEventListener("click", handleAdd);
minus.addEventListener("click", handleMinus);
```

