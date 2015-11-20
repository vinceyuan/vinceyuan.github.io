---
layout:     post
title:      Profiling memory usage of a Go app
category:   blog
description: Use go tool pprof to profile memory usage of a Go app (to find memory leaks).
---

`net/http/pprof` is a powerful package for profiling. This [video](https://www.youtube.com/watch?v=ydWFpcoYraU) introduces how to use it to do memory profiling. Let me write down the steps.

1. Import the package in your go web project (it doesn't have to be a web app). Just add this line `import _ "net/http/pprof"` into your code. (If your app is not a web app, see the links at the end of this post.) If you are using Gin web framework, you need to use the [pprof wrapper for Gin](https://github.com/DeanThompson/ginpprof).
2. You can view statistic in browser [http://localhost:8080/debug/pprof/](http://localhost:8080/debug/pprof/)
3. Download the heap profile `curl -s http://localhost:8080/debug/pprof/heap > ~/Downloads/base.heap`
4. After a while, download the current heap profile `curl -s http://localhost:8080/debug/pprof/heap > ~/Downloads/current.heap`
5. Compare two profiles to find the difference.

    `go tool pprof --base ~/Downloads/base.heap ~/go/bin/yourapp ~/Downloads/current.heap`
    
    You can show the number of objects by using this option `-inuse_objects`
    
    `go tool pprof -inuse_objects --base ~/Downloads/base.heap ~/go/bin/yourapp ~/Downloads/current.heap`

6. Now you enter the interactive mode of pprof.

Use `top` to view the top entries (memory usage).

```
(pprof) top
13356 of -19412 total (  -69%)
      flat  flat%   sum%        cum   cum%
     10923   -56%   -56%      10923   -56%  runtime.deferproc.func1
      1365    -7%   -63%       1365    -7%  runtime.malg
       910  -4.7%   -68%        910  -4.7%  math/big.nat.make
       158 -0.81%   -69%        158 -0.81%  crypto/tls.(*Conn).readHandshake
         0    -0%   -69%          0    -0%  bufio.(*Reader).Peek
         0    -0%   -69%          0    -0%  bufio.(*Reader).Read
         0    -0%   -69%          0    -0%  bufio.(*Reader).fill
         0    -0%   -69%          0    -0%  bufio.(*Writer).Write
         0    -0%   -69%          0    -0%  bufio.(*Writer).flush
         0    -0%   -69%          0    -0%  bytes.(*Buffer).ReadFrom
```

Use `web` to visualize the graph though the web browser. Actually it generates a .svg file. You need to set your browser as the default application to open .svg file first.

You can view the source code! Use `list` command for functions shown in `top`. For example, `list runtime.deferproc.func1`

```
(pprof) list runtime.deferproc.func1
Total: -19412
ROUTINE ======================== runtime.deferproc.func1 in /usr/local/go/src/runtime/panic.go
     10923      10923 (flat, cum)   -56% of Total
         .          .     67:	sp := getcallersp(unsafe.Pointer(&siz))
         .          .     68:	argp := uintptr(unsafe.Pointer(&fn)) + unsafe.Sizeof(fn)
         .          .     69:	callerpc := getcallerpc(unsafe.Pointer(&siz))
         .          .     70:
         .          .     71:	systemstack(func() {
     10923      10923     72:		d := newdefer(siz)
         .          .     73:		if d._panic != nil {
         .          .     74:			throw("deferproc: d.panic != nil after newdefer")
         .          .     75:		}
         .          .     76:		d.fn = fn
         .          .     77:		d.pc = callerpc
```

More resources:

1. [https://golang.org/pkg/net/http/pprof/](https://golang.org/pkg/net/http/pprof/)
2. [https://blog.golang.org/profiling-go-programs](https://blog.golang.org/profiling-go-programs)

