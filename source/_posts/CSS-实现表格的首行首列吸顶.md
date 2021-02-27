---
title: CSS 实现表格的首行首列吸顶
date: 2021-02-27 22:54:34
tags: CSS
---

# 背景

最近在对现有的页面进行优化。

发现之前页面中表格的首行首列吸顶效果是由 js 在 onscroll 中计算 top 和 left 的数值实现的。

# CSS 的实现方法

通过谷歌发现一个通过 `position: sticky` 和 `table-layout: fixed` 配合 `table` 的实现方法非常巧妙。

这里利用 `position: sticky` 和 `width: max-content` 配合 `div` 实现吸顶的效果。

效果如下：

<style>
    .main {
        width: 500px;
        height: 280px;
        overflow-x: scroll;
        overflow-y: scroll;
    }
    .head,
    .row {
        width: max-content;
    }
    .head {
        position: sticky;
        top: 0;
        z-index: 2;
    }
    .col {
        display: inline-block;
        border: 1px solid #5f5f5f;
        width: 100px;
        height: 40px;
    }
    .head .col {
        background-color: cornflowerblue;
    }
    .head .col:first-child {
        position: sticky;
        left: 0;
        background-color: aquamarine;
        z-index: 1;
    }
    .row .col:first-child {
        position: sticky;
        left: 0;
        z-index: 1;
        background-color: palegoldenrod;
    }
</style>
<div class="main">
    <div class="head">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <div class="row">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
</div>

附上 demo 代码：

```html
<style>
    .main {
        width: 500px;
        height: 280px;
        overflow-x: scroll;
        overflow-y: scroll;
    }
    .head,
    .row {
        width: max-content;
    }
    .head {
        position: sticky;
        top: 0;
        z-index: 2;
    }
    .col {
        display: inline-block;
        border: 1px solid #5f5f5f;
        width: 100px;
        height: 40px;
    }
    .head .col {
        background-color: cornflowerblue;
    }
    .head .col:first-child {
        position: sticky;
        left: 0;
        background-color: aquamarine;
        z-index: 1;
    }
    .row .col:first-child {
        position: sticky;
        left: 0;
        z-index: 1;
        background-color: palegoldenrod;
    }
</style>
<div class="main">
    <div class="head">
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
        <div class="col"></div>
    </div>
    <template v-for="n in 10">
        <div class="row" :key="n">
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
            <div class="col"></div>
        </div>
    </template>
</div>
```
