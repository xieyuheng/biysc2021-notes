---
title: Logic in Lean - Part 2
subtle: Day 2
---


Your mission today is to wrap up the remaining bits of logic
and move on to doing some "actual math".

Remember to **always save your work**.
You might find the [Glossary of tactics](99-appendix-a-pretty-symbols-in-lean.md) page
and the [Pretty symbols](99-appendix-b-glossary-of-tactics.md) page useful.

Before we move on to new stuff, let's understand what we did yesterday.

# Behind the scenes

**A note on brackets:**
It is not uncommon to compose half a dozen functions in Lean.
The brackets get really messy and unwieldy.
As such, Lean will often drop the brackets by following the following conventions.

* The function `P → Q → R → S` stands for `P → (Q → (R → S))`.
* The expression `a + b + c + d` stands for `((a + b) + c) + d`.

An easy way to remember this is that, arrows are bracketed on the right and binary operators on the left.

## Proof irrelevance

It might feel a bit weird to say that a proposition has proofs as its inhabitants.
Proofs can get huge and it seems unnecessary to have to remember not just the statement but also its proof.
This is something we don't normally do in math.
To hide this complication, in type theory there is an axiom, called *proof irrelevance*, which says that
if `P : Prop` and `hp1 hp2 : P` then `hp1 = hp2`.
Taking our *analogy* with sets further, you can think of a proposition as a set which is either empty or contains a single element (false or true).
In fact, in some forms of type theory (e.g. [homotopy type theory](https://en.wikipedia.org/wiki/Homotopy_type_theory)) this is taken as the definition of propositions.
This is of course not true for general types.
For example, `0 : ℕ ≠ 1 : ℕ`.


## Proofs as functions

Every time you successfully construct a proof of a theorem say

``` lean
theorem tautology (P : Prop) : P → P :=
begin
  intro hp,
  exact hp,
end
```

Lean constructs a *proof term* `tautology : ∀ P : Prop, P → P`
(you can see this by typing `#check tautology`).

In type theory, the *for all* quantifier, `∀`, is a generalized function, called a [dependent function](https://en.wikipedia.org/wiki/Dependent_type).
For all practical purposes, we can think of `tautology` as having the type `(P : Prop) → (P → P)`.
Note that this is not a function in the classical sense of the word because the codomain `(P → P)` *depends* on the input variable `P`.
If `Q : Prop`, then `tautology(Q)` is a term of type  `Q → Q`.

Consider a theorem with multiple hypothesis, say

``` lean
theorem hello_world (hp : P) (hq : Q) (hr : R) : S
```

Once we provide a proof of it, Lean will create a proof term
`hello_world : (hp:P) → (hq:Q) → (hr:R) → S`.
So that if we have terms `hp' : P`, `hq' : Q`, `hr' : R`
then `hello_world hp' hq' hr'` (note the convenient lack of brackets) will be a term of type `S`.

Once constructed, any term can be used in a later proof. For example,

``` lean
example (P Q : Prop) : (P → Q) → (P → Q) :=
begin
  exact tautology (P → Q),
end
```

This is how Lean simulates mathematics.
Every time you prove a theorem using tactics a *proof term* gets created.
Because of proof irrelevance, Lean forgets the exact content of the proof and
only remembers its type.
All the proof terms can then be used in later proofs.
All of this falls under the giant umbrella of the [Curry--Howard correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence).

We'll now continue our study of the remaining logical operators: *and* (`∧`),
*or* (`∨`),
*if and only if* (`↔`),
*for all* (`∀`),
*there exists* (`∃`).

# And / Or

The operators *and* (`∧`) and *or* (`∨`) are very easy to use in Lean.
Given a term `hpq : P ∧ Q`,
there are tactics that let you
create terms `hp : P` and `hq : Q`, and vice versa.
Similarly for `P ∨ Q`, with a subtle change (see below).

**Note** that when multiple goals are open, you are trying to solve the topmost goal.

- `cases`

  `cases` is a general tactic that breaks a complicated term into simpler ones.
   If `hpq` is a term of type `P ∧ Q`, then
   `cases hpq with hp hq,` breaks it into `hp : P` and `hp : Q`.

   If `fg` is a term of type `P ↔ Q`, then
   `cases fg with f g,` breaks it into `f : P → Q` and `g : Q → P`.

   If `hpq` is a term of type `P ∨ Q`, then
   `cases hpq with hp hq,` creates two goals and adds the hypotheses `hp : P` and `hq : Q` to one each.

- `split`

  `split` is a general tactic that breaks a complicated goal into simpler ones.

  If the target of the current goal is `P ∧ Q`, then
  `split,` breaks up the goal into two goals with targets `P` and `Q`.

  If the target of the current goal is `P × Q`, then
  `split,` breaks up the goal into two goals with targets `P` and `Q`.

  If the target of the current goal is `P ↔ Q`, then
  `split,` breaks up the goal into two goals with targets `P → Q` and `Q → P`.

- `left`

  If the target of the current goal is `P ∨ Q`, then
  `left,` changes the target to `P`.

- `right`

  If the target of the current goal is `P ∨ Q`, then
  `right,` changes the target to `Q`.


> - `cases`
>
>   `cases` is a general tactic that breaks up complicated terms.
>   If `hpq` is a term of type `P ∧ Q` or `P ∨ Q` or `P ↔ Q`, then use
>   `cases hpq with hp hq,`.
>
> - `split`
>
>   If the target of the current goal is `P ∧ Q` or `P ↔ Q`, then use
>   `split,`.
>
> - `left`/`right`
>
>   If the target of the current goal is `P ∨ Q`, then use
>   either `left,` or `right,` (choose wisely).
>
> - `exfalso`
>
>   Changes the target of the current goal to `false`.
>
> Delete the `sorry,` below and replace them with a legitimate proof.

<!--
``` lean
import tactic

-- these two statements tell Lean to use the law of excluded middle as necessary
noncomputable theory
open_locale classical
```
-->

``` lean
example (P Q : Prop) : P ∧ Q → Q ∧ P :=
begin
  sorry,
end

example (P Q : Prop) : P ∨ Q → Q ∨ P :=
begin
  sorry,
end

example (P Q R : Prop) : P ∧ false ↔ false :=
begin
  sorry,
end

theorem principle_of_explosion (P Q : Prop) : P ∧ ¬ P → Q :=
begin
  sorry,
end
```

# Quantifiers

As mentioned it the introduction the *for all* quantifier, `∀`, is a generalization of a function.
As such the tactics for dealing with `∀` are the same as those for `→`.

- `have`

  If `hp` is a term of type `∀ x : X, P x` and
  `y` is a term of type `X` then
  `have hpy := hp(y)` creates a hypothesis `hpy : P y`.

- `intro`

  If the target of the current goal is `∀ x : X, P x`, then
  `intro x,` creates a hypothesis `x : X` and
  changes the target to `P x`.

The *there exists* quantifier, `∃`, in type theory is very intuitive.
If you want to prove a statement `∃ x : X, P x` then you need to provide a witness.
If you have a term `hp : ∃ x : X, P x` then from this you can extract a witness.

- `cases`

  If `hp` is a term of type `∃ x : X, P x`, then
  `cases hp with x key,` breaks it into
  `x : X` and `key : P x`.

- `use`

  If the target of the current goal is `∃ x : X, P x`
  and `y` is a term of type `X`, then
  `use y,` changes the target to `P y` and tries to close the goal.

# Geometry

Now it's your turn! Introduce Hilbert's axioms for between-ness. We'll give you the
ones for incidence from yesterday.

``` lean
import tactic
constants Point Line : Type*
constant belongs : Point → Line → Prop
local notation A `∈` L := belongs A L
local notation A `∉` L := ¬ belongs A L

-- I1: there is a unique line passing through two distinct points.
axiom I1 (A B : Point) (h : A ≠ B) : ∃! (ℓ : Line) , A ∈ ℓ ∧ B ∈ ℓ

-- I2: any line contains at least two points.
axiom I2 (ℓ : Line) : ∃ A B : Point, A ≠ B ∧ A ∈ ℓ ∧ B ∈ ℓ

-- I3: there exists 3 non-collinear points.
axiom I3 : ∃ A B C : Point, (A ≠ B ∧ A ≠ C ∧ B ≠ C ∧ (∀ ℓ : Line, (A ∈ ℓ ∧ B ∈ ℓ) → (¬ (C ∈ ℓ) )))

-- We can make our own definitions
def collinear (A B C : Point) : Prop := ∃ (ℓ : Line), (A ∈ ℓ ∧ B ∈ ℓ ∧ C ∈ ℓ)

-- We define the between-ness relation
constant between : Point → Point → Point → Prop
local notation A `*` B `*` C := between A B C
```
