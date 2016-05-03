
This is still a draft!

# Expressive Grammar Language 1.0
## Abstract
The Expressive Grammar Language (EGL) can be used to specify formal languages over an arbitrary alphabet.
*EGL* can describe all context free languages and by using the *Without Expression* or defining constraints even some non context free languages.

## Definitions
[ Definiton: An **Alphabet** is a set of distinguishable elements that are called **Characters** ]
[ Definition: A **String** over an *Alphabet* `A` (or just an `A`-**String**) is a finite sequence in `A` ]
[ Definition: **ASCII** refers to the *Alphabet* as defined by the ASCII charset ]

## Productions

**Whitespaces** is an *ASCII*-*String* that consists of zero or more whitespace characters (ASCII hex-codes 09, 0A, 0D, 20).
An **Identifier** is an *ASCII*-*String* that matches the regular expression `[a-zA-Z][a-zA-Z0-9]`.
A **Symbol** is an *Identifier* that is defined by a *Production*.
A **Production Rule** (or just **Production**) is an *ASCII*-*String* that starts with an *Identifier* that names the *Symbol* to be defined, followed by *Whitespaces*, the equals sign ("="), further *Whitespaces*, an *Expression*, more *Whitespaces* and a dot character (".") that ends the *Production*.

A **Grammar** over an *Alphabet* `A` consists of *Productions* and a *Symbol* called the **Start Symbol**.
An `A`-*String* is a member of the formal language described by a *Grammar* over an *Alphabet* `A`, iff it matches the *Expression* that defines the *Start Symbol*.

## Expressions
An **Expression** is an *ASCII*-*String* that describes *Strings* over a certain *Alphabet* `A`.
Each *Expression* type defines when an `A`-*String* matches such an *Expression*.
Let `S` be an `A`-*String* in the following.

These constructs are atomic *Expressions*:

* `Sym` where `Sym` is a *Symbol* defined by a *Production*. 
  Is matched by `S` iff `S` matches the *Expression* that defines `Sym`.

Depending on the used *Alphabet* `A` more atomic *Expressions* can be defined. See chapter *Unicode* for further atomic *Expressions* regarding *Unicode*.

If `Expr1` and `Expr2` are *Expressions*, the following *ASCII*-*Strings* are *Expressions* too:


* `(Expr1)`  and `Expr1` surrounded by *Whitespaces*.
Is matched by a `S` iff `S` matches `Expr1`.

* Concatenation: `Expr1 Expr2`
Is matched by `S` iff `S` is the concatenization of two `A`-*Strings* `S1` and `S2` so that `S1` matches `Expr1` and `S2` matches `Expr2`.
  
* Disjunction: `Expr1 | Expr2`
Is matched by `S` iff `S` matches `Expr1` or `Expr2`. This is not an exclusive or, thus `S` can match both expressions.

* Without: `Expr1 \ Expr2`
Is matched by `S` iff `S` matches `Expr1` but not `Expr2`.
  
* Optional: `Expr1?`
Is matched by `S` iff `S` has length zero or `S` matches `Expr1`.
  
* Star: `Expr*`
Is matched by `S` iff `S` has length zero or matches `Expr Expr*`.

* Positive Star: `Expr+`
Is matched by `S` iff `S` matches `Expr Expr*`.

## Parse Trees

Let `A` be an *Alphabet* and `S` an `A`-*String* that matches a certain *Grammar* over the *Alphabet* `A`.

[Definition: A **Text Fragment** of a *String* `Str` is a pair of two natural numbers. The first refers to the start position within `Str`, the second to the length of the *Text Fragment* which can be zero. Two *Text Fragments* are equal, if the *Strings* they are from, their positions and their lengths are equal. The **Text** of a *Text Fragment* is the substring of `Str` that starts at the given position and has the given length.  ]

Note that even though the *Text* of two *Text Fragments* can be equal, the *Text Fragments* itself do not have to.

**Parse Trees** of `S` are trees whose nodes are the *Text Fragments* of `S` together with the *Symbol* the *Text Fragment* has matched with.
The **root node** of this tree is the *Start Symbol* of the *Grammar* together with the *Text Fragment* of `S` that encloses `S`.
Children of a node are constructed by matching the node's text fragment `Tf` with the *Expression* `Expr` that defines the node's *Symbol*. For each *Symbol* `Sym` within `Expr` that is matched with a sub text fragment `Tfs` of `Tf` a new node containing `Tfs` and `Sym` is added as child node. These child nodes are ordered.

*Parse Trees* are like syntax trees yielded by context free grammars, however, *Parse Trees* do not contain terminal nodes and nodes can have arbitrary many children since symbols within *Star Expressions* can be matched multiple times.

## Unicode
[ Definition: **Unicode** refers to the *Alphabet* as defined by the *Unicode* charset. ]
Accordingly, all unicode *Characters* can be represented by their code point.

A **Unicode Printable Character** is a *Unicode Character* whose hexadecimal code point is in the following list:

* `09` (horizontal tab)
* `0A` (line feed)
* `0D` (carriage return)
* `20`, `21`, ..., `7E` 
* `85` (next line)
* `A0`, `A1`, ..., `D7FF`
* `E000`, `E001`, ..., `10FFFF`

Thus a *Unicode Printable Character* can be any unicode character excluding the surrogate block and the C0 and C1 control codes with the exception of a few whitespace characters.

Besides, for *Unicode Grammers*, the following *ASCII Strings* are valid *Expressions*:

* `#xN` where `N` is a hexadecimal integer. 
Is matched by the *Unicode-String* that represents a single character whose code point is `N`. Leading zeros in `N` are insignificant.
  
* `[a-zA-Z]`, `[#xN-#xN]`
Is matched by any *Unicode-Strings* that represent a single character with a value in the range(s) indicated (inclusive).
Only *Unicode Characters* that fall into the ASCII range can be used within the *Expression String*. When referring to a *Unicode Character* that does not, the code point notation can be used.

* `[abc]`, `[#xN#xN#xN]`
Is matched by any Char with a value among the characters enumerated. Enumerations and ranges can be mixed in one set of brackets.

* `"Str"` and `'Str'` where `Str` is an *ASCII-String*.
  Is matched by the *Unicode-String* that is equal to `Str`.