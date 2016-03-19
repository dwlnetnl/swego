# Swiss Ephemeris library for Go [![GoDoc](https://godoc.org/github.com/dwlnetnl/swego?status.svg)](https://godoc.org/github.com/dwlnetnl/swego)
Package swego allows access to the Swiss Ephemeris from Go.

## Implemented C functions
Currently the following subset of the C API is implemented:
- [`swe_version`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200807)
- [`swe_calc`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200740)
- [`swe_calc_ut`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200740)
- [`swe_close`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200805)
- [`swe_set_ephe_path`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200804)
- [`swe_set_jpl_file`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200806) (via arguments passed to method)
- [`swe_get_planet_name`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200762)
- [`swe_set_topo`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200753) (via arguments passed to method)
- [`swe_set_sid_mode`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200801) (via arguments passed to method)
- [`swe_get_ayanamsa`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200802) (*you should use `swe_get_ayanamsa_ex`*)
- [`swe_get_ayanamsa_ut`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200802) (*you should use `swe_get_ayanamsa_ex_ut`*)
- [`swe_get_ayanamsa_ex`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200802)
- [`swe_get_ayanamsa_ex_ut`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200802)
- `swe_get_ayanamsa_name`
- [`swe_julday`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200790)
- [`swe_revjul`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200790)
- [`swe_utc_to_jd`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200791)
- [`swe_jdet_to_utc`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200791)
- [`swe_jdut1_to_utc`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200791)
- [`swe_houses`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200809)
- [`swe_houses_ex`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200811)
- [`swe_houses_armc`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200810)
- [`swe_house_pos`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200814)
- [`swe_house_name`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200812)
- [`swe_deltat`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200796) (*you should use `swe_deltat_ex`*)
- [`swe_deltat_ex`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200795)
- [`swe_time_equ`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200793)
- [`swe_lmt_to_lat`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200793)
- [`swe_lat_to_lmt`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200793)
- [`swe_sidtime0`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200816)
- [`swe_sidtime`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200816)
- [`swe_set_tid_acc`](http://www.astro.com/swisseph/swephprg.htm#_Toc433200797) (handled internally by the C library)

### What is the deal with that _via arguments passed to method_?
The reason is to eliminate the number of calls a user has to make. This is in contrast with the C API that requires you to call `swe_set_topo` before `swe_calc`. when you are calculating the topocentric position of Venus. Only calling a single C function is important in the context of Go because you like to minimize the number of calls to C.

Currently the implementation is smart about when to call `swe_set_topo`, but it figures this out in via a C call separate of the calculation. So here's room for improvement but this can be done without changing the public API.

# Example
```go
package main

import (
	"log"

	"github.com/dwlnetnl/swego"
	"github.com/dwlnetnl/swego/swecgo"
)

func ExampleCall_calcUT() {
	swecgo.Call(nil, func(swe swego.Interface) {
		xx, cfl, err := swe.CalcUT(2451544.5, 0, swego.CalcFlags{}) // flags = 0
		if err != nil {
			log.Fatal(err)
		}

		log.Println("xx[0]", xx[0]) // xx[0] 279.8592144230896
		log.Println("xx[1]", xx[1]) // xx[1] 0.0002296532779708701
		log.Println("xx[2]", xx[2]) // xx[2] 0.9833318568951198
		log.Println("xx[3]", xx[3]) // xx[3] 0
		log.Println("xx[4]", xx[4]) // xx[4] 0
		log.Println("xx[5]", xx[5]) // xx[5] 0
		log.Println("cfl", cfl)     // cfl 2
	})
}
```
