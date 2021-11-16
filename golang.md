## Principle

- less is exponentially more
  - rob pike, go designer
- do less enable more
  - russ cox, go tech lead

### cons of existing lang

- dependency management: C
- heavy and complex: Java, C++
- GC support
- Parallel computing support
- multi-core CPU support

## go feature

- high efficiency compiling
- strong support for concurrency
- gc oriented
- clear dependency management
- removal of "inherit", keep it simple

### go not supporting

- override of function and operator
- implicit conversion
- inherit (though support interface)
- dll
- dynamic load code
- exception (use recover and panic)
- assert
- static variable

## go application

- cloud native eco system



## compile and run

- compile

```powershell
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go build .\main.go
```

- check

```powershell
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> ls

    Directory: D:\github\nanshaqundao\golang\examples\module1\helloworld

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          31/10/2021    13:10        2035712 main.exe
-a---          30/10/2021    21:19            331 main.go
```

- run

```powershell
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> .\main.exe
os args is: [D:\github\nanshaqundao\golang\examples\module1\helloworld\main.exe]
input parameter is: world
Hello world from Go
```

### cross build from win to linux

- check current env

```go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go env                        
set GO111MODULE=
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\Nansh\AppData\Local\go-build
set GOENV=C:\Users\Nansh\AppData\Roaming\go\env
set GOEXE=.exe
set GOEXPERIMENT=
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOINSECURE=
set GOMODCACHE=C:\Users\Nansh\go\pkg\mod
set GONOPROXY=
set GONOSUMDB=
set GOOS=windows
set GOPATH=C:\Users\Nansh\go
set GOPRIVATE=
set GOPROXY=https://proxy.golang.org,direct
set GOROOT=C:\Program Files\Go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=C:\Program Files\Go\pkg\tool\windows_amd64
set GOVCS=
set GOVERSION=go1.17.1
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=D:\github\nanshaqundao\golang\go.mod
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\Nansh\AppData\Local\Temp\go-build2936208148=/tmp/go-build -gno-record-gcc-switches
```

- as the GOARCH is already amd64, just set the GOOS

```go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> $Env:GOOS = "linux"
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go env
set GO111MODULE=
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\Nansh\AppData\Local\go-build
set GOENV=C:\Users\Nansh\AppData\Roaming\go\env
set GOEXE=
set GOEXPERIMENT=
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOINSECURE=
set GOMODCACHE=C:\Users\Nansh\go\pkg\mod
set GONOPROXY=
set GONOSUMDB=
set GOOS=linux
set GOPATH=C:\Users\Nansh\go
set GOPRIVATE=
set GOPROXY=https://proxy.golang.org,direct
set GOROOT=C:\Program Files\Go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=C:\Program Files\Go\pkg\tool\windows_amd64
set GOVCS=
set GOVERSION=go1.17.1
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=0
set GOMOD=D:\github\nanshaqundao\golang\go.mod
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-fPIC -m64 -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\Nansh\AppData\Local\Temp\go-build823267327=/tmp/go-build -gno-record-gcc-switches
```

- compile and build

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go build .\main.go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> ls

    Directory: D:\github\nanshaqundao\golang\examples\module1\helloworld

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          31/10/2021    13:16        1878347 main
-a---          31/10/2021    13:10        2035712 main.exe
-a---          30/10/2021    21:19            331 main.go
```

- the new "13:16" main file.. try in ubuntu wsl

```
nash@LAPTOP-SP9KAOQ6:/mnt/d/github/nanshaqundao/golang/examples/module1/helloworld$ pwd
/mnt/d/github/nanshaqundao/golang/examples/module1/helloworld
nash@LAPTOP-SP9KAOQ6:/mnt/d/github/nanshaqundao/golang/examples/module1/helloworld$ ls -ltr
total 3824
-rwxrwxrwx 1 nash nash     331 Oct 30 21:19 main.go
-rwxrwxrwx 1 nash nash 2035712 Oct 31 13:10 main.exe
-rwxrwxrwx 1 nash nash 1878347 Oct 31 13:16 main
nash@LAPTOP-SP9KAOQ6:/mnt/d/github/nanshaqundao/golang/examples/module1/helloworld$ ./main
os args is: [./main]
input parameter is: world
Hello world from Go

nash@LAPTOP-SP9KAOQ6:/mnt/d/github/nanshaqundao/golang/examples/module1/helloworld$
```

- in powershell with wsl support it can run as well

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> .\main
os args is: [D:\github\nanshaqundao\golang\examples\module1\helloworld\main.exe]
input parameter is: world
Hello world from Go

PS D:\github\nanshaqundao\golang\examples\module1\helloworld>
```



### go format

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go fmt .\main.go
main.go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> 
```



### go get

### go install

- compile and install packages and dependencies
- used sometimes in building image of container

### go mod

- module maintenance

### go build

- full options list in file $GOROOT/src/go/build/syslist.go

- ```
  // Copyright 2011 The Go Authors. All rights reserved.
  // Use of this source code is governed by a BSD-style
  // license that can be found in the LICENSE file.
  
  package build
  
  // List of past, present, and future known GOOS and GOARCH values.
  // Do not remove from this list, as these are used for go/build filename matching.
  
  const goosList = "aix android darwin dragonfly freebsd hurd illumos ios js linux nacl netbsd openbsd plan9 solaris windows zos "
  const goarchList = "386 amd64 amd64p32 arm armbe arm64 arm64be ppc64 ppc64le loong64 mips mipsle mips64 mips64le mips64p32 mips64p32le ppc riscv riscv64 s390 s390x sparc sparc64 wasm "
  
  ```

- Above is version 1.17.1



### go test

- sample

```go
PS D:\github\nanshaqundao\golang\examples\module1\callbacks> ls

    Directory: D:\github\nanshaqundao\golang\examples\module1\callbacks

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          30/10/2021    21:19            242 main_test.go
-a---          30/10/2021    21:19            295 main.go

