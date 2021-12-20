.. _introduction:

*****************
Introduction
*****************


What is Lean?
===============
`Lean <https://leanprover.github.io/about/>`__ is an open source proof-checker and a proof-assistant. 
One can *explain* mathematical proofs to it and it can check their correctness.
It also simplifies the proof writing process by providing ``goals`` and ``tactics``.

Lean is built on top of a formal system called type theory.
In type theory, the basic notions are "terms" and "types" --- compare to "elements" and "sets" in set theory.
Every term has a type, and types are just a special kind of term.
Terms can be interpreted as mathematical objects, functions, propositions, or proofs.
The only two things Lean can do is *create* terms and *check* their types.
By iterating these two operations, we can teach Lean to verify complex mathematical proofs. For instance,
below you can see the statement of Fermat's Last Theorem coded in Lean, which states that the equation
:math:`x^n+y^n=z^n` has no nontrivial solutions in the integers when :math:`n \in \mathbb{N}` is greater than 2.


.. code-block:: lean

  def x := 2 + 2                                  -- a natural number
  def f (x : ℕ) := x + 3                          -- a function
  def easy_theorem_statement := 2 + 2 = 4         -- a proposition
  def fermats_last_theorem_statement              -- another proposition
    :=
    ∀ n : ℕ,
    n > 2
    →
    ¬ (∃ x y z : ℕ, (x^n + y^n = z^n) ∧ (x ≠ 0) ∧ (y ≠ 0) ∧ (z ≠ 0))

  theorem
  easy_proof : easy_theorem_statement             -- proof of easy_theorem
  :=
  begin
    exact rfl,
  end

  theorem 
  my_hard_proof : fermats_last_theorem_statement     -- cheating!
  :=
  begin
    sorry,
  end

  #check x
  #check f
  #check easy_theorem_statement
  #check fermats_last_theorem_statement 
  #check easy_proof
  #check hard_proof


How to use these notes 
=========================
Every once in a while, you will see a code snippet like this:

.. code-block:: lean

    #eval "Hello, World!"

Clicking on the ``try it!`` button in the upper right corner will
open a copy in a window
so that you can edit it,
and Lean provides feedback in the ``Lean Infoview`` window.
We use this feature to provide exercises inline in the notes. 
We recommend attempting each exercise as you go along.

These notes are based a 5-day Lean crash course at Mathcamp 2020. We have adapted them to
BIYSC 2021.

These notes provide a sneak-peek into the world of theorem proving in Lean and are by no means comprehensive.
It is recommended that you simultaneously attempt the `Natural Number Game`_.
It is a fun (and highly addictive!) game that proves same basic properties of natural numbers in Lean.

Acknowledgments.
===================
These notes are based on work of `Apurva Nakade <https://apurvanakade.github.io>`__ and `Jalex Stark <https://jalexstark.com/>`__.
Large chunks of these notes are taken directly from <https://apurvanakade.github.io/courses/lean_at_MC2020/>`__.


Useful Links.
==================
#. `Formalizing 100 theorems <http://www.cs.ru.nl/~freek/100/index.html>`__
#. `Formalizing 100 theorems in Lean <https://leanprover-community.github.io/100.html>`__
#. Articles, videos, blog posts, etc. 
    #. `The Xena Project <https://xenaproject.wordpress.com/>`__
    #. `The Mechanization of Mathematics`_ 
    #. `The Future of Mathematics`_
#. `Lean Zulip chat group`_

.. _`The Mechanization of Mathematics`: https://www.ams.org/journals/notices/201806/rnoti-p681.pdf
.. _`The Future of Mathematics`: https://www.youtube.com/watch?v=Dp-mQ3HxgDE
.. _`Lecture videos from LFTCM 2020`: https://www.youtube.com/playlist?list=PLlF-CfQhukNlxexiNJErGJd2dte_J1t1N
.. _Lean: https://leanprover.github.io/people/
.. _mathlib: https://leanprover-community.github.io/
.. _`Natural Number Game`: https://wwwf.imperial.ac.uk/~buzzard/xena/natural_number_game/
.. _`mathlib repository`: https://github.com/leanprover-community/mathlib
.. _`Lean Zulip chat group`: https://leanprover.zulipchat.com/
