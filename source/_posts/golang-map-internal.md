---
title: the map internal of Golang
date: 2019-02-10 00:30:47
tags: Golang
---
golang map is implemented in hashmap.
a hashmap is combined with a hash table array and each array node is a linked list.

unlike C++, golang is using a fixed size of array to replace linked list, and if there
are conflicts of new key-value pairs, golang will put the key-value pairs into a 
'extra' bucket.

the bucket is defined,

```c++
// A bucket for a Go map.
type bmap struct {
	// tophash generally contains the top byte of the hash value
	// for each key in this bucket. If tophash[0] < minTopHash,
	// tophash[0] is a bucket evacuation state instead.
	tophash [bucketCnt]uint8
	// Followed by bucketCnt keys and then bucketCnt values.
	// NOTE: packing all the keys together and then all the values together makes the
	// code a bit more complicated than alternating key/value/key/value/... but it allows
	// us to eliminate padding which would be needed for, e.g., map[int64]int8.
	// Followed by an overflow pointer.
}
```

the map is defined as 'hmap',

```c++
// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/gc/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}
```

when to access the element,(remove unrelated code)
```
func mapaccess1(t *maptype, h *hmap, key unsafe.Pointer) unsafe.Pointer

    //hash function to calculate the index of the bucket,

	hash := alg.hash(key, uintptr(h.hash0)) 
	m := bucketMask(h.B)

    bucketloop:

    //find the element from current bucket,
	for ; b != nil; b = b.overflow(t) {
		for i := uintptr(0); i < bucketCnt; i++ {
			if b.tophash[i] != top {
				if b.tophash[i] == emptyRest {
					break bucketloop
				}
				continue
			}
			k := add(unsafe.Pointer(b), dataOffset+i*uintptr(t.keysize))
			if t.indirectkey() {
				k = *((*unsafe.Pointer)(k))
			}
			if alg.equal(key, k) {
				v := add(unsafe.Pointer(b), dataOffset+bucketCnt*uintptr(t.keysize)+i*uintptr(t.valuesize))
				if t.indirectvalue() {
					v = *((*unsafe.Pointer)(v))
				}
				return v
			}
		}
	}
```
from the source code, we can understand why map is not safe.
And new version of golang has added new sync map.
https://golang.org/pkg/sync/#Map

Additonally,
map is passed by the address,
```
func makemap(t *maptype, hint int, h *hmap) *hmap
```

```
func f1(m map[string]string) {
	m["name"] = "bingo"
}

func f0() {
	m := make(map[string]string)
	m["name"] = "John"
	f1(m)
	fmt.Println(m["name"])
}
```
above code will change the value of map element, this is different with slice.