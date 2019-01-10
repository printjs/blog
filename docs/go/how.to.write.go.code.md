[首页](https://printjs.github.io/blog) / [go语言](https://printjs.github.io/blog/docs/go) / 如何写go语言


# 如何写go语言

> 转自[https://golang.org/doc/code.html](https://golang.org/doc/code.html)

## 介绍

这份文档演示简单的go语言包开发并且介绍[go]工具(https://golang.org/cmd/go/)，fetch，build和安装go包以及命令的标准方式。

`go`工具需要你在指定的方式组织你的代码。请仔细阅读这篇文档。它解释了启动并运行你的go安装最简单的方式。

有一个[视频](https://www.youtube.com/watch?v=XCsL89YtqCs)也是类似的解释

## 代码组织


### 概览

* go编程者一般保持他们所有的代码在一个单独的工作区域
* 一个工作区包含很多版本的存储库（通过git管理）
* 每一个项目包含一个或多个包
* 每一个包由一个或多个在独立文件夹下的go源文件组成。
* 包目录的路径确定其导入路径。


注意，这里有别于其他的编程语言环境，每一个工程都有一个独立的工作区并且工作区与版本的控制库紧紧相连

### 工作区

工作区是一个层次目录，其根目录有两个文件夹

* `src`包含go的源文件
* `bin`包含可执行的命令

`go`工具构建二进制文件并且向`bin`文件夹安装二进制文件

`src`的子目录一般包含多个版本控制的存储库，用于跟踪一个或多个源包的开发。

为了让您了解工作区在实践中的外观，以下是一个示例：

```
bin/
    hello                          # command executable
    outyet                         # command executable
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
	hello/
	    hello.go               # command source
	outyet/
	    main.go                # command source
	    main_test.go           # test source
	stringutil/
	    reverse.go             # package source
	    reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
	bmp/
	    reader.go              # package source
	    writer.go              # package source
    ... (many more repositories and packages omitted) ...
```

上面的树展示一个工作区包含两个存储库（example和image）。这个`example`存储库包含两个命令（hello和outyet）和一个库（stringutil）。这个`image`存储库包含`bmp`包和[其他若干](https://godoc.org/golang.org/x/image)

一个典型的工作区包含许多源存储库，源存储库包含许多包和命令。大部分go编程者保持所有他们的go源代码并且依赖于一个单独的工作区。

请注意，不应使用符号链接将文件或目录链接到工作区。

命令和库是从不同类型的源包构建的。 我们稍后会讨论这种区别。

`GOPATH`环境变量

`GOPATH`环境变量指定你工作区的位置。它默认是在你home文件下的go文件夹，因此`$HOME/go on Unix`， `$home/go on Plan 9`, 和 `%USERPROFILE%\go (usually C:\Users\YourName\go) on Windows`。

假如你喜欢在不同的地方工作，你将需要设置`GOPATH`为目标路径。注意GOPATH一定不能是你go的安装目录。


命令`go env GOPATH` 打印当前有效的`GOPATH`;如果环境变量未设置，他打印默认的定位

为了方便，增加工作区子目录`bin`在你的PATH中

```shell
$ export PATH=$PATH:$(go env GOPATH)/bin
```


为简洁起见，本文档其余部分中的脚本使用`$ GOPATH`而不是`$ go env GOPATH`。 如果你没有设置`GOPATH`，要使脚本以书面形式运行，你可以在这些命令中替换`$ HOME / go`，否则运行：

```shell
$ export GOPATH=$(go env GOPATH)
```

为了学习更多关于*GOPATH*环境变量的值，参看[go help gopath](https://golang.org/cmd/go/#hdr-GOPATH_environment_variable)


使用自定义工作区位置，设置[环境变量](https://github.com/golang/go/wiki/SettingGOPATH)


### 导入路径

导入路径是唯一标识包的字符串。包的导入路径对应于其在工作空间内或远程存储库中的位置（解释如下）

来自于标准库的包给予简短的导入路径，比如`fmt`,`net/http`。对于你自己的包，你必须选择基础路径，该路径不太可能与将来添加到标准库或其他外部库中发生冲突。

如果你保持你代码在源存储的什么地方，你将使用源存储库的根作为你的基础路径。目前，如果你有github账户在github.com/user,这将是你的基础路径

请注意，在构建代码之前，无需将代码发布到远程存储库。 组织代码只是一个好习惯，好像有一天你会发布它一样。 实际上，您可以选择任意路径名称，只要它对标准库和更大的Go生态系统是唯一的。

我们将使用github.com/user 作为我们的基础路径，创建一个文件夹在你的工作区保存源代码

```shell
mkdir -p $GOPATH/src/github.com/user
```

### 第一个程序

编译运行一个简单的程序，首先选择一个包的路径（我们将使用github.com/user/hello）并且穿件对应的包文件夹在你的工作区

```shell
$ mkdir $GOPATH/src/github.com/user/hello
```

接下来，创建一个名为`hello.go`的文件在hello文件夹下，包含以下go代码

```go
package main

import "fmt"

func main() {
    mt.Println("Hello, world.")
}
```

现在，你可以使用`go`工具构建并且安装这个程序了

```shell
$ go install github.com/user/hello
```

注意，你可以运行这个命令在你系统的任何地方。这个`go`工具通过指定的GOPATH的工作区目录下的github.com/user/hello包找到源代码

你也可以忽略包路径如果你运行`go install`在包目录下

```shell
$ cd $GOPATH/src/github.com/user/hello
$ go install
```

这个命令构建hello命令，产生一个可执行的二进制。它将会安装在工作区的bin目录下，（在window为，hello.exe）在我们的例子，它将会是`$GOPATH/bin/hello` 即 `$HOME/go/bin/hello`

你可以运行该程序，通过输入完成的路径
```shell
$ $GOPATH/bin/hello
Hello, world.
```
或者，你可以增加`$GOPATH/bin/`在你的PATH中，仅仅输入二进制名称就可执行
```shell
$ hello
Hello, world.
```
如果你使用源控制系统，现在是好时间去初始化存储库，增加文件，并且提交你第一次改变。再一次，这一步是额外的，你不需要使用源控制器去写go代码
```shell
$ cd $GOPATH/src/github.com/user/hello
$ git init
Initialized empty Git repository in /home/user/work/src/github.com/user/hello/.git/
$ git add hello.go
$ git commit -m "initial commit"
[master (root-commit) 0b4507d] initial commit
 1 file changed, 1 insertion(+)
  create mode 100644 hello.go
```

### 第一个二进制

一起写一个二进制并且在hello程序中使用它。

再一次，在第一步是选择包的路径（我们将使用github.com/user/stringutil）并且创建包文件夹

```shell
$ mkdir $GOPATH/src/github.com/user/stringutil
```

接下来，创建一个文件命名为`reverse.go`并且键入如下内容
```go
// Package stringutil contains utility functions for working with strings.
package stringutil

// Reverse returns its argument string reversed rune-wise left to right.
func Reverse(s string) string {
    r := []rune(s)
    for i, j := 0, len(r)-1; i < len(r)/2; i, j = i+1, j-1 {
        r[i], r[j] = r[j], r[i]
    }
    return string(r)
}
```
现在，测试包使用go build编译：
```shell
$ go build github.com/user/stringutil
```
或者，如果你在包的源目录下，仅仅执行
```shell
$ go build
```
这不会产生输出文件。取而代之的是它保存一个编译的包在本地构建缓存中。
确认完`stringutil`包构建后，修改你原版的`hello.go`（在$GOPATH/src/github.com/user/hello）
```go
package main

import (
    "fmt"
    "github.com/user/stringutil"
)

func main() {
    fmt.Println(stringutil.Reverse("!oG ,olleH"))
}
```
安装hello程序
```shell
$ go install github.com/user/hello
```
运行一个新版本的程序，你将会看到新的信息
```shell
$ hello
Hello, Go!
```
在上述步骤后，你的工作区就像下面
```
bin/
    hello                 # command executable
src/
    github.com/user/
        hello/
            hello.go      # command source
        stringutil/
            reverse.go    # package source
```

### 包名称
第一句语句在go的源文件必须是`package name`

这个`name`是包的默认导入名称（所有文件在包中必须使用相同的`name`）

Go的约定是包名称是导入路径的最后一个元素：导入为“crypto / rot13”的包应命名为rot13。

可执行命令必须始终使用`package main`。

不要求包名称在链接到单个二进制文件的所有包中都是唯一的，只要导入路径（它们的完整文件名）是唯一的。

请参阅[Effective Go](https://golang.org/doc/effective_go.html#names)以了解有关Go的命名约定的更多信息。

## 测试

Go有一个由go test命令和测试包组成的轻量级测试框架。

你可以通过创建以`_test.go`为结尾的文件编写测试。它包含签名函数为`TestXXX`func(t *testing.T)。测试框架运行在每一个这样的函数；如果函数执行失败，比如t.Error或者t.Fail,这个测试被认为是失败了。

增加测试给`stringutil`包通过创建文件在` $GOPATH/src/github.com/user/stringutil/reverse_test.go`中，并且包含以下内容
```go
package stringutil

import "testing"

func TestReverse(t *testing.T) {
    cases := []struct {
        in, want string
    }{
        {"Hello, world", "dlrow ,olleH"},
        {"Hello, 世界", "界世 ,olleH"},
        {"", ""},
    }
    for _, c := range cases {
        got := Reverse(c.in)
        if got != c.want {
            t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
        }
    }
}
```
然后使用`go test`运行测试
```shell
$ go test github.com/user/stringutil
ok    github.com/user/stringutil 0.165s
```
一如既往，如果你运行`go`工具从包文件夹中，你可以忽略包路径
```shell
$ go test
ok    github.com/user/stringutil 0.165s
```

使用[go help test](https://golang.org/cmd/go/#hdr-Test_packages)可以查看[测试包文档](https://golang.org/pkg/testing/)

## 远程包

导入路径可以描述如何使用诸如Git或Mercurial之类的修订控制系统来获取包源代码。

`go`工具使用此属性自动从远程存储库获取包。 例如，本文档中描述的示例也保存在GitHub上托管的Git存储库中`github.com/golang/example`如果您在包的导入路径中包含存储库URL，那么`go get`将自动获取，构建和安装它：

```shell
$ go get github.com/golang/example/hello
$ $GOPATH/bin/hello
Hello, Go examples!
```

如果工作空间中不存在指定的包,`go get` 会将它放在GOPATH指定的第一个工作空间内。（如果该包已经存在，`go get`跳过远程拉取，其行为与`go install`一样）

发出上面的`go get`命令后，工作区目录树现在应如下所示：
```
bin/
    hello                           # command executable
src/
    github.com/golang/example/
    .git/                       # Git repository metadata
        hello/
            hello.go                # command source
        stringutil/
            reverse.go              # package source
            reverse_test.go         # test source
    github.com/user/
        hello/
            hello.go                # command source
        stringutil/
            reverse.go              # package source
            reverse_test.go         # test source
```
在GitHub上托管的hello命令取决于同一存储库中的stringutil包。 `hello.go`文件中的导入使用相同的导入路径约定，因此`go get`命令也能够找到并安装依赖包。`

```go
import "github.com/golang/example/stringutil"
```
此约定是使您的Go包可供其他人使用的最简单方法。 [Go Wiki](https://golang.org/wiki/Projects)和[godoc.org](https://godoc.org/)提供了外部Go项目的列表。

有关使用go工具使用远程存储库的更多信息，请参阅[go help importpath](https://golang.org/cmd/go/#hdr-Remote_import_paths)。

