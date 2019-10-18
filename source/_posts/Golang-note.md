---
title: Golang note
date: 2019-10-18 13:55:58
tags: Golang
---

# Golang note

## `Go` 环境

### `GOPATH`

`GOPATH` 允许多个目录。

多个目录的时候分隔符：`Windows` 是分号，`Linux` 系统是冒号。

当有多个 `GOPATH` 时，默认会将 go get 的内容放在第一个目录下。

`$GOPATH` 目录约定有三个子目录：

-   `src` 存放源代码（比如：`.go` `.c` `.h` `.s` 等）

-   `pkg` 编译后生成的文件（比如：`.a`）
-   `bin` 编译后生成的可执行文件（为了方便，可以把此目录加入到 `$PATH` 变量中，如果有多个 `GOPATH`，那么使用 `\${GOPATH//://bin:}/bin` 添加所有的 `bin` 目录）

### 代码目录结构

应用包或者可执行应用根据 `package` 是 `main` 还是其他来决定，一般建议 `package` 的名称和目录名保持一致。

### 编译和应用

应用包编译安装的两种方法：

1. 进入对应的应用包目录，然后执行 `go install`

2. 在任意的目录执行如下代码 `go install ${PackageName}`

安装完后，可在 `pkg` 目录下看到 `${PackageName}.a` 文件。

调用应用包：

`import` 调用，如果是多级目录，就在 `import` 里面引入多级目录，如果你有多个 `GOPATH`，`Go` 会自动在多个 `$GOPATH/src` 中寻找。

进入应用目录，然后执行 `go build`，那么在该目录下面会生成一个 `${ApplicationName}` 的可执行文件。

### 获取远程包

在代码中如何使用远程包，在开头 `import` 相应的路径：

```golang
import "github.com/astaxie/beedb"
```

### 整体结构

```
bin/
    ${ApplicationName}
pkg/
    平台名/ 如：darwin_amd64、linux_amd64
        ${PackageName}.a
        github.com/
            astaxie/
                beedb.a
src/
    ${ApplicationName}
        main.go
    ${PackageName}/
        ${FunctionName}.go
    github.com/
        astaxie/
            beedb/
                beedb.go
                util.go
```

## `Go` 命令

### `go build` & `go install`

`go build` 用于编译代码。在包的编译过程中，若有必要，会同时编译与之相关联的包。

-   普通包，执行 `go build` 后，不会产生任何文件。如果需要在 `$GOPATH/pkg` 下生成相应的文件，就得执行 `go install`。

-   `main` 包，执行 `go build` 之后，会在当前目录下生成一个可执行文件。如果需要在 `$GOPATH/bin` 下生成相应的文件，需要执行 `go install`，或者使用 `go build -o ${PATH}/a.exe`。

-   只想编译某个文件，就可在 `go build` 之后加上文件名，`go build` 命令默认会编译当前目录下的所有 `go` 文件。

-   指定编译输出的文件名。可以指定 `go build -o ${Name}.exe`，默认情况是 `package` 名(非 `main` 包)，或者是第一个源文件的文件名(`main` 包)。

    （注：实际上，`package` 名在 `Go` 语言规范中指代码中 `package` 后使用的名称，此名称可以与文件夹名不同，默认生成的可执行文件名是文件夹名。）

-   `go build` 会忽略目录下以 `“_”` 或 `“.”` 开头的 `go` 文件。

-   如果源代码针对不同的操作系统需要不同的处理，可以根据不同的操作系统后缀来命名文件。

    例如有一个读取数组的程序，它对于不同的操作系统可能有如下几个源文件：

    `array_linux.go` `array_darwin.go` `array_windows.go` `array_freebsd.go`

    `go build` 的时候会选择性地编译以系统名结尾的文件（Linux、Darwin、Windows、Freebsd）。例如 Linux 系统下面编译只会选择 array_linux.go 文件，其它系统命名后缀文件全部忽略。

### `go clean`

用来移除当前源码包和关联源码包里面编译生成的文件。这些文件包括：

```
_obj/            旧的object目录，由Makefiles遗留
_test/           旧的test目录，由Makefiles遗留
_testmain.go     旧的gotest文件，由Makefiles遗留
test.out         旧的test记录，由Makefiles遗留
build.out        旧的test记录，由Makefiles遗留
*.[568ao]        object文件，由Makefiles遗留

DIR(.exe)        由go build产生
DIR.test(.exe)   由go test -c产生
MAINFILE(.exe)   由go build MAINFILE.go产生
*.so             由 SWIG 产生
```

### `go test`

执行命令，自动读取源码目录下面名为 `*_test.go` 的文件，生成并运行测试用的可执行文件。

### `godoc`

在 `Go 1.2` 版本之前还支持 `go doc` 命令，但是之后全部移到了 `godoc` 这个命令下，需要这样安装 `go get golang.org/x/tools/cmd/godoc` 。

如果是 `http` 包，那么执行 `godoc net/http` 查看某一个包里面的函数。

通过命令在命令行执行 `godoc -http=:端口号` 比如 `godoc -http=:8080`。然后在浏览器中打开 `127.0.0.1:8080`，将会看到一个 `golang.org` 的本地 `copy` 版本，通过它可以查询 `pkg` 文档等其它内容。如果设置了 `GOPATH`，在 `pkg` 分类下，不但会列出标准包的文档，还会列出本地 `GOPATH` 中所有项目的相关文档。

## `Go` 语言

### 程序

包名 `main` 告诉我们它是一个可独立运行的包，编译后会产生可执行文件。除了 `main` 包之外，其它的包最后都会生成 `*.a` 文件（也就是包文件）并放置在 `$GOPATH/pkg/$GOOS_$GOARCH` 中（以 `Mac` 为例就是 `$GOPATH/pkg/darwin_amd64`）。

### 变量 & 常量

`var` 关键字定义变量，把变量类型放在变量名后面。

-   简短声明：`:=` 这个符号直接取代了 `var` 和 `type`。只能用在函数内部，在函数外部使用则会无法编译通过，一般用 `var` 方式来定义全局变量。

-   `_` （下划线）是个特殊的变量名，任何赋予它的值都会被丢弃。

-   对于已声明但未使用的变量会在编译阶段报错。

`const` 常量可定义为数值、布尔值或字符串等类型。

### 类型

-   整数类型有无符号和带符号两种。同时支持 `int` 和 `uint`。两种类型的长度相同，具体长度取决于不同编译器的实现。

    直接定义好位数的类型：`rune`, `int8`, `int16`, `int32`, `int64` 和 `byte`, `uint8`, `uint16`, `uint32`, `uint64`。其中 `rune` 是 `int32` 的别称，`byte` 是 `uint8` 的别称。

    这些类型的变量之间不允许互相赋值或操作。

    浮点数的类型有 `float32` 和 `float64` 两种（没有 `float` 类型），默认是 `float64`。

    复数的默认类型是 `complex128`（64 位实数+64 位虚数）。如果需要小一些的，也有 `complex64` (32 位实数+32 位虚数)。复数的形式为 `RE + IMi`，其中 `RE` 是实数部分，`IM` 是虚数部分，而最后的 `i` 是虚数单位。

-   字符串都是采用 `UTF-8` 字符集编码。字符串是用一对双引号（`""`）或反引号（\` \`）括起来定义，它的类型是 `string`。

    字符串是不可变。

    修改字符串：

    ```golang
    s := "hello"
    c := []byte(s)  // 将字符串 s 转换为 []byte 类型
    c[0] = 'c'
    s2 := string(c)  // 再转换回 string 类型
    fmt.Printf("%s\n", s2)

    s := "hello"
    s = "c" + s[1:] // 字符串虽不能更改，但可进行切片操作
    fmt.Printf("%s\n", s)
    ```

    可以使用 `+` 操作符来连接两个字符串。

    可以通过 \` 声明一个多行的字符串，\` 括起的字符串为 `Raw` 字符串，即字符串在代码中的形式就是打印时的形式，它没有字符转义，换行也将原样输出。

-   `error` 类型，专门用来处理错误信息，`package` 里有一个包 `errors` 来处理错误。

### 分组声明

```golang
const(
    i = 100
    pi = 3.1415
    prefix = "Go_"
)

var(
    i int
    pi float32
    prefix string
)
```

### `iota` 枚举

关键字 `iota`，这个关键字用来声明 `enum` 的时候采用，它默认开始值是 `0`，`const` 中每增加一行加 1：

```golang
const (
    x = iota // x == 0
    y = iota // y == 1
    z = iota // z == 2
    w        // 常量声明省略值时，默认和之前一个值的字面相同。这里隐式地说w = iota，因此w == 3。其实上面y和z可同样不用"= iota"
)

const v = iota // 每遇到一个const关键字，iota就会重置，此时v == 0

const (
    h, i, j = iota, iota, iota //h=0,i=0,j=0 iota在同一行值相同
)

const (
    a       = iota //a=0
    b       = "B"
    c       = iota             //c=2
    d, e, f = iota, iota, iota //d=3,e=3,f=3
    g       = iota             //g = 4
)

func main() {
    fmt.Println(a, b, c, d, e, f, g, h, i, j, x, y, z, w, v)
}
```

### 默认规则

-   大写字母开头的变量是可导出的，也就是其它包可以读取的，是公有变量；小写字母开头的就是不可导出的，是私有变量。
-   大写字母开头的函数也是一样，相当于 `class` 中的带 `public` 关键词的公有函数；小写字母开头的就是有 `private` 关键词的私有函数。
