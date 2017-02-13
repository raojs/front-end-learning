### 1、任何可以用作css属性值的赋值都可以用作sass变量值。如果变量值与属性不匹配，sass会当作普通字符串来处理。

```css
$family: "microsoft yahei", Arial, sans-serif;
div {
    font-family: $family;
    color: 7em;
}
```
编译为：
```css
div {
    font-family: "microsoft yahei", Ubuntu, Arial, sans-serif;
    color: 7em;
}
```

### 2、添加`!global`标识的变量将作用在全局

```css
.inner {
    $some-color: #ccc !global;
}
div {
    color: $some-color;
}
```
编译为：
```css
div {
    color: #ccc;
}
```

### 3、颜色也是可运算的

```css
div {
    color: #010203 + #030201;
    background: #010203 * 2;
}
```
编译为：
```css
div {
    color: #040404;
    background: #020406;
}
```

### 4、在类名、属性名、字符串变量值中使用变量时，需要用`#{}`包裹

```css
$name: foo;
$prop: width;
div.#{$name} {
    $font-size: 14px;
    $font-weight: bold;
    font: #{$font-size} * #{$font-weight};
    border-#{$prop}: 1px;
}
```
编译为：
```css
div.foo {
    font: 14px * bold;
    border-width: 1px;
}
```

### 5、中划线和下划线是互相兼容的，用中划线申明的变量可以用下划线引用，反之亦然。
```css
div {
    $some-color: #ccc;
    color: $some_color;
}
```
编译为：
```css
div {
    color: #ccc;
}
```

### 6、带有`!default`标识的变量，如果该变量在别处声明了，且声明的值不为null，则用声明的值，否则用这个默认值。

```css
$some-color: #ccc;
$some-color: red !default;
$bg: null;
$bg: #fff !default;
div {
    color: some-color;
    background-color: $bg;
}
```
编译为：
```css
div {
    color: #ccc;
    background-color: #fff;
}
```

### 7、&指向父选择器。如果没有父选择器，&为null，所以&可以在条件语句中使用。

```css
a {
    &:hover {
        color: red;
    }
    .tip & {
        font-size: 14px;
    }
}
.foo .bar, .baz {
    /* 此时&为：((".foo" ".bar"), ".baz") */
    @if & {
        background: #ccc;
    } @else {
        a {
            color: #ccc;
        }
    }
}
```
编译为：
```css
a:hover {
    color: red;
}
.tip a {
    font-size: 14px;
}
.foo .bar, .baz {
    background: #ccc;
}
```
