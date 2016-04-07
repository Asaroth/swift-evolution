# Shadowing import

* Proposal: [SE-NNNN](https://github.com/apple/swift-evolution/blob/master/proposals/NNNN-name.md)
* Author(s): [Anton3](https://github.com/Anton3)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Add an annotation that allows to "fix" interface of imported C functions:

```swift
extension OS_dispatch_queue {
    @shadowingImportC(dispatch_sync(self, block))
    func sync(@noescape block: @convention(block) () -> Void)
}
```

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

TODO

## Proposed solution

TODO

## Detailed design

TODO

## Impact on existing code

It is a strictly additive feature and will not break any existing code. 

## Alternatives considered

None currently.
