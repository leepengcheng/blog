---
title: go 测试
categories: programming
tags:
  - go
date: 2017-01-16 16:35:28
---

defer常见作用是延迟加载,例如打开文件后关闭,
```golang
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
```

>多个defer越往后执行优先级越高
```golang
	for i := 0; i < 5; i++ {
		defer fmt.Printf("%d ", i)
  }
  // 输出:4 3 2 1 0
```


>example1:(z=0 > z=1 > z++ > return),defer中的`z++`是引用
```golang
func example1() (z int) {
	z = 0
	defer func() {
		z++
	}()
	return 1
}
// 输出:z=2
```
>example2:(t=5 > z=t > t=t+5 > return)
```golang
func example2() (z int) {
	t := 5
	defer func() {
		t = t + 5
	}()
	return t
}
//输出:z=5 
```

>example3:(z=1 > z*=z*+5 > return),defer中的`z`是z的值复制`z*`,故不会修改z
```golang
func example3() (z int) {
	defer func(z int) {
		z = z + 5
	}(z)
	return 1
}
//输出z=1
```