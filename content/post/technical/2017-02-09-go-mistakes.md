---
title:  "Go Recipes For the Lost"
date:   2017-02-09 15:04:23
categories: [technical]
tags: [Golang, Coding]
---

Welcome to "Go Recipes For the Lost" post, this is a live post, it won't ever be done as long as I am writing go code. The intention here is to share with you some fast resolution for common Go coding problems. Let's begin.


### Undefined function even though it is declared in another file!
When you would like to use functions defined in another go file, and you are using the "run" command. Make sure to pass all the file names to it.

```bash
├── main.go
└── utils.go

go run main.go utils.go
```
It is preferrable to use ```go build``` or ```go install``` instead of "run".


### Can't cast an slice of interfaces to a slice of another type!
Since I don't have a better answer, I copied this answer from [Stephen Weinberg's answer on  stackoverflow](http://stackoverflow.com/questions/12753805/type-converting-slices-of-interfaces-in-go):

```
In Go, there is a general rule that syntax should not hide complex/costly operations. Converting a string to an interface{} is done in O(1) time. Converting a []string to an interface{} is also done in O(1) time since a slice is still one value. However, converting a []string to an []interface{} is O(n) time because each element of the slice must be converted to an interface{}.

The one exception to this rule is converting strings. When converting a string to and from a []byte or a []rune, Go does O(n) work even though conversions are "syntax".

 There is no standard library function that will do this conversion for you. You could make one with reflect, but it would be slower than the three line option.
```

 Example with reflection:

```go
func InterfaceSlice(slice interface{}) []interface{} {
    s := reflect.ValueOf(slice)
    if s.Kind() != reflect.Slice {
        panic("InterfaceSlice() given a non-slice type")
    }

    ret := make([]interface{}, s.Len())

    for i:=0; i<s.Len(); i++ {
        ret[i] = s.Index(i).Interface()
    }

    return ret
}
```

Your best option though is just to use those three lines of code:

```go
b := make([]interface{}, len(a))
for i := range a {
    b[i] = a[i]
}
```

### Godep command is not found
If you are wondering why you can't execute Godep, it is because you did not
export "GOBIN". Here is what you need to do:

```bash
export GOBIN=$GOPATH/bin
export PATH=$GOPATH:$GOBIN:$PATH
```

you can now execute ```godep save``` and immendiately find the "Godeps/" directory there.


### Why are my structs not being correctly marshalled into json?
When marshalling structs, you should export your struct parameters by making them
public. For example:

```go
type Test struct{
  a string
  b int
  }

//That wont work
json.Marshal(test{a: "hello", b: 10})

type Test struct{
  A string //Public
  B int  //Public
  }

//This would work
json.Marshal(test{A: "hello", B: 10})
```

### What is go get <library> ...?

Using go get will download the library you are asking for, usually it fetches that from github, gitlab, or similar project hosting sites. The ```...``` asks go to get in subsidary projects recursively.


### How to resize a slice for reception?
Sometimes when you are writing networking application, you are not sure about the size of the data
received, as a result your fixed capacity will result in an error. So how to fix that? Do the following:

```go
bytestToReceive := make([]byte, 1024)
yourModel := &models.structToUnmarshalTo{}

// Now get the size of the your data
size, err := yourConnection.Read(bytestToReceive)

//resize your receiver byte array
bytestToReceiveResized := bytestToReceive[:size]

//Unmarshal your stuff for e.g.,
json.Unmarshal(bytestToReceiveResized,yourModel)
```


### How to do a DELETE REST request ing Go?
The http library does not support a DELETE function. However, it provides a "NewRequest" call that you can utilize
to issue a delete request with the help of the http.Client library, here is how:

```go
request, err := http.NewRequest("DELETE", url, nil)
if err!=nil{
//Handle Error
}

netClient:= &http.Client{//configure your options}

_, err = netClient.Do(request)
if err!=nil{
//Handle Error
}
```


### How to use glide for dependency management?
You need to follow these steps:

```bash
glide init // this will create a glide.yml and a .lock file which contains all your dependencies
glide up // you updated your code with new dependencies, run this if you want .lock file to be updated
glide install //this reads all your dependencies from the .lock file and installs them
```
