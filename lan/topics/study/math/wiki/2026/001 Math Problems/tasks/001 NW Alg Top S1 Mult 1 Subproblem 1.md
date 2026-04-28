---
parent: "[[001 Math Problems]]"
spawned_by: "[[000 Spawn Logs for Math Problems]]"
context_type: task
status: todo
---

Parent: [[001 Math Problems]]

Spawned by: [[000 Spawn Logs for Math Problems]]

Spawned in: [[000 Spawn Logs for Math Problems#^spawn-task-25dab4|^spawn-task-25dab4]]

Parent problem : [[002 NW Alg Top S1 Mult 1 Failed Attempt 1]]

Used in : [[002 NW Alg Top S1 Mult 1 Failed Attempt 1#^5cc9b7]]
# 1 Problem

Simplify the following expression and obtain $?_0$:

$$P_0 : \forall (a\ b\ c : \mathbb{Q}) \to (1 - a - b + a \cdot b) \cdot (1 + c^2) - (1 + a + b + a \cdot b) \cdot (1 - c^2) \equiv\ ?_0$$

# 2 Proof

$$P_0 : \forall (a\ b\ c : \mathbb{Q}) \to (1 - a - b - a \cdot b) \cdot (1 + c^2) - (1 + a + b + a \cdot b) \cdot (1 - c^2) \equiv\ ?_0$$
$P_0\ a\ b\ c =$
$$
(1 - a - b + a \cdot b) \cdot (1 + c^2) - (1 + a + b + a \cdot b) \cdot (1 - c^2)
$$

$\equiv \langle$ `_` $\rangle$
$$
((1 - a - b + ab) + c^2 - ac^2 - bc^2 + abc^2 ) - 
((1 + a + b + ab) - c^2 - ac^2 - bc^2 - abc^2  )
$$

$\equiv \langle$ `_` $\rangle$
$$
( 1 - a - b + ab + c^2 - ac^2 - bc^2 + abc^2 ) +
(-1 - a - b - ab + c^2 + ac^2 + bc^2 + abc^2  )
$$

$\equiv \langle$ `_` $\rangle$
$$
- 2a - 2b + 2c^2 + 2abc^2
$$

$\equiv \langle$ `_` $\rangle$
$$
2 \cdot (abc^2 - a - b + c^2)
$$

Thus we set $?_0$ to $2 \cdot (abc^2 - a - b + c^2)$:

$P_0 : \forall (a\ b\ c : \mathbb{Q}) \to (1 - a - b + a \cdot b) \cdot (1 + c^2) - (1 + a + b + a \cdot b) \cdot (1 - c^2) \equiv$
$2 \cdot (abc^2 - a - b + c^2)$


# 3 Template

$\equiv \langle$ ... $\rangle$
$\blacksquare$