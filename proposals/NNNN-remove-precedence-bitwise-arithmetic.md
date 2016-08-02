# Remove precedence relationship between bitwise and arithmetic operators

* Proposal: [NNNN-remove-precedence-bitwise-arithmetic.md](NNNN-remove-precedence-bitwise-arithmetic.md)
* Author: [Anton Zhilin](https://github.com/Anton3)
* Status: **Awaiting review**
* Review manager: TBD

## Introduction

Enforce parentheses when bitwise and arithmetic operators stand next to each other:

```swift
1 ^ 2 + 3    // will be an error
(1 ^ 2) + 3  // ok
1 ^ (2 + 3)  // ok
```

## Motivation

**WIP**

## Proposed solution

Standard library changes:

```diff
 precedencegroup AssignmentPrecedence {
   assignment: true
   associativity: right
 }
 precedencegroup TernaryPrecedence {
   associativity: right
   higherThan: AssignmentPrecedence
 }
 precedencegroup DefaultPrecedence {
   higherThan: TernaryPrecedence
 }
 precedencegroup LogicalDisjunctionPrecedence {
   associativity: left
   higherThan: TernaryPrecedence
 }
 precedencegroup LogicalConjunctionPrecedence {
   associativity: left
   higherThan: LogicalDisjunctionPrecedence
 }
 precedencegroup ComparisonPrecedence {
   higherThan: LogicalConjunctionPrecedence
 }
 precedencegroup NilCoalescingPrecedence {
   associativity: right
   higherThan: ComparisonPrecedence
 }
 precedencegroup CastingPrecedence {
   higherThan: NilCoalescingPrecedence
 }

 precedencegroup RangeFormationPrecedence {
   higherThan: CastingPrecedence
 }

 precedencegroup AdditionPrecedence {
   associativity: left
   higherThan: RangeFormationPrecedence
 }
 precedencegroup MultiplicationPrecedence {
   associativity: left
   higherThan: AdditionPrecedence
 }

+precedencegroup BitwiseOrPrecedence {
+  associativity: left
+  higherThan: RangeFormationPrecedence
+}
+precedencegroup BitwiseAndPrecedence {
+  associativity: left
+  higherThan: BitwiseOrPrecedence
+}
 precedencegroup BitwiseShiftPrecedence {
-  higherThan: MultiplicationPrecedence
+  higherThan: BitwiseAndPrecedence
 }

 postfix operator ++
 postfix operator --
 // postfix operator !

 prefix operator ++
 prefix operator --
 prefix operator !
 prefix operator ~
 prefix operator +
 prefix operator -

 // infix operator = : AssignmentPrecedence
 infix operator *=  : AssignmentPrecedence
 infix operator /=  : AssignmentPrecedence
 infix operator %=  : AssignmentPrecedence
 infix operator +=  : AssignmentPrecedence
 infix operator -=  : AssignmentPrecedence
 infix operator <<= : AssignmentPrecedence
 infix operator >>= : AssignmentPrecedence
 infix operator &=  : AssignmentPrecedence
 infix operator ^=  : AssignmentPrecedence
 infix operator |=  : AssignmentPrecedence

 // infix operator ?: : TernaryPrecedence

 infix operator ||  : LogicalDisjunctionPrecedence

 infix operator &&  : LogicalConjunctionPrecedence

 infix operator <   : ComparisonPrecedence
 infix operator <=  : ComparisonPrecedence
 infix operator >   : ComparisonPrecedence
 infix operator >=  : ComparisonPrecedence
 infix operator ==  : ComparisonPrecedence
 infix operator !=  : ComparisonPrecedence
 infix operator === : ComparisonPrecedence
 infix operator !== : ComparisonPrecedence
 infix operator ~=  : ComparisonPrecedence

 infix operator ??  : NilCoalescingPrecedence

 // infix operator as : CastingPrecedence
 // infix operator as? : CastingPrecedence
 // infix operator as! : CastingPrecedence
 // infix operator is : CastingPrecedence

 infix operator ..< : RangeFormationPrecedence
 infix operator ... : RangeFormationPrecedence

 infix operator +   : AdditionPrecedence
 infix operator -   : AdditionPrecedence
 infix operator &+  : AdditionPrecedence
 infix operator &-  : AdditionPrecedence
-infix operator |   : AdditionPrecedence
-infix operator ^   : AdditionPrecedence

 infix operator *   : MultiplicationPrecedence
 infix operator /   : MultiplicationPrecedence
 infix operator %   : MultiplicationPrecedence
 infix operator &*  : MultiplicationPrecedence
-infix operator &   : MultiplicationPrecedence

+infix operator |   : BitwiseOrPrecedence
+infix operator ^   : BitwiseOrPrecedence

+infix operator &   : BitwiseAndPrecedence

 infix operator <<  : BitwiseShiftPrecedence
 infix operator >>  : BitwiseShiftPrecedence
```

## Impact on existing code

This is a breaking change; parentheses can be added automatically, if code places together operators that no longer have precedence relationship.
