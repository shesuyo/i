``` go
package main

import (
	"encoding/json"
	"encoding/xml"
	"fmt"
)

type X struct {
	Text string
}

func main() {

	a := X{"mosschou"}
	{
		bs, err := xml.Marshal(&a)
		fmt.Println(err)
		fmt.Println(string(bs))
		// <X><Text>mosschou</Text></X>
	}
	{
		bs, err := json.Marshal(&a)
		fmt.Println(err)
		fmt.Println(string(bs))
		// {"Text":"mosschou"}
	}

}


	return
	app.Service()
```