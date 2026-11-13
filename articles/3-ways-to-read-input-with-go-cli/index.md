---
title: "3 ways to read input with a Go CLI"
url: https://www.mizouzie.dev/articles/3-ways-to-read-input-with-go-cli/
date: 2023-02-23
description: "Tutorial with examples showing 3 different ways to read user input when building a command line interface (CLI) with Go"
tags: ["web dev","Golang","CLI","learning","tutorial"]
related:
  - title: "MySQL Database Migration to a Remote Server from a .gz mysqldump file"
    url: https://www.mizouzie.dev/articles/mysql-database-migration-to-remote-server-from-gz-file/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  - title: "Space is the Place: Reasons to explore Twitter Spaces"
    url: https://www.mizouzie.dev/articles/space-is-the-place/index.md
---

# 3 ways to read input with a Go CLI

![Simple but Powerful command line interface](https://www.mizouzie.dev/images/command-line-interface-go.png)


Go, or Golang, is a wonderfully simple and robust language for building back end and data processing applications. In order to process data, we will need to know how to read data. Here are 3 different ways to achieve that before your application gets to crunching bytes.

## Packages

Go is a modular language with a strong standard library. We'll remain inside the realms of standard and still have a comfortable amount of options. The 2 packages that we'll look at are:

- "os"
    > Package os provides a platform-independent interface to operating system functionality.
- "bufio"
    > Package bufio implements buffered Input/Output.

Even though it is only two packages, there are at least three options here. The best part is that `os` is actually involved in all three.

Additionally, we'll call on some helper functions from 2 other standard library packages just for niceties and a little formatting when passing variables:

- "fmt"
- "strings"

## Reading Input

User input can come in a few forms. You may only need to set an environment variable like an API key or you may need to crawl through rows and rows of data in a spreadsheet. We can start small and simple.

### CLI Interaction: User Input

![CLI asking for user input](https://www.mizouzie.dev/images/go-cli-input.png)


The example above demonstrates an interactive app requesting that the user enters some input. When a relatively small value is needed, this is an easy way to get it. The user can input a string, press `Enter` and the application is able to read that string and do something with it. Let's see how this works behind the scenes.

`./main.go`
```go
package reader

import (
    "bufio"
    "os"
)

func Reader() *bufio.Reader {
    // Initiate user input reader
    reader := bufio.NewReader(os.Stdin)

    return reader
}
```

What we see here is the **initialization** of a new `Reader` type by the bufio package. This type is actually from the "io" package and it looks like this:

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
```

Bufio wraps this type in a buffer which gathers and temporarily holds the data before sending it along the line all together as one object, rather than streaming it.

We also call upon the "os" package early on, in this case for the `os.Stdin` function which effectively stands for <u>**o**</u>perating <u>**s**</u>ystem <u>**St**</u>an<u>**d**</u>ard <u>**in**</u>put. This is being called as the argument for the `NewReader()` function to tell the reader to accept input from the console.

Once created, this reader object has access to a few functions where you can specify exactly how it reads. We will use it to read the user's input from the command line. To do so we call the `ReadString()` function, in which we have to specify the delimiter which tells the function "that's the end". Let's build on what we had earlier.

`./main.go`
```go
package reader

import (
    "bufio"
    "os"
    "fmt"
)

func Reader() string {
    // Initiate user input reader
    reader := bufio.NewReader(os.Stdin)

    // Print the instruction to the reader in the console
    fmt.Println("Please enter your API key below:")

    // Call the reader to read user's input
    key, err := reader.ReadString('\n')
    if err != nil {
        panic(err)
    }

    return key
}
```

So here we can see:

1. Addition of "fmt" package just to print the prompt for the user
1. Calling the reader to return to us the user's input and an error
1. `ReadString()` expects one argument which is the delimiter, in this case the byte for a new line '\n'
1. If the error is empty (nil) we have the string value stored in the variable `key`
1. Return type for the function is now `string`

This method of reading input is nice, quick and basic. Ideal for short string and integer values. What is also interesting is that you can combine it with another method when the data gets a little larger. We read a string, so what if that string was a path to a file? Maybe a csv file.

### Follow the Path: Read a File

Nobody is going to sit and type out an entire huge data-set. We are building an application to make it faster and easier for the user to process data! So let's read directly from a file. Say we used the first method to get a **path** to a config file and we have that string value saved in a variable... what can we do with it? Call "os" yet again.

`main.go`
```go
package reader

import (
    "bufio"
    "os"
    "fmt"
    "strings"
)

func Reader() []byte {
    // Initiate user input reader
    reader := bufio.NewReader(os.Stdin)

    // Print the instruction to the reader in the console
    fmt.Println("Please enter your config file path:")

    // Call the reader to read user's input
    path, err := reader.ReadString('\n')
    if err != nil {
    	panic(err)
    }

    // Read config file from trimmed path string
    file, err := os.ReadFile(strings.TrimSpace(path))
    if err != nil {
    	panic(err)
    }

    return file
}
```

What has been added?

1. `os.Readfile()` that accepts a string as an argument. The string is the path we extracted from the user earlier
1. We use the helper `TrimSpace()` from the "strings" package to remove and whitespace characters from the start or end of the provided path
1. If the error if empty (nil) we have the read contents of the file pointed to by `path` 
1. Return type now `[]byte` as ReadFile returns as a slice of bytes in the variable `file`

This method helps to speed things up a great deal when there is a lot of data to feed into the application. There is a way, very similar to this, that we can open the file and read it line by line, but I will explain that in greater detail in another post as it allows you to do more interesting things when you involve loops and needs a little explanation regarding `Reader` types.

### Executable Arguments: Feed it from the Start

One huge benefit of Go is that it is compilable into binary files. This means that once it is written, you can export it as a stand alone program that can be run on another machine once installed. In the case of a CLI, it is often a good idea to build it to be able to accept arguments when being invoked. What this means is that you can set it that when a user runs the compiled program we're building here, they can provide the input we need as argument following the invoking command. Like this.

![Invoking the binary with an argument](https://www.mizouzie.dev/images/go-cli-args.png)


For this example, it works almost exactly the same as reading the user input. Let's go back to that simple code and see what difference there is.

`main.go`
```go
package reader

import (
    "os"
    "strings"
)

func Reader() []byte {
    // Get the path from the first argument
    path := os.Args[1]

    // Read config file from trimmed path string
    file, err := os.ReadFile(strings.TrimSpace(path))
    if err != nil {
    	panic(err)
    }

    return file
}
```

Here the difference is:

1. Calling `os.Args` is all that is needed to access the array of arguments provided at the start
1. That is assigned to a variable, which is then passed to our file reader function

You may notice that we are calling index `1` on the array of `os.Args` and this is not a mistake. The reason for this is that the very first element of the array, index `0` is always the name of the program itself. The arguments actually begin from the second index position. It is possible to accept as many as you like, but be sure to add some sort of `--help` or manual for the tool if you are expecting a large number of variables to be passed this way. It is good practice to provide decent instructions anyway.

## Summary

There you have it. Three great ways to accept data to be used, analyzed and manipulated by your Go CLI application. Each has it's merits and best use cases, but at the end of the day it is up to you how and which you use. I tend to even include a combination and use nested `if` statements to check for presence/absence of one type or the other so that an appropriate prompt can be given or omitted.

Of course, in this tutorial we have not looked at proper input sanitizing and the types of checks that can be done to ensure that a file/directory actually exists at the specified path. These are definitely a good idea to include in a real application as human error is a very real thing and a good application is a prepared application!
