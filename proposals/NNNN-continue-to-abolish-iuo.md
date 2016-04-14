# Continue to abolish Implicitly Unwrapped Optional

* Proposal: [SE-NNNN](https://github.com/apple/swift-evolution/blob/master/proposals/NNNN-name.md)
* Author(s): [Swift Developer](https://github.com/swiftdev)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Proposal `0054` mentions `@autounwrapped` attribute, but it is not actually allowed for use in Swift.
This proposal adds it, so that the following code is valid:

```swift
func toInt(@autounwrapped string: String) -> @autounwrapped Int?
@IBOutlet @autounwrapped weak let button: UIButton?
```

It also removes the ability to use `T!` notation in Swift. This notation will still be used in imports.

```swift
let x: Int!  // error
func defineProperty(property: String!, descriptor: AnyObject!)  // ok
```

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

With acceptance of `0054`, notation of implicitly unwrapped optional in Swift became confusing.
It is not a type -- it is actually an attribute which cannot be named -- but it has type syntax.

`Optional`, `Array` and `Dictionary` are simpler and more understandable, because we have desugared syntax.
There should be fewer black boxes and magic in the language, when possible.

Implicitly unwrapped optional has few use cases in Swift. One of them is "delayed initialization" pattern.
Explicit denotion of this pattern would be helpful there.

Other than that, `T!` notation is primarily used in imported functions.
The critical requirement to syntax there is brevity.
Declarations of such imported functions must not get bold and cry for a Swift wrapper.

## Proposed solution

### `@autounwrapped`

`@autounwrapped` attribute can be applied to variable declarations, parameters and return types of functions.

Type of annotated variable must be `Optional<T>` for some `T`, in line with `@autoclosure`. Examples:

```swift
@autounwrapped var x: Int?
@IBOutlet @autounwrapped weak var button: UIButton?
func toInt(@autounwrapped string: String?) -> @autounwrapped Int?
```

### Restricting `T!` notation

`T!` notation will no longer be allowed in Swift code.
It will still appear in signatures of imported functions and variables.

```swift
var x: Int!  // error, but ok in imported entities:
func defineProperty(property: String!, descriptor: AnyObject!)
func strcat(_: UnsafeMutablePointer<Int8>!, _: UnsafePointer<Int8>!) -> UnsafeMutablePointer<Int8>!
```

## Detailed design

`@autoclosure` attribute will be added for variable declarations, parameters and return types of functions.

`T!` notation in any Swift file will be an error, but it will not be completely purged from the language.

## Impact on existing code

Migrator will replace uses of `T!` in Swift with `@autounwrapped`.
