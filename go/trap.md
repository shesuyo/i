``` go

// 这样是不可以的
func (d *Data)Destory(){
  d = &Data{}
}

//这样才是可以的
func (d *Data)Destory(){
  *d = Data{}
}

```