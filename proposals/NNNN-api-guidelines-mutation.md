# Function naming guidelines and mutation

* Proposal: [SE-NNNN](https://github.com/apple/swift-evolution/blob/master/proposals/NNNN-name.md)
* Author(s): [Swift Developer](https://github.com/swiftdev)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Change API guidelines for naming of mutating / nonmutating functions:

```swift
// Before
extension Array {
  func sorted() -> [Self.Generator.Element]
  mutating func sort()
}

// After
extension Array {
  func sort() -> [Self.Generator.Element]
  mutating func sortInPlace()
}
```

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

`ed` / `ing` convention works in simple cases like `sort`.
In other places, the new convention breaks syntactic correctness, experience from other languages,
and terms of application domain.

Examples: `union` vs `unioning` (for sets), `filter` vs `filtered` (for sequences), `sum` vs `summed`

Surely I can’t be the only one who thinks that “union/unionInPlace” is clearly, obviously, and measurably superior to “formUnion/union” (or, even worse, unioning or unioned). While I’m pleased with most of the Swift API design guidelines, there are just a few places it steps too far. Here are a few quick ways to improve the guidelines, IMO:

Remove “Prefer method and function names that make use sites form grammatical English phrases.” This isn’t Objective-C. It’s time to move on. This single guideline, while superficially lovely, is causing too much strain and pain in the language. Shoehorning code into grammatical English doesn’t benefit the code or make it more understandable.

Remove “Use the “ed/ing” rule to name the nonmutating counterpart of a mutating method, e.g. x.sort()/x.sorted() and x.append(y)/x.appending(y).” Here’s another pain point. Using “in place” reads better and makes more sense. This is a clear example of not, to steal a phrase, using terminology well.

Add “When creating mutating/non-mutating counterparts, prefer names that relate to each other and use the new `MutatingCounterpart` and `NonmutatingCounterpart` document comments to connect those items”. SE-0047 simplifies the entire cross-referencing situation without having to use “form” (or as it’s read more often, “from”, as in “fromMorePerfectUnion“), “ing”, or “ed”.

Add: “Prefer nouns for methods with no side effects (or only incidental ones, like logging) and verbs for methods with significant side-effects.” When taking side effects (or their lack) into account, you want to name verby things with verby names and nouny things with nouny names, but don’t be too prescriptive.

## Proposed solution

Describe your solution to the problem. Provide examples and describe
how they work. Show how your solution is better than current
workarounds: is it cleaner, safer, or more efficient?

## Detailed design

Describe the design of the solution in detail. If it involves new
syntax in the language, show the additions and changes to the Swift
grammar. If it's a new API, show the full API and its documentation
comments detailing what it does. The detail in this section should be
sufficient for someone who is *not* one of the authors to be able to
reasonably implement the feature.

## Impact on existing code

Describe the impact that this change will have on existing code. Will some
Swift applications stop compiling due to this change? Will applications still
compile but produce different behavior than they used to? Is it
possible to migrate existing Swift code to use a new feature or API
automatically?

## Alternatives considered

Describe alternative approaches to addressing the same problem, and
why you chose this approach instead.

