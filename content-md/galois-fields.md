# Galois Fields, from 5-10 meters

## Preface: 5-10 meters

This isn't going to be complete, in the sense that it won't contain the formalizations for groups and fields in full detail, or show the procedures for finding multiplicative inverses. These can be found elsewhere, and this exists to provide a relatively somewhat high-level understanding, but with enough detail to know what's generally going on when Galois fields are being used. I like the term "from 10,000 feet" that people use to describe very high-level overviews. This isn't going to be on the ground, but on the scale of explanations, it'll be pretty close. 

Where exactly did five to ten meters come from? Fox Kestrels!

> After hanging in the wind at 5-10 m above the ground (stationing), the falcons were able to hold their position with minimal, compensative movements of the wings and tail. 
> 
> From [The Fox Kestrel (_Falco alopex_) Hovers](https://sora.unm.edu/sites/default/files/journals/jrr/v036n03/p00236-p00237.pdf)

In case you're now expecting a ridiculous analogy between Fox Kestrels and Galois fields, I'm sorry to disappoint you, but I really can't think of anything. Anyway, on with actual math. 

## Finite Fields

A _group_ is a set $G$ of objects equipped with an operation $\circ$ that satisfies the following properties:
- Associativity: $(a \circ b) \circ c = a \circ (b \circ c)$
- Has a Neutral Element: There exists a so-called neutral element $e$, such that $e \circ a = a \circ e = a$ for all $a$
- Invertibility: Every element $a$ has an inverse element $a^{-1}$ such that $a \circ a^{-1} = e$

If a group's operation is also commutative ($a \circ b = b \circ a$), we call it an _Abelian group_. 

A _field_ is a set $F$ of equipped with two operations, $+$ and $\cdot$, with neutral elements $0$ and $1$ respectively, such that:
- The Additive Group: $(F, +)$ forms an Abelian group
- The Multiplicative Group: $F(F \setminus \{0\}, \cdot)$ forms an Abelian group
- Distributivity: $a \cdot (b + c) = (a \cdot b) + (a \cdot c)$
- Distinct Neutral Elements: $0 \neq 1$

The real numbers and rational numbers both form a field. This leads to the question of whether we can construct a field with finitely many elements. It turns out that this is only possible if the number of elements in the set is a prime number $p$ raised to some power $m \in \mathbb{N}$. These are called _Galois fields_, and the Galois field with $p^m$ elements is denoted $GF(p^m)$. 

The integers modulo some prime $p$ form a Galois field, using the normal definitions of addition and multiplication. This satisfies the $m = 1$ case, and forms the _prime fields_. When $m \neq 1$, we use _extension fields_. 

## Extension Fields

If we already have $GF(p)$, then perhaps $GF(p^m)$ could consist of $m$-dimensional vectors with components in $GF(p)$. This let's us define the additive group, but you can't multiply two vectors. However, if we make these polynomials (which are really just vectors in the basis $\{1, x, x^2, \cdots, x^m\}$), we can multiply them. The problem with this is that, for example this isn't closed. Considering this hypothetical definition applied to $GF(2^3)$, we would calculate $(x^2 + x) \cdot (x^2 + 1)$ as $x^4 + x^3 + x^2 + x$, which isn't in the field. 

Thus, just like we had to work modulo some prime number to keep prime fields closed, we need to work modulo some irreducible polynomial to keep extension fields closed. Irreducible and prime both mean "have no factors other than one and themselves", they're just used in slightly different contexts. Regardless, this means that there isn't just one way to do multiplication (or division) in an extension field. It depends on the irreducible polynomial that you select. But regardless, this is how we define the multiplication operation in extension fields: by treating them as polynomials, and working modulo some irreducible polynomial that must be given. 

One last important thing is that since the elements of $GF(2)$ are just $0$ and $1$, the extension fields $GF(2^m)$, which can be thought of as vectors on the field $GF(2)$, are just a strange representation of bitstrings. This is, in part, what makes them so useful in cryptography, but both $GF(2^m)$ and Galois fields in general have countless applications outside of that particular field (no pun intended). 
