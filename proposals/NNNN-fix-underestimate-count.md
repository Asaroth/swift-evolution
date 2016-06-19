# Fix `underestimateCount`

* Proposal: [SE-NNNN](NNNN-filename.md)
* Author: [Anton Zhilin](https://github.com/Anton3)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Require `underestimateCount` of `Sequence` to execute in `O(1)`, not `O(n)`.

Swift-evolution thread: [Discussion thread topic for that proposal](http://news.gmane.org/gmane.comp.lang.swift.evolution)

## Motivation

`Array(_ sequence: S)` constructor uses `sequence.underestimateCount()` to preallocate memory if possible.
It correctly handles the cases where `underestimateCount` is less than actual count.

On the other hand, `LazyFilterSequence` implements `underestimateCount` like this

```swift
func underestimateCount() -> Int {
  return self.reduce(0) { a, _ in return a + 1 }
}
```

In other words, it strives to return actual count, even if it means calling `predicate` twice as much:

```swift
let filtered = Array((1...5).lazy.filter { num in print("Called"); return true })  // "Called" 10 times
```

Moreover, documentation tells us that `underestimateCount` must be nondestructive.
But if we call `underestimateCount` on a lazily filtered single-pass `Sequence`, it will consume the sequence completely.

## Proposed solution

- Require `underestimateCount` of `Sequence` to execute in `O(1)`, not `O(n)`
- Fix `underestimateCount` of `LazyFilterSequence` to return `0`

## Impact on existing code

No user code broken, but new `SequenceType`s should consider returning `0` in `underestimateCount`
if there is no possibility to get `count` in `O(1)` time.
