### Hi there 👋, I invited ...

![](https://projectapario.com/assets/2023Logo_666w-e2f2736f012bf796e58e2f911b4d6b55ce0558299725e1cb3d83cbbc6ad3dcaf.png)

**which is written in Ruby on Rails and supported by MongoDB and Redis.**

I am actively working on rewriting the application in Go.

It's easier said than done. Nevertheless, the learning opportunities presented are exciting.

**safe_close.go**
```go
package main

func safeClose[T any](ch chan T) (closed bool) {
	// allow panic
	defer func() {
		if r := recover(); r != nil {
			closed = true
			return
		}
	}()
	_, chOpen := <-ch
	if chOpen {
		closed = true
		close(ch)
	} else {
		closed = false
	}
	return
}
````

**safe_close_test.go**
```go
package main

import (
	"github.com/stretchr/testify/assert"
	"testing"
)

func Test_safeClose(t *testing.T) {
	type args[T any] struct {
		ch chan T
	}
	type testCase[T any] struct {
		name string
		args args[T]
		want bool
	}

	// open channel with bool in buffer
	ch1 := make(chan bool, 1)
	ch1 <- true

	// closed channel with bool in buffer
	ch2 := make(chan bool, 1)
	ch2 <- false
	close(ch2)

	// closed channel with empty buffer
	ch3 := make(chan bool, 1)
	close(ch3)

	tests := []testCase[bool]{
		{
			name: "open channel with bool in buffer",
			args: args[bool]{ch1},
			want: true,
		},
		{
			name: "closed channel with bool in buffer",
			args: args[bool]{ch2},
			want: true,
		},
		{
			name: "closed channel with empty buffer",
			args: args[bool]{ch3},
			want: false,
		},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			assert.Equal(t, tt.want, safeClose(tt.args.ch))
		})
	}
}

```

Then **go** run it....


```sh
go test .
```

Which returns:

```txt
=== RUN   Test_safeClose
--- PASS: Test_safeClose (0.00s)
=== RUN   Test_safeClose/open_channel_with_bool_in_buffer
    --- PASS: Test_safeClose/open_channel_with_bool_in_buffer (0.00s)
=== RUN   Test_safeClose/closed_channel_with_bool_in_buffer
    --- PASS: Test_safeClose/closed_channel_with_bool_in_buffer (0.00s)
=== RUN   Test_safeClose/closed_channel_with_empty_buffer
    --- PASS: Test_safeClose/closed_channel_with_empty_buffer (0.00s)
PASS

Process finished with the exit code 0
```

> [Check it out in the Go Playground...](https://go.dev/play/p/Z3HoVxJysLv)

<a href='https://ko-fi.com/P5P36GT3H' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://storage.ko-fi.com/cdn/kofi2.png?v=3' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

Why do I need this functionality? How does this `for select` statement look to you?

```go
// wait for results
for {
	select {
	case <-ctx.Done(): // parent context canceled
		if ctx.Err() != nil {
			log.Println(ctx.Err()) // with error
		}
		closeEmAll(false)
		return ImportedAssetDownloadOutput{} // exit out of the func

	case d, chOk := <-documentResults: // documents finished!
		if chOk { // open channel
			dmu.Lock()
			documents = d                 // set documents
			documentsReceived.Store(true) // mark flag
			dmu.Unlock()
			closeAllDocuments(false)
		}

	case p, chOk := <-pageResults: // pages finished!
		if chOk { // open channel
			pmu.Lock()
			pages = p                 // set pages
			pagesReceived.Store(true) // mark flag
			pmu.Unlock()
			closeAllPages(false)
		}

	default: // when both aren't completed yet
		if documentsReceived.Load() && pagesReceived.Load() { // both received
			output := ImportedAssetDownloadOutput{ // structure the output
				ImportedDocumentsDownloadOutput: documents,
				ImportedPagesDownloadOutput:     pages,
			}

			log.Printf("total documents = %d\ntotal pages = %d\n", len(output.Documents), len(output.Pages))
			closeEmAll(true)
			return output
		}
	}
}
```



<!--
**andreimerlescu/andreimerlescu** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
