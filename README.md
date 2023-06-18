<a href="https://github.com/romshark/jscan/actions?query=workflow%3ACI">
    <img src="https://github.com/romshark/jscan/workflows/CI/badge.svg" alt="GitHub Actions: CI">
</a>
<a href="https://coveralls.io/github/romshark/jscan">
    <img src="https://coveralls.io/repos/github/romshark/jscan/badge.svg" alt="Coverage Status" />
</a>
<a href="https://goreportcard.com/report/github.com/romshark/jscan">
    <img src="https://goreportcard.com/badge/github.com/romshark/jscan" alt="GoReportCard">
</a>
<a href="https://pkg.go.dev/github.com/romshark/jscan/v2">
    <img src="https://pkg.go.dev/badge/github.com/romshark/jscan/v2.svg" alt="Go Reference">
</a>

# jscan
jscan provides high-performance zero-allocation JSON iterator and validator for Go. This module doesn't provide `Marshal`/`Unmarshal` capabilities, instead it focuses on highly efficient iteration over JSON data with on-the-fly validation.

jscan is tested against https://github.com/nst/JSONTestSuite, a comprehensive test suite for [RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259) compliant JSON parsers.

See [jscan-benchmark](https://github.com/romshark/jscan-benchmark) for benchmark results 🏎️ 🏁.

## Example
https://go.dev/play/p/moP3l9EkebF

```go
package main

import (
	"fmt"

	"github.com/romshark/jscan/v2"
)

func main() {
	j := `{
		"s": "value",
		"t": true,
		"f": false,
		"0": null,
		"n": -9.123e3,
		"o0": {},
		"a0": [],
		"o": {
			"k": "\"v\"",
			"a": [
				true,
				null,
				"item",
				-67.02e9,
				["foo"]
			]
		},
		"a3": [
			0,
			{
				"a3.a3":8
			}
		]
	}`

	err := jscan.Scan(j, func(i *jscan.Iterator[string]) (err bool) {
		fmt.Printf("%q:\n", i.Pointer())
		fmt.Printf("├─ valueType:  %s\n", i.ValueType().String())
		if k := i.Key(); k != "" {
			fmt.Printf("├─ key:        %q\n", k[1:len(k)-1])
		}
		if ai := i.ArrayIndex(); ai != -1 {
			fmt.Printf("├─ arrayIndex: %d\n", ai)
		}
		if v := i.Value(); v != "" {
			fmt.Printf("├─ value:      %q\n", v)
		}
		fmt.Printf("└─ level:      %d\n", i.Level())
		return false // Resume scanning
	})

	if err.IsErr() {
		fmt.Printf("ERR: %s\n", err)
		return
	}
}
```
