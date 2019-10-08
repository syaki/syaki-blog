---
title: The Tiny Compiler
date: 2019-10-08 10:46:27
---

# A super tiny compiler

This file would only be ~200 lines of actual code.

这个文件大概只有200行代码。

We're going to compile some lisp-like function calls into some C-like function calls.

我们将把一些类似 lisp 的方法调用编译为类似 C 的方法调用。

If we had two functions `add` and `subtract` they would be written like this:

如果我们有两个方法 `add` 和 `subtract`，他们将被写成这样：

```
               LISP                      C

2 + 2          (add 2 2)                 add(2, 2)
4 - 2          (subtract 4 2)            subtract(4, 2)
2 + (4 - 2)    (add 2 (subtract 4 2))    add(2, subtract(4, 2))
```

Most compilers break down into three primary stages: Parsing, Transformation, and Code Generation

大多数编译器主要分为三个阶段：解析、转换和代码生成

1. *Parsing* is taking raw code and turning it into a more abstract representation of the code.
   
    *Parsing* 是将原始代码转化为抽象代码表现形式。

2. *Transformation* takes this abstract representation and manipulates to do whatever the compiler wants it to.
   
    *Transformation* 接受抽象表示并按照编译器的要求操作。

3. *Code Generation* takes the transformed representation of the code and turns it into new code.
   
    *Code Generation* 根据转化后的抽象代码表现形式生产新的代码。

# Parsing

Parsing typically gets broken down into two phases: Lexical Analysis and Syntactic Analysis.

解析通常分为两个阶段：词法分析和句法分析。

1. *Lexical Analysis* takes the raw code and splits it apart into these things called tokens by a thing called a tokenizer (or lexer).
   
    *Lexical Analysis* 获取原始代码，并将其分解为由记号赋予器（或lexer）组成的记号。

    Tokens are an array of tiny little objects that describe an isolated piece of the syntax. They could be numbers, labels, punctuation, operators, whatever.
    
    令牌是一组微小的对象，它们描述了一个孤立的语法片段。它们可以是数字、标签、标点符号、操作符等等。

2. *Syntactic Analysis* takes the tokens and reformats them into a representation that describes each part of the syntax and their relation to one another. This is known as an intermediate representation or Abstract Syntax Tree.
    
    *Syntactic Analysis* 获取标记并将它们重新格式化为一个表示，该表示描述了语法的每个部分以及它们之间的关系。这就是所谓的中间表示或抽象语法树。

    An Abstract Syntax Tree, or AST for short, is a deeply nested object that represents code in a way that is both easy to work with and tells us a lot of information.
    
    抽象语法树，简称AST，是一种嵌套很深的对象，它以一种既易于使用又能告诉我们很多信息的方式表示代码。

For the following syntax:

    (add 2 (subtract 4 2))

Tokens might look something like this:

```js
[
    { type: 'paren',  value: '('        },
    { type: 'name',   value: 'add'      },
    { type: 'number', value: '2'        },
    { type: 'paren',  value: '('        },
    { type: 'name',   value: 'subtract' },
    { type: 'number', value: '4'        },
    { type: 'number', value: '2'        },
    { type: 'paren',  value: ')'        },
    { type: 'paren',  value: ')'        },
]
```

And an Abstract Syntax Tree (AST) might look like this:

```js
{
    type: 'Program',
    body: [{
        type: 'CallExpression',
        name: 'add',
        params: [{
            type: 'NumberLiteral',
            value: '2',
        }, {
            type: 'CallExpression',
            name: 'subtract',
            params: [{
                type: 'NumberLiteral',
                value: '4',
            }, {
                type: 'NumberLiteral',
                value: '2',
            }]
        }]
    }]
}
```

# Transformation

The next type of stage for a compiler is transformation. Again, this just takes the AST from the last step and makes changes to it. It can manipulate the AST in the same language or it can translate it into an entirely new language.

编译器的下一个阶段是转换。同样，这只是从最后一步获取AST并对其进行更改。它可以在同一种语言中操作AST，也可以将它翻译成一种全新的语言。

You might notice that our AST has elements within it that look very similar. There are these objects with a type property. Each of these are known as an AST Node. These nodes have defined properties on them that describe one isolated part of the tree.

您可能会注意到，AST中的元素看起来非常相似。这些对象具有type属性。每个节点都称为AST节点。这些节点上定义了描述树的一个独立部分的属性。

We can have a node for a "NumberLiteral":

```js
{
    type: 'NumberLiteral',
    value: '2',
}
```

Or maybe a node for a "CallExpression":

```js
{
    type: 'CallExpression',
    name: 'subtract',
    params: [...nested nodes go here...],
}
```

When transforming the AST we can manipulate nodes by adding/removing/replacing properties, we can add new nodes, remove nodes, or we could leave the existing AST alone and create an entirely new one based on it.

在转换AST时，我们可以通过添加/删除/替换属性来操作节点，我们可以添加新节点，删除节点，或者我们可以不使用现有的AST并基于它创建一个全新的AST。

Since we’re targeting a new language, we’re going to focus on creating an entirely new AST that is specific to the target language.

由于我们的目标是一种新语言，所以我们将重点创建一个针对目标语言的全新AST。

## Traversal

In order to navigate through all of these nodes, we need to be able to traverse through them. This traversal process goes to each node in the AST depth-first.

为了浏览所有这些节点，我们需要能够遍历它们。这个遍历过程首先遍历AST深度第一的每个节点。

```js
{
    type: 'Program',
    body: [{
        type: 'CallExpression',
        name: 'add',
        params: [{
            type: 'NumberLiteral',
            value: '2'
        }, {
            type: 'CallExpression',
            name: 'subtract',
            params: [{
                type: 'NumberLiteral',
                value: '4'
            }, {
                type: 'NumberLiteral',
                value: '2'
            }]
        }]
    }]
}
```

So for the above AST we would go:

1. Program - Starting at the top level of the AST
    
    从AST的顶层开始

2. CallExpression (add) - Moving to the first element of the Program's body

    移动到程序主体的第一个元素

3. NumberLiteral (2) - Moving to the first element of CallExpression's params

    移动到CallExpression的参数的第一个元素

4. CallExpression (subtract) - Moving to the second element of CallExpression's params

    移动到CallExpression的参数的第二个元素

5. NumberLiteral (4) - Moving to the first element of CallExpression's params

    移动到CallExpression的参数的第一个元素

6. NumberLiteral (2) - Moving to the second element of CallExpression's params

    移动到CallExpression的参数的第二个元素

If we were manipulating this AST directly, instead of creating a separate AST, we would likely introduce all sorts of abstractions here. But just visiting each node in the tree is enough for what we're trying to do.

如果我们直接操作这个AST，而不是创建一个单独的AST，我们可能会在这里引入各种各样的抽象。但只要访问树中的每个节点就足够了。

The reason I use the word "visiting" is because there is this pattern of how to represent operations on elements of an object structure.

我之所以使用“访问”这个词，是因为存在这样一种模式，即如何表示对象结构元素上的操作。

## Visitors