---
title: Golang note
date: 2019-10-18 13:55:58
tags: Golang
---

# Golang note

## Go 环境变量

### `GOPATH`

`GOPATH` 允许多个目录。

多个目录的时候分隔符：`Windows` 是分号，`Linux` 系统是冒号。

当有多个 `GOPATH` 时，默认会将 go get 的内容放在第一个目录下。

`$GOPATH` 目录约定有三个子目录：

- `src` 存放源代码（比如：`.go` `.c` `.h` `.s` 等）

- `pkg` 编译后生成的文件（比如：`.a`）
    
- `bin` 编译后生成的可执行文件（为了方便，可以把此目录加入到 `$PATH` 变量中，如果有多个 `GOPATH`，那么使用 `\${GOPATH//://bin:}/bin` 添加所有的 `bin` 目录）

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

```go
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