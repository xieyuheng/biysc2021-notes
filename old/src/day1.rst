.. _day1:

Logic in Lean - Part 1
************************


Lean is built on top of a logic system called *type theory*, which is an alternative to *set theory*.
In type theory, instead of elements we have *terms* and every term has a *type*.
When translated to math, terms can be either mathematical objects, functions, propositions, or proofs. 
The notation ``x : X`` stands for "``x`` is a term of type ``X``" or "``x`` is an inhabitant of ``X``".
For the most part, you can think of a type as a set and terms as elements of the set.

Propositions as types
======================

In set theory, a **proposition** is any statement that has the potential of being true or false, like ``2 + 2 = 4``, ``2 + 2 = 5``, "Fermat's last theorem", or "Riemann hypothesis".
In type theory, there is a special type called ``Prop`` whose inhabitants are propositions.
Furthermore, each proposition ``P`` is itself a type and the inhabitants of ``P`` are its proofs!

.. code:: lean

    P : Prop     -- P is a proposition
    hp : P       -- hp is a proof of P

As such, in type theory "producing a proof of ``P``" is the same as "producing a term of type ``P``"
and so a proposition ``P`` is ``true`` if there exists a term ``hp`` of type ``P``.

**Notation.** Throughout these notes, ``P, Q, R, ...`` will denote propositions.

Implication 
------------
In set theory, the proposition ``P ⇒ Q`` ("P implies Q") is true if either both ``P`` and ``Q`` are true or if ``P`` is false. 
In type theory, a proof of an implication ``P ⇒ Q`` is just a function ``f : P → Q``.
Given a function ``f : P → Q``, every proof ``hp : P`` produces a proof ``f hp : Q``.
If ``P`` is false then ``P`` is *empty*, and there exists an `empty function <https://en.wikipedia.org/wiki/Function_(mathematics)#empty_function>`_ from an empty type to any type.
Hence, in type theory we use ``→`` to denote implication. 


Negation 
----------
In type theory, there is a special proposition ``false : Prop`` which has no proof (hence is *empty*).
The negation of a proposition ``¬ P`` is the implication ``P → false``.
Such a function exists if and only if ``P`` itself is empty (`empty function <https://en.wikipedia.org/wiki/Function_(mathematics)#empty_function>`_), hence ``P → false`` is inhabited if and only if ``P`` is empty which justifies using it as the definition of ``¬ P``.


**To summarize:**
  1. Proving a proposition ``P`` is equivalent to producing an inhabitant ``hp : P``. 
  2. Proving an implication ``P → Q`` is equivalent to producing a function ``f : P → Q``.
  3. The negation, ``¬ P``, is defined as the implication ``P → false``.

Propositions in Lean 
---------------------
In Lean, a proposition and its proof are written using the following syntax.

.. code:: lean

  theorem fermats_last_theorem 
    (n : ℕ) 
    (n_gt_2 : n > 2) 
    : 
    ¬ (∃ x y z : ℕ, (x^n + y^n = z^n) ∧ (x ≠ 0) ∧ (y ≠ 0) ∧ (z ≠ 0))
  := 
  begin 
    sorry,
  end


Let us parse the above statement.

* ``fermats_last_theorem`` is the name of the theorem. 
* ``(n : ℕ)`` and ``(n_gt_2 : n > 2)`` are the two *hypotheses*.
  The former says ``n`` is a natural number and the latter says that ``n_gt_2`` is a proof of ``n > 2``.
* ``:`` is the delimiter between hypotheses and targets
* ``¬ (∃ x y z : ℕ, (x^n + y^n = z^n) ∧ (x ≠ 0) ∧ (y ≠ 0) ∧ (z ≠ 0))`` is the *target* of the theorem.
* ``:= begin ... end`` contains the proof. When you start your proof, Lean opens up a goal window  for you to keep track of hypotheses and targets. 
  **Your goal is to produce a term that has the type of the target**.

  .. code:: lean

    -- example of Lean goal window
    n : ℕ, -- hypothesis 1
    n_gt_2 : n > 2 -- hypothesis 2
    ⊢ ¬∃ (x y z : ℕ), x ^ n + y ^ n = z ^ n ∧ x ≠ 0 ∧ y ≠ 0 ∧ z ≠ 0 -- target

* The commands you write between ``begin`` and ``end`` are called *tactics*. 
  ``sorry,`` is an example of a tactic. 
  **Very Important:** All tactics must end with a comma (,) .

Even though they are not explicitly displayed, 
all the theorems in the Lean library are also hypotheses that you can use to close the goal. 


Implications in Lean 
======================
We'll start learning tactics by proving implications in Lean.
In the following sections, there are tables describing what a tactic does. 
Solve the following exercises to see the tactics in action.

The first two tactics we'll learn are ``exact`` and ``intros``. 

.. list-table:: 
   :widths: 20 80
   :header-rows: 0

   * - ``exact``
     - If 
       ``P`` is the target of the current goal 
       and ``hp`` is a term of type ``P``,  
       then ``exact hp,`` will close the goal.

       Mathematically, this saying "this is *exactly* what we were required to prove".

   * - ``intro``
     - If the target of the current goal is a function ``P → Q``, 
       then ``intro hp,`` will produce a hypothesis 
       ``hp : P`` and change the target to  ``Q``.

       Mathematically, this is saying that in order to define a function from ``P`` to ``Q``,
       we first need to choose an arbitrary element of ``P``.

.. code:: lean
  
  /--------------------------------------------------------------------------

  ``exact``
    
    If ``P`` is the target of the current goal and 
    ``hp`` is a term of type ``P``, then  
    ``exact hp,`` will close the goal.


  ``intro``

    If the target of the current goal is a function ``P → Q``, then 
    ``intro hp,`` will produce a hypothesis 
    ``hp : P`` and change the target to  ``Q``.

  Delete the ``sorry,`` below and replace them with a legitimate proof.
       
  --------------------------------------------------------------------------/
  
  theorem tautology (P : Prop) (hp : P) : P :=
  begin
    sorry, 
  end

  theorem tautology' (P : Prop) : P → P :=
  begin
    sorry,
  end

  example (P Q : Prop): (P → (Q → P)) := 
  begin 
    sorry,
  end 

  -- Can you find two different ways of proving the following?
  example (P Q : Prop) : ((Q → P) → (Q → P)) := 
  begin 
    sorry,
  end 

The next two tactics are ``have`` and ``apply``.

.. list-table:: 
   :widths: 20 80
   :header-rows: 0

   * - ``have``
     - ``have`` is used to create intermediate variables. 
     
       If ``f`` is a term of type ``P → Q`` and 
       ``hp`` is a term of type ``P``, then
       ``have hq := f(hp),`` creates the hypothesis ``hq : Q`` .
     
   * - ``apply``
     - ``apply`` is used for backward reasoning. 

       If the target of the current goal is ``Q`` and 
       ``f`` is a term of type ``P → Q``, then 
       ``apply f,`` changes target to ``P``.

       Mathematically, this is equivalent to saying "because ``P`` implies ``Q``, to prove ``Q`` it suffices to prove ``P``".

Often these two tactics can be used interchangeably. 
Think of ``have`` as reasoning forward and ``apply`` as reasoning backward.
When writing a big proof, you often want a healthy combination of the two that makes the proof readable.

.. code:: lean

  /--------------------------------------------------------------------------

  ``have``
    
    If ``f`` is a term of type ``P → Q`` and 
    ``hp`` is a term of type ``P``, then
    ``have hq := f(hp),`` creates the hypothesis ``hq : Q`` .


  ``apply``

    If the target of the current goal is ``Q`` and 
    ``f`` is a term of type ``P → Q``, then 
    ``apply f,`` changes target to ``P``.

  Delete the ``sorry,`` below and replace them with a legitimate proof.

  --------------------------------------------------------------------------/

  example (P Q R : Prop) (hp : P) (f : P → Q) (g : Q → R) : R :=
  begin
    sorry,
  end

  example (P Q R S T U: Type)
  (hpq : P → Q)
  (hqr : Q → R)
  (hqt : Q → T)
  (hst : S → T)
  (htu : T → U)
  : P → U :=
  begin
    sorry,
  end

For the following exercises, recall that ``¬ P`` is defined as ``P → false``,
``¬ (¬ P)`` is ``(P → false) → false``, and so on.

.. code:: lean

  /--------------------------------------------------------------------------

  Recall that 
    ``¬ P`` is ``P → false``,
    ``¬ (¬ P)`` is ``(P → false) → false``, and so on.

  Delete the ``sorry,`` below and replace them with a legitimate proof.

  --------------------------------------------------------------------------/

  theorem self_imp_not_not_self (P : Prop) : P → ¬ (¬ P) :=
  begin
    sorry,
  end

  theorem contrapositive (P Q : Prop) : (P → Q) → (¬Q → ¬P) :=
  begin
    sorry,
  end

  example (P : Prop) : ¬ (¬ (¬ P)) → ¬ P :=
  begin
    sorry,
  end


Proof by contradiction
========================
You can prove exactly one of the converses of the above three using just ``exact``, ``intro``, ``have``, and ``apply``.
Can you find which one?

.. code:: lean

  /--------------------------------------------------------------------------

  You can prove exactly one of the following three using just 
  ``exact``, ``intro``, ``have``, and ``apply``.
  
  Can you find which one?

  --------------------------------------------------------------------------/

  theorem not_not_self_imp_self (P : Prop) : ¬ ¬ P → P:=
  begin
    sorry,
  end

  theorem contrapositive_converse (P Q : Prop) : (¬Q → ¬P) → (P → Q) :=
  begin
    sorry,
  end

  example (P : Prop) : ¬ P → ¬ ¬ ¬ P :=
  begin
    sorry,
  end

This is because it is not true that ``¬ ¬ P = P`` *by definition*, after all, 
``¬ ¬ P`` is ``(P → false) → false`` which is drastically different from ``P``.
There is an extra axiom called **the law of excluded middle** which says that 
either ``P`` is inhabited or ``¬ P`` is inhabited (and there is no *middle* option) 
and so ``P ↔ ¬ ¬ P``.
This is the axiom that allows for proofs by contradiction. 
Lean provides us the following tactics to use it.

.. list-table:: 
  :widths: 10 90
  :header-rows: 0

  * - ``exfalso``
    - Changes the target of the current goal to ``false``.
      
      The name derives from `"ex falso, quodlibet" <https://en.wikipedia.org/wiki/Principle_of_explosion>`__ which translates to "from contradiction, anything". 
      You should use this tactic when there are contradictory hypotheses present. 
  
  * - ``by_cases``
    - If ``P : Prop``, then ``by_cases P,`` creates two goals, 
      the first with a hypothesis ``hp: P`` and second with a hypothesis ``hp: ¬ P``.

      Mathematically, this is saying either ``P`` is true or ``P`` is false.
      ``by_cases`` is the most direct application of the law of excluded middle.

  * - ``by_contradiction``
    - If the target of the current goal is  ``Q``,
      then ``by_contradiction,`` changes the target to  ``false`` and 
      adds ``hnq : ¬ Q`` as a hypothesis.

      Mathematically, this is proof by contradiction. 
  
  * - ``push_neg``
    - ``push_neg,`` simplifies negations in the target. 
    
      For example, if the target of the current goal is ``¬ ¬ P``, then 
      ``push_neg,`` simplifies it to ``P``. 

      You can also push negations across a hypothesis ``hp : P`` using ``push_neg at hp,``.

  * - ``contrapose!``
    - If the target of the current goal is  ``P → Q``,
      then ``contrapose!,`` changes the target to  ``¬ Q → ¬ P``.

      If the target of the current goal is ``Q`` 
      and one of the hypotheses is ``hp : P``,
      then ``contrapose! hp,`` changes the target to  ``¬ P`` 
      and changes the hypothesis to ``hp : ¬ Q``.

      Mathematically, this is replacing the target by its contrapositive.

Even though the list is long, these tactics are almost all *obvious*.
The only two slightly unusual tactics are ``exfalso`` and ``by_cases``.
You'll see ``by_cases`` in action later. 
For the following exercises, you only require ``exfalso``, ``push_neg``, and ``contrapose!``.

.. code:: lean

  import tactic

  -- these two statements tell Lean to use the law of excluded middle as necessary
  noncomputable theory
  open_locale classical
  

  --BEGIN--


  /--------------------------------------------------------------------------

  ``exfalso``

    Changes the target of the current goal to ``false``.

  ``push_neg``
    
    ``push_neg,`` simplifies negations in the target. 
    You can push negations across a hypothesis ``hp : P`` using 
    ``push_neg at hp,``.


  ``contrapose!``

    If the target of the current goal is  ``P → Q``,
    then ``contrapose!,`` changes the target to  ``¬ Q → ¬ P``.

    If the target of the current goal is ``Q`` and
    one of the hypotheses is ``hp : P``, then 
    ``contrapose! hp,`` changes the target to  ``¬ P`` and
    changes the hypothesis to ``hp : ¬ Q``.


  Delete the ``sorry,`` below and replace them with a legitimate proof.

  --------------------------------------------------------------------------/

  theorem not_not_self_imp_self (P : Prop) : ¬ ¬ P → P:=
  begin
    sorry,
  end

  theorem contrapositive_converse (P Q : Prop) : (¬Q → ¬P) → (P → Q) :=
  begin
    sorry,
  end

  example (P : Prop) : ¬ P → ¬ ¬ ¬ P :=
  begin
    sorry,
  end

  theorem principle_of_explosion (P Q : Prop) : P → (¬ P → Q) :=
  begin
    sorry,
  end

  --END--

Geometry
============

Finally, let's do some geometry! We will introduce the incidence axioms,
and start proving some lemmas from them.

.. code:: lean

  constants Point Line : Type*
  constant belongs : Point → Line → Prop
  local notation A `∈` L := belongs A L
  local notation A `∉` L := ¬ belongs A L

Here is how we can introduce axioms.

.. code:: lean

  import tactic
  constants Point Line : Type*
  constant belongs : Point → Line → Prop
  local notation A `∈` L := belongs A L
  local notation A `∉` L := ¬ belongs A L

  --BEGIN--
  -- I1: there is a unique line passing through two distinct points.
  axiom I1 (A B : Point) (h : A ≠ B) : ∃! (ℓ : Line) , A ∈ ℓ ∧ B ∈ ℓ

  -- I2: any line contains at least two points.
  axiom I2 (ℓ : Line) : ∃ A B : Point, A ≠ B ∧ A ∈ ℓ ∧ B ∈ ℓ

  -- I3: there exists 3 non-collinear points.
  axiom I3 : ∃ A B C : Point, (A ≠ B ∧ A ≠ C ∧ B ≠ C ∧ (∀ ℓ : Line, (A ∈ ℓ ∧ B ∈ ℓ) → (¬ (C ∈ ℓ) )))
  --END--

Axiom I3 really says that there are 3 non-collinear points. We can make actually define
what it means to be collinear and prove a statement which is easier to remember.

.. code :: lean

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

  --BEGIN--
  -- We can make our own definitions
  def collinear (A B C : Point) : Prop := ∃ (ℓ : Line), (A ∈ ℓ ∧ B ∈ ℓ ∧ C ∈ ℓ)

  -- So let's prove that axiom I3 really says that there are 3 non-collinear points
  example : ∃ A B C : Point, ¬ collinear A B C :=
  begin
    sorry
  end
  --END--

In the morning we proved quite in detail the following theorem (we called Theorem 1). Before trying to prove it,
make sure that the *Lean* statement is really what the English sentence says.

.. code:: lean

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

  --BEGIN--
  -- Two distinct lines meet at most at one point
  example (r s : Line) (h : r ≠ s) (A B : Point) : A ∈ r ∧ B ∈ r ∧ A ∈ s ∧ B ∈ s → A = B :=
  begin
    sorry
  end
  --END--

Let's prove another useful lemma: given a line, there is a point outside it.

.. code:: lean

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

  --BEGIN--
  -- Use I3 to prove the following lemma
  lemma exists_point_not_on_line (ℓ : Line): ∃ A : Point, A ∉ ℓ :=
  begin
    sorry
  end

  -- Challenge: is it true for two lines? If so, prove it
  lemma exists_point_not_on_two_line (r s : Line): ∃ A : Point, A ∉ r ∧ A ∉ s :=
  begin
    sorry
  end
  --END--
