---
layout:     post
title:      leetcode_solutions_go
category:   project
description: leetcode_solutions_go is a set of my solutions to the programming challenges at LeetCode, implemented in Go. Originally, I implemented solutions in C++/C, and then re-wrote them with Go. Please go to solutions in C++/C for the description of each problem.
---

###About
`leetcode_solutions_go` is a set of my solutions to the programming challenges at [LeetCode](http://leetcode.com), implemented in Go. Originally, I implemented [solutions in C++/C](http://github.com/vinceyuan/leetcode_solutions), and then re-wrote them with Go. Please go to solutions in C++/C for the description of each problem.

###Run
`go test`

###Debug
We can use `gcc` to debug Go programs. However, `go test -c` generated test executable always has optimization. We can't debug the test executable directly. We have to copy code to main.go and compile code without optimization by `go build -gcflags "-N -l"`. Then run `gcc leetcode_solutions_go`. I found `gcc -tui leetcode_solutions_go` is better. 

###Performance (Go vs C++/C)
I compaired the performance after I converted 50 questions from C++/C to Go. `time ./main` for C++/C version shows `real	0m0.010s`, and `go test` for Go version shows `ok  	github.com/vinceyuan/leetcode_solutions_go	0.049s` on my MacBook Pro 13-inch 2012. I think one of the reasons the Go version is much slower, is I use slices to replace vector/stack/queue of C++.

###License
MIT License
Copyright (c) 2015 Vince Yuan (vince.yuan###gmail.com)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
