---
parent: "[[001 Proc Math Problems]]"
spawned_by: "[[002 Proc Rational Parameterization of Circle]]"
context_type: entry
---

Parent: [[001 Proc Math Problems]]

Spawned by: [[002 Proc Rational Parameterization of Circle]]

Spawned in: [[002 Proc Rational Parameterization of Circle#^spawn-entry-b771b9|^spawn-entry-b771b9]]

# 1 Journal

Errata for [[003 Rational Parameterization of the circle]]

## 1.1 Errata 1

2026-05-11 Wk 20 Mon - 19:16 +03:00

(updating)

```haskell
module
	{- ... -}
	where
		E₂ : y₁ ≡ h (x₁ + 1) ↔ y₁ ≡ {! 2!}
		E₂ =
			y₁ ≡ h (x₁ + 1)                      ↔⟨ ap-≡ᵣ (ap (λ a → h (a + 1)) E₁ᵣ) ⟩

				   1 - h²
			y₁ ≡ h –––––– + 1                  
				   1 + h²                        ↔⟨ _ ⟩
					
				   1 - h²   1 + h²
			y₁ ≡ h –––––– + ––––––             
				   1 + h²   1 + h²               ↔⟨ _ ⟩
					
				     2
			y₁ ≡ h ––––––
				   1 + h²                        ↔⟨ _ ⟩
					
				   2h
			y₁ ≡ ––––––             
			     1 + h²                          ↔∎
```

(/updating)

This is wrong.  The h was ignored in that addition of fractions:

(1) 
```haskell
				   1 - h²   1 + h²
			y₁ ≡ h –––––– + ––––––             
				   1 + h²   1 + h²               ↔⟨ _ ⟩
					
				     2
			y₁ ≡ h ––––––
				   1 + h²                        ↔⟨ _ ⟩
```

You would get

```haskell
				  h + 1
			y₁ ≡  ––––––
				  1 + h²                        ↔⟨ _ ⟩
```

But actually notice the prior mistake also:

(2) 
```haskell
			y₁ ≡ h (x₁ + 1)                      ↔⟨ ap-≡ᵣ (ap (λ a → h (a + 1)) E₁ᵣ) ⟩

				   1 - h²
			y₁ ≡ h –––––– + 1                  
				   1 + h²                        ↔⟨ _ ⟩
```

The parentheses are missing. This indicates that (1) would be correct if instead we had

```haskell
				    1 - h²   1 + h²
			y₁ ≡ h (–––––– + ––––––)
				    1 + h²   1 + h²               ↔⟨ _ ⟩
					
				     2
			y₁ ≡ h ––––––
				   1 + h²                        ↔⟨ _ ⟩
```

The corrected segment:

```haskell
module
	{- ... -}
	where
		E₂ : y₁ ≡ h (x₁ + 1) ↔ y₁ ≡ {! 2!}
		E₂ =
			y₁ ≡ h (x₁ + 1)                      ↔⟨ ap-≡ᵣ (ap (λ a → h (a + 1)) E₁ᵣ) ⟩

				    1 - h²
			y₁ ≡ h (–––––– + 1)                  
				    1 + h²                       ↔⟨ _ ⟩
					
				    1 - h²   1 + h²
			y₁ ≡ h (–––––– + ––––––)
				    1 + h²   1 + h²              ↔⟨ _ ⟩
					
				     2
			y₁ ≡ h ––––––
				   1 + h²                        ↔⟨ _ ⟩
					
				   2h
			y₁ ≡ ––––––             
			     1 + h²                          ↔∎
```
