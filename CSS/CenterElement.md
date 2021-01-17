# 垂直居中

> 对元素进行水平居中，行内元素，则父元素应用`text-align: center`；块级元素，对自身应用`margin: auto`。然而如果要对一个元素进行垂直居中，则麻烦一些。

## 基于绝对定位的实现方式

> 想办法把元素的正中心，放到视口的正中心。

- 宽高固定

```css
main {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-top: -3em; /* 6/2 = 3 */
  margin-left: -9em; /* 18/2 = 9 */
  width: 18em;
  height: 6em;
}

main {
  position: absolute;
  top: calc(50% - 3em);
  left: calc(50% - 9em);
  width: 18em;
  height: 6em;
}
```

- 宽高不固定

> 以下方式已算完美，但对不能使用绝对定位的场景则无法使用。

```css
main {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

## 基于视口的解决方案

> vw 是与视口宽度相关的，1vw表示视口宽度的1%。
> 局限性：只适用于在视口中居中的场景。

```css
main {
  width: 18em;
  padding: 1em 1.5em;
  margin: 50vh auto 0;
  transform: translateY(-50%);
}
```

## 基于flexbox的解决方案

> 终极方案，且flex布局得到了全面普及

```css
main {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 18em;
  height: 10em;
}
```