PS D:\github\nanshaqundao\golang\examples\module1\callbacks> cat .\main_test.go
package main

import (
        "testing"

        "github.com/stretchr/testify/assert"
)

func add(a, b int) int {
        return a + b
}
func TestIncrease(t *testing.T) {
        t.Log("Start testing")
        result := add(1, 2)
        assert.Equal(t, result, 3)
}
PS D:\github\nanshaqundao\golang\examples\module1\callbacks> cat .\main.go     
package main

func main() {
        var a *int
        *a += 1
        // DoOperation(1, increase)
        DoOperation(1, decrease)
}

func increase(a, b int) int {
        return a + b
}

func DoOperation(y int, f func(int, int)) {
        f(y, 1)
}

func decrease(a, b int) {
        println("decrease result is:", a-b)
}
PS D:\github\nanshaqundao\golang\examples\module1\callbacks>
```

- normal test

```
PS D:\github\nanshaqundao\golang\examples\module1\callbacks> go test .\main_test.go
ok      command-line-arguments  0.030s
```

- normal test with vs code

```
Running tool: C:\Program Files\Go\bin\go.exe test -timeout 30s -run ^TestIncrease$ github.com/cncamp/golang/examples/module1/callbacks

=== RUN   TestIncrease
    d:\github\nanshaqundao\golang\examples\module1\callbacks\main_test.go:13: Start testing
--- PASS: TestIncrease (0.00s)
PASS
ok      github.com/cncamp/golang/examples/module1/callbacks     0.032s


> Test run finished at 31/10/2021, 13:31:45 <
```



- change the value in assert to make it fail

```
PS D:\github\nanshaqundao\golang\examples\module1\callbacks> go test .\main_test.go
--- FAIL: TestIncrease (0.00s)
    main_test.go:13: Start testing
    main_test.go:15:
                Error Trace:    main_test.go:15
                Error:          Not equal:
                                expected: 3
                                actual  : 4
                Test:           TestIncrease
FAIL
FAIL    command-line-arguments  0.029s
FAIL
PS D:\github\nanshaqundao\golang\examples\module1\callbacks>
```

- with goland

```
GOROOT=C:\Program Files\Go #gosetup
GOPATH=C:\Users\Nansh\go #gosetup
"C:\Program Files\Go\bin\go.exe" test -c -o C:\Users\Nansh\AppData\Local\Temp\GoLand\___main_test_go.test.exe github.com/cncamp/golang/examples/module1/callbacks #gosetup
"C:\Program Files\Go\bin\go.exe" tool test2json -t C:\Users\Nansh\AppData\Local\Temp\GoLand\___main_test_go.test.exe -test.v -test.paniconexit0 -test.run ^\QTestIncrease\E$ #gosetup
=== RUN   TestIncrease
    main_test.go:13: Start testing
--- PASS: TestIncrease (0.00s)
PASS

Process finished with the exit code 0
```



### go vet

- static code check

```go
	str := "hello world"
	fmt.Printf("%d\n", str)
```

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go vet .\main.go
# command-line-arguments
.\main.go:18:2: Printf format %d has arg str of wrong type string
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> 
```

- boolean - always true/false or unnecessary expression

```go
i := 3
fmt.Println(i != 0|| i !=1 )
```

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go build .\main.go 
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go vet .\main.go
# command-line-arguments
.\main.go:21:14: suspect or: i != 0 || i != 1
.\main.go:18:2: Printf format %d has arg str of wrong type string
```

- range issue

```go
	words := []string{"foo", "bar", "baz"}
	for _, word := range words {
		go func() {
			fmt.Println(word)
		}()
	}
```

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go build .\main.go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go vet .\main.go  
# command-line-arguments
.\main.go:26:16: loop variable word captured by func literal
```

- error check too late

```go
	res, err := http.Get("https://www.google.co.uk")
	defer res.Body.Close()
	if err != nil {
		log.Fatal(err)
	}
```

```
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go build .\main.go
PS D:\github\nanshaqundao\golang\examples\module1\helloworld> go vet .\main.go
# command-line-arguments
.\main.go:33:8: using res before checking for errors
```

### Methods and pointer indirection

Comparing the previous two programs, you might notice that functions with a pointer argument must take a pointer:

```
var v Vertex
ScaleFunc(v, 5)  // Compile error!
ScaleFunc(&v, 5) // OK
```

while methods with pointer receivers take either a value or a pointer as the receiver when they are called:

```
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
```

For the statement `v.Scale(5)`, even though `v` is a value and not a pointer, the method with the pointer receiver is called automatically. That is, as a convenience, Go interprets the statement `v.Scale(5)` as `(&v).Scale(5)` since the `Scale` method has a pointer receiver.
