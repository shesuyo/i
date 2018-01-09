``` go
fmt.Println(math.MaxInt8)   // 127
fmt.Println(1<<7 - 1)       // 127
fmt.Println(1<<8 - 1)       // 255
fmt.Println(2 << 10)        // 2048 2^10 = 1024
fmt.Println(math.MaxUint8)  // 255
fmt.Println(math.MaxInt16)  // 32767
fmt.Println(math.MaxUint16) // 65535
fmt.Println(math.MaxInt32)  // 2147483647
fmt.Println(math.MaxUint32) // 4294967295
fmt.Println(math.MaxInt64)  // 9223372036854775807
//	fmt.Println(math.MaxUint64) // 这个会报错 constant 18446744073709551615 overflows int
fmt.Println(1<<63 - 1)          // 1<<63就会overflows
fmt.Println(1 < math.MaxUint64) // 不能打印，但是可以比较。
```