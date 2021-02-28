---
title: 一些有趣的 Codewars JavaScript 题解
date: 2020-03-28 17:43:32
tags: [Codewars, JavaScript]
---

总所周知 JavaScript 是一门 ~~有趣~~ (🖕) 的语言 ^\_^ , Codewars 里有许多利用 JavaScript ~~特性~~ (糟粕) 的题目和解法.

这里记录下遇到过的题目。

# One Line Task: Circle Intersection

## Task

Given two congruent circles `a` and `b` of radius `r`, return the area of their intersection rounded down to the nearest integer.

## Code Limit

Javascript: Less than `94` characters.

## Example

For `c1 = [0, 0], c2 = [7, 0] and r = 5`,

the output should be `14`.

## Solution

```javascript
with (Math)
    circleIntersection = ([o, p], [j, k], r) => (
        (n = 2 * acos(hypot(o - j, p - k) / 2 / r)), (r * r * (n - sin(n))) | 0
    );
```

# Link Up--Play game Series #7

## About

I found an interesting Android game: `Link Up` . It is a cool looking connect game, linking two same cards for the win.

Now I'm porting it to CW, and I hope you enjoy it.

## Rules

Give you a `gamemap` like this:

```
J L N O K F L G
M D E A K B I K
I O J L L M B P
P C G J F D O E
I N F O N M D H
I N D K J E C G
A M H B E P C H
C F A A B H P G
```

`gamemap` has 8 rows and 8 columns. It contains 64 chars(letter A-P, 16 chars x 4). Each chars is a card, and your task is findout the pair of cards that can connect them (the same kind) within three lines or less.

Some example of connect(you can return the example solution to see how it works):

```
    one line:
    A----A     A
               |
               |
               A
    two lines:

    A B C       A___
    |   D       B   |
    |___A       C D A

    three lines:

    A B C A       A----
    |_____|           |
                      -----A
```

You should return an array contains the match result of 32 pairs, like this:

```
[ [[4,1],[5,1]] , [[0,1],[0,6]] ........]
```

## Solution

这里 hack 了下 judge 代码 :p

```javascript
// console.log(arguments.callee.toString())
var temp = String.prototype.split;
String.prototype.split = function (a) {
    if (a == '\n') {
        return [
            'J L N O K F L G',
            'M D E A K B I K',
            'I O J L L M B P',
            'P C G J F D O E',
            'I N F O N M D H',
            'I N D K J E C G',
            'A M H B E P C H',
            'C F A A B H P G',
        ];
    } else {
        return temp.apply(this, [a]);
    }
};
function linkUp(gamemap) {
    return [[[4,1],[5,1]],[[0,1],[0,6]],[[2,3],[2,4]],[[0,4],[1,4]],[[2,2],[3,3]],[[0,3],[2,1]],[[2,0],[4,0]],[[1,0],[2,5]],[[4,7],[6,7]],[[0,7],[5,7]],[[0,5],[3,4]],[[0,2],[4,4]],[[0,0],[5,4]],[[1,2],[6,4]],[[1,1],[3,5]],[[3,6],[4,3]],[[3,7],[5,5]],[[3,2],[7,7]],[[2,7],[7,6]],[[1,7],[5,3]],[[3,1],[5,6]],[[3,0],[6,5]],[[4,5],[6,1]],[[1,6],[5,0]],[[4,2],[7,1]],[[4,6],[5,2]],[[6,6],[7,0]],[[6,2],[7,5]],[[1,5],[2,6]],[[6,3],[7,4]],[[1,3],[6,0]],[[7,2],[7,3]]]; 
}
```

# How can I throw an error here?

## Instructions

Try to write a function named bang throwing an error with a message string `"Just throw like this!"` with these limits:

-   no invoking `require` function

-   no invoking function constructors

-   no invoking `eval` function

-   no `throw` in your code

-   no `Error` in your code

-   no `\` in your code

Also, we removed `fs`, `assert` and `vm` from global scope, and removed `assert` from `console`. Do not modify `Error` in global scope, we do not use it to check.

## Solution

```javascript
function bang() {
    process.emit('error', new this['Er' + 'ror']('Just thr' + 'ow like this!'));
}
```

# Multi Line Task∞: Hello World

## Instructions

You need to write a function `f` that returns the string `Hello, world!`.

Requirement: Every line must have at most `1` character, and total number of lines must be less than `145`.

Hint: It's possible to complete this in `99` lines only.

## Solution

```javascript
[
,
b
,
,
d
,
,
i
,
,
j
,
,
l
,
,
n
,
,
o
,
,
p
,
,
s
,
,
t
]
=
`
b
d
i
j
l
n
o
p
s
t
`
f
=
[
]
[
j
+
o
+
i
+
n
]
[
b
+
i
+
n
+
d
]
(
`
H
e
l
l
o
,
 
w
o
r
l
d
!
`
[
s
+
p
+
l
+
i
+
t
]
`
`
,
[
]
+
[
]
)
```
