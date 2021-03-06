# Language description

Waves smart contracts language is:

* Lazy
* Strong typed
* Statically typed
* Expression-based.

## Operations and constructs

* Binary operations:`>=`, `>`, `<`, `<=`, `+`, `-`, `&&`, `||`, `*`, `%`, `/`, `==`, `!=`
* Unary operations `-`, `!`
* Constants declaration via `let`
* `if-then-else` clause
* Accessing fields of any instances within predefined structures by `.`
* Accessing list element by index `[]`
* Calls of predefined functions by `()`

## Available data types

* `Long`
* `String`
* `Boolean`
* `ByteVector`
* `List[T]`
* Predefined non-recursive data structure like `Transaction` and `Block`.
* `Unit` - a type which has only one instance, `unit`.
* `Nothing`- "bottom type", no instance of this type can exist, but it can be applied on any other type.
* Union types, like `Int | String | Transaction` and `ByteVector | Unit`

### Structures

* `Point(x: Int, y: Int)`
* `Alias(name: String)`

Defining user structures are restricted in RIDE.
You can create instance of any pre-defined structure by calling the constructor.

```
let addr = Address(base58'3My3KZgFQ3CrVHgz6vGRt8687sH4oAA1qp8')
let alias = Alias("alicia")
let name  = alias.name
```

### List\[T\]

The user can't create `List[T]` instances but he can input data which contains some `List[T]` fields. All transactions contain field `proofs: List[ByteVector]` but MassTransfer transactions contain field `transfers: List[Transfer]`.

To access lists element you can use syntax `list[index]` with the first element at index 0.

To determinate count of list elements, you can use function `size`:
* `size `: `List[T] => Long`
This is also true as well for `DataType.ByteArray`:
* `size `: `DataType.ByteArray => Long`

### ByteVector
Standard ByteVector type

* `size `: `ByteVector => Long`
* `take`: `ByteVector`, `Long` => `ByteVector`
* `drop`: `ByteVector`, `Long` => `ByteVector`
* `dropRight`: `ByteVector`, `Long` => `ByteVector`
* `takeRight`: `ByteVector`, `Long` => `ByteVector`

### Long

* `fraction(value: LONG, numerator: LONG, denominator: LONG) => LONG`

### String
Standard string type

* `size `: `String => Long`
* `take`: `String`, `Long` => `String`
* `drop`: `String`, `Long` => `String`
* `takeRight`: `String`, `Long` => `String`
* `dropRight`: `String`, `Long` => `String`


## Pattern Matching

There is a mechanism for checking a value against a pattern. The user can handle different expected types in a match expression.
A match expression has:
* A value
* The match keyword.
* At least one case clause:
```
match tx {
	case t:Transfer => t.recepient
	case t:MassTransfer => t.transfers
}
```

# Examples

Here's a complete example for 2 of 3 multi-sig :

```
let alicePubKey  = base58'B1Yz7fH1bJ2gVDjyJnuyKNTdMFARkKEpV'
let bobPubKey    = base58'7hghYeWtiekfebgAcuCg9ai2NXbRreNzc'
let cooperPubKey = base58'BVqYXrapgJP9atQccdBPAgJPwHDKkh6A8'

let aliceSigned  = if(sigVerify(tx.bodyBytes, tx.proofs[0], alicePubKey  )) then 1 else 0
let bobSigned    = if(sigVerify(tx.bodyBytes, tx.proofs[1], bobPubKey    )) then 1 else 0
let cooperSigned = if(sigVerify(tx.bodyBytes, tx.proofs[2], cooperPubKey )) then 1 else 0

aliceSigned + bobSigned + cooperSigned >= 2
```

**Note** Keep in mind that all `let`'s could actually be inlined, they exist only for the sake of readability.
