.. code::

  /- Incidence axioms -/
  (I1 {A B : Ω} (h : A ≠ B) : ∃! (ℓ : Line Ω), ( A ∈ ℓ ) ∧ ( B ∈ ℓ ))
  (I2 (ℓ : Line Ω) : ∃ A B : Ω, A ≠ B ∧ A ∈ ℓ ∧ B ∈ ℓ)
  (I3 : ∃ A B C : Ω, A ≠ B ∧ A ≠ C ∧ B ≠ C ∧ ∀ ℓ : Line Ω, A ∈ ℓ ∧ B ∈ ℓ → ¬ C ∈ ℓ)

  /- Betweenness is symmetric -/
  (B11 {A B C : Point} : (A * B * C) → (C * B * A))
  /- If A * B * C then the three points are distinct and collinear. -/
  (B12 {A B C : Point} : (A * B * C) → (A ≠ B ∧ A ≠ C ∧ B ≠ C ∧ collinear A B C))
  /- Given two distinct points A, B, there is a third point C such that A * B * C.-/
  (B2 {A B : Point} (h: A ≠ B) : ∃ C, A * B * C)
  /- Given 3 distinct collinear points A B C, exactly one of them is between the other two.-/
  (B3 {A B C : Point} (h: collinear A B C) :
    xor3 (A * B * C) ( B * A * C ) (A * C * B))

  (B4 {A B C D : Point} {ℓ : Line} -- Pasch
    (hnc: ¬ collinear A B C)
    (hnAl: ¬ (A ∈ ℓ)) (hnBl: ¬ B ∈ ℓ) (hnCl: ¬ C ∈ ℓ)
    (hDl: D ∈ ℓ) (hADB: A * D * B) :
            (∃ E ,  E ∈ ℓ ∧ (A * E * C)) xor (∃ E, E ∈ ℓ ∧ (B * E * C)))

  /- Given a segment AB and a ray C->D, there is a unique point E on C->D such that
    AB ≅ CE. -/
  (C1 (S : Segment Point) (r : Ray Point) : 
    ∃! E, (E ∈ pts r) ∧ ((S.A⬝S.B) ≅ (r.origin⬝E)))
  /- Congruence of segments is reverse-transitive -/
  (C21 (S T U): (S ≅ T) → (S ≅ U) → (T ≅ U))
  /- Congruence of segments is reflexive.
    Note that this together with C21 implies symmetry of congruence. -/
  (C22 (S) : S ≅ S)
  /- Congruence of segments respects the notion of sum of segments -/
  (C3 (A B C D E F) : (A * B * C) → (D * E * F) →
    ((A⬝B) ≅ (D⬝E)) → ((B⬝C) ≅ (E⬝F)) → ((A⬝C) ≅ (D⬝F)))
  /- Given a nondegenerate angle α, a segment AB, and a point s,
    construct a point E on the same side as s, such that ∟EAB ≃ α. -/
  (C4 (α : Angle Point) (S : Ray Point) (s : Point)
    (hα : α.nondegenerate) (hs : ¬ collinear s S.origin S.target)
    (hr : S.origin ≠ S.target) :  ∃! E : Point,
    (∟ E S.origin S.target ≃ α) ∧ 
    ∀ x, (E * x * s) → ∀ (ℓ : Line), S.origin ∈ ℓ ∧ S.target ∈ ℓ 
    → ¬ x ∈ ℓ)
  /- Congruence of angles is reverse-transitive.-/
  (C5 (α β γ : Angle Point) : α ≃ β → α ≃ γ → β ≃ γ)
  /- Given triangles T and T', if they have two sides and their middle angle
    congruent, then the third sides and the other two angles are also congruent.-/
  (C6 (A B C A' B' C') : (A⬝B ≅ A'⬝B') → (A⬝C ≅ A'⬝C') → (∟ B A C ≃ ∟ B' A' C') →
    (B⬝C ≅ B'⬝C') ∧ (∟ A B C ≃ ∟ A' B' C') ∧ (∟ A C B ≃ ∟ A' C' B'))
