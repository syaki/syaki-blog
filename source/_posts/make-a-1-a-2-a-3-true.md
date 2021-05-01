---
title: make (a == 1 && a == 2 && a == 3) === true
date: 2021-05-01 12:24:15
tags: JavaScript
---

1.  a 类型为 Object，并且调用 toString 或者 ValueOf 返回的结果与 b 严格相等

    ```javascript
    const a = {
        i: 1,
        toString: function () {
            return a.i++;
        },
        valueOf() {
            return this.i++;
        },
    };

    if (a == 1 && a == 2 && a == 3) {
        console.log('Hello World!');
    }
    ```

2.  Proxy 对象默认的 toString 和 valueOf 方法会返回被 getter 劫持过的结果

    ```javascript
    const a = new Proxy(
        { i: 1 },
        {
            get(target) {
                return () => target.i++;
            },
        }
    );

    if (a == 1 && a == 2 && a == 3) {
        console.log('Hello World!');
    }
    ```

3.  Strict Mode 下, Object.defineProperty 方法

    ```javascript
    let i = 1;
    Object.defineProperty(window, 'a', {
        get() {
            return i++;
        },
    });

    if (a === 1 && a === 2 && a === 3) {
        console.log('Hello World!');
    }
    ```

参考资料:

-   https://zhangzhao.name/posts/make-a-1-a-2-a-3-evaluate-true/
-   https://stackoverflow.com/questions/48270127/can-a-1-a-2-a-3-ever-evaluate-to-true
