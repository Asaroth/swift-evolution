# Shadowing imported functions

* Proposal: [SE-NNNN](https://github.com/apple/swift-evolution/blob/master/proposals/NNNN-name.md)
* Author(s): [Swift Developer](https://github.com/swiftdev)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Add `@shadowing` attribute to correct declarations of imported functions.
The original function will no longer be accessible from Swift.

```swift
// Imported function (implicit declaration)
func dispatch_sync(queue: dispatch_queue_t, block: (@convention(block) () -> Void)!)

@shadowing(dispatch_sync(queue, block))
func sync(queue: dispatch_queue_t, @noescape block: @convention(block) () -> Void)
```

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

Imported functions do not always get desired annotations.
Their signature can also be inconsistent with Swift guidelines.

If that functions are part of the project, then the obvious solution would be to add all annotations useful in Swift.

But if they are part of external modules/libraries, then it may be difficult to impossible to do that.
So, there should be an easy way to add these small changes to the signature and make it look more Swift-y.

## Proposed solution

### General syntax

The only argument of `@shadowing` is call to an imported function, where arguments are internal parameters of the annotated function.

All parameters must be used in that call.
Return value of the function call in `@shadowing` is returned from the overriding function.
Types must be the same, with a few exceptions described below.

Function name, external parameter names and order of parameters may not be equal in the two functions.

Examples:

```swift
@shadowing(defineProperty(property, descriptor: descriptor))
func defineProperty(descriptor: AnyObject!, forName property: String!)

@shadowing(strcat(left, right))
func +=(left: UnsafeMutablePointer<Int8>!, right: UnsafePointer<Int8>!) -> UnsafeMutablePointer<Int8>!
```

The declared function must not have a body; it is defined implicitly by the compiler.

One of the main features of this attribute is that it is not possible to access the original function
in places other than this declaration.
If multiple `@shadowing` annotations point to the same imported function, a compilation error occurs.

### Shadowing with a non-global function

Static function can shadow an imported function as well.

Member function can shadow, using `self` as one of the arguments of function being shadowed.
If the original method was an Objective-C instance method, `self` is forwarded implicitly:

```swift
extension SomeClass {
  @shadowing(someInstanceMethod(arg))
  func someInstanceMethod(arg: Int)
  
  @shadowing(someGlobalMethod(self, arg: arg))
  func someInstanceMethod(arg: Int)
}
```

### Allowed signature modifications

The reason why `@shadowing` is needed is to allow simple correction of attributes of imported functions.
They are listed below; this list can be extended in the future.

- Turning implicitly unwrapped optional into optional or value

- Adding `@noescape` or `@autoclosure` to closure parameters

- Adding `@warn_unused_result` or `@discardableResult` to the function

- Adding `@noreturn` to the function

These modifications can be combined, for example, removing IUO from a closure parameter and marking it as `@noescape`.

### Visibility

`@shadowing` functions can be granted all usual visibility levels.
In places where such declaration is not visible, unmodified imported function is visible instead.
Example:

```c
// header.h
char* strcat(char* dest, const char* src);
```
```swift
// 1.swift
@shadowing(strcat(left, right))
private func +=(left: UnsafeMutablePointer<Int8>!, right: UnsafePointer<Int8>!) -> UnsafeMutablePointer<Int8>!

strcat(dest, src)  // error
dest += src        // ok
```
```swift
// 2.swift
strcat(dest, src)  // ok
dest += src        // error
```

## Detailed design

`@shadowing` attribute is added. Formally, Swift grammar will not change.

## Impact on existing code

This is a strictly additive feature.

## Alternatives considered

An alternative is API notes, which is a plain text file put next to the bridging header.

The advantage is that no changes to the language itself are required in this case.

```
// API notes
- Name: dispatch_apply
  SwiftName: 'OS_dispatch_queue.apply(_:self:_:)'
```
