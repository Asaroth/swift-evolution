# Fix `Collection` to `Array` conversion

* Proposal: [SE-NNNN](NNNN-filename.md)
* Author: [Anton Zhilin](https://github.com/Anton3)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Make `Array.init(_:)` use `underestimatedCount` by default, and opt-in `count` for `Collection` types.

Swift-evolution thread: [Discussion thread topic for that proposal](http://news.gmane.org/gmane.comp.lang.swift.evolution)

## Motivation

`Array(_ sequence: S)` initializer behaves differently, depending on whether `sequence` is a `Collection`:

- If `sequence` is a `Collection`, then `sequence.count` is computed to preallocate memory
- If `sequence` is not a `Collection`, then `sequence.underestimatedCount` is NOT used

If `sequence` is a `Collection`, but not a `RandomAccessCollection`, then computation of `sequence.count` can take O(n) time.
That time can outweight benefits gained from preallocation of `Array` memory.
For example, `LazyFilterCollection` implements `count` like this

```swift
func count() -> Int {
  return self.reduce(0) { a, _ in return a + 1 }
}
```

In other words, it strives to return actual count, even if it means calling `predicate` twice as much:

```swift
let filtered = Array((1...5).lazy.filter { num in print("Called"); return true })  // "Called" 10 times
```

## Proposed solution

- Require `underestimatedCount` of `Sequence` to execute in `O(1)`, not `O(n)`
- Make `underestimatedCount` of `LazyFilterCollection` type and alike always return `0`
- Add initializer `init<C: Collection>(_: C, usePreciseCount: Bool = false)` to `Array`
- Make these two initializers use `underestimatedCount`, unless `usePreciseCount = true` 

## Impact on existing code

No user code broken, but new `SequenceType`s should consider returning `0` in `underestimatedCount`
if there is no possibility to get `count` in `O(1)` time.
