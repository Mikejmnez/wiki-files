## Is it better to pass as a const reference, or by value and then use std::move()?

It depends.

Here's a concise explanation from Stack Overflow. There are three cases:

` /* (0) */ `
` Creature(const std::string &name) : m_name{name} { }`

A passed lvalue binds to name, then is copied into m_name.

A passed rvalue binds to name, then is copied into m_name.

` /* (1) */ `
` Creature(std::string name) : m_name{std::move(name)} { }`

A passed lvalue is copied into name, then is moved into m_name.

A passed rvalue is moved into name, then is moved into m_name.

` /* (2) */ `
` Creature(const std::string &name) : m_name{name} { }`
` Creature(std::string &&rname) : m_name{std::move(rname)} { }`

A passed lvalue binds to name, then is copied into m_name.

A passed rvalue binds to rname, then is moved into m_name.

As move operations are usually faster than copies, (1) is better than
(0) if you pass a lot of temporaries. (2) is optimal in terms of
copies/moves, but requires code repetition.

Source:
<https://stackoverflow.com/questions/51705967/advantages-of-pass-by-value-and-stdmove-over-pass-by-reference>

## What is an *lvalue*? What is an *rvalue*?

LValue: An lvalue refers to an expression that identifies a memory location and can be assigned a value. It essentially acts as a locator for data storage.

Key characteristics:

- Can appear on the left-hand side of an assignment operator (=).
- Represents a persistent object that exists beyond the evaluation of a
  single expression.

Examples:

- Variable names (e.g., x, name)
- Array elements (e.g., array\[3\])
- Member variables of objects (e.g., object.value)
- Function calls returning an lvalue reference (rare)

RValue: An rvalue represents a value itself, but it doesn't have a memory location that can be directly assigned to. It provides the data for an assignment.

Key characteristics:

- Can appear on the right-hand side of an assignment operator (=).
- Represents a temporary value that exists only during the evaluation of
  an expression.

Examples:

- Literal values (e.g., 42, "hello")
- Arithmetic expressions (e.g., 2 + 3)
- Function calls that don't return an lvalue reference
- The result of certain operators (e.g., increment/decrement)

*Every expression in a program is either an lvalue or an rvalue.*

Implicit conversions: In some cases, compilers can implicitly convert between lvalues and rvalues. For example, an lvalue can be converted to an rvalue when its value is used in an expression (e.g., x + 5).
Modern languages: While the core concepts remain similar, some modern languages like C++ introduce additional categories like *xvalues* for handling move semantics and resource management.

Source: <https://gemini.google.com/app/efaa1d88d284719d>, with edits.