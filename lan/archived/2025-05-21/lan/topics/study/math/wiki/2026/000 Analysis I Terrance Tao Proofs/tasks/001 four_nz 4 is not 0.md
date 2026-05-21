---
parent: "[[000 Analysis I Terrance Tao Proofs]]"
spawned_by: "[[000 Spawn Logs for Notes for Posts]]"
context_type: task
status: done
---

Parent: [[000 Analysis I Terrance Tao Proofs]]

Spawned by: [[000 Spawn Logs for Notes for Posts]]

Spawned in: [[000 Spawn Logs for Notes for Posts#^spawn-task-1f1d99|^spawn-task-1f1d99]]

# 1 Proof

```haskell
four-nz : 4 ≠ 0
four-nz = (ℕ-ax3 3)
```

# 2 Argument

```haskell
four-nz : 4 ≠ 0
four-nz (h₀ : 4 ≡ 0) = ?0
```

[[000 Defn N#^ax3]] Allows us to generate `⊥`: 

```haskell
ℕ-ax3 = ∀ (a : ℕ) → suc a ≠ 0
```

```haskell
_ = test-identical-type (ℕ-ax3 3) (4 ≠ 0)
```

```haskell
four-nz : 4 ≠ 0
four-nz (h₀ : 4 ≡ 0) = (ℕ-ax3 3) h₀
```

Or just

```haskell
four-nz : 4 ≠ 0
four-nz = (ℕ-ax3 3)
```

# 3 Related

- [[002 Chapter 1 Problems]]