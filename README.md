Kaito
=====

Kaito (káitəʊ) is auto-detection and decompression tool for Go.


Motivation
----------

These days, I've been working on log files with several formats; some are Gzipped,
others are Xz-compressed, and even others are just plain text. OMG! I don't mind
whatever compression format is used, but just want plain content.


Usage
-----

Just make `kaito.Reader` from another `io.Reader`, then read from it.

Example:

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"

	"github.com/Maki-Daisuke/go-kaito"
)

func main() {
	for _, f := range os.Args[1:] {
		file, err := os.Open(f)
		if err != nil {
			panic(err)
		}
		k := kaito.New(file)
		r := bufio.NewReader(k)
		for {
			line, err := r.ReadString('\n')
			if err != nil {
				if err == io.EOF {
					break
				}
				panic(err)
			}
			fmt.Print(line) // `Here, line is decompressed string if the file is compressed, as-is otherwise.
		}
	}
}
```

You can make it from any kind of `io.Reader`. For example, you can easily
implement your own filter command with reading STDIN:

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"os"

	"github.com/Maki-Daisuke/go-kaito"
)

func main() {
	k := kaito.New(os.Stdin)
	r := bufio.NewReader(k)
	for {
		line, err := r.ReadString('\n')
		if err != nil {
			if err == io.EOF {
				break
			}
			panic(err)
		}
		// Do what you want to do.
	}
}
```


Prerequisites
---------------

It is recommended to install `gzip`, `bzip2` and `xz` commands. 
Kaito tries to use these commands, and fallbacks to Go-native implementation of 
those algorithms if the commands fail. 
Thus, those commands are not mandatory, but they are much faster than Go-native 
implementations according to my experience.


License
-------

The Simplified BSD License (2-clause)


Author
---------

Daisuke (yet another) Maki
