---
similar_to: "[[Wk 27 000 Expedition 33 Lune Fire Rage Scaling]]"
status: pend
resolved: "false"
---

# 1 Objective

To map out the damage Maelle does against Golgra and understand it.

# 2 Context

![[Pasted image 20250726232540.png]]


![[Pasted image 20250727001245.png]]

![[Pasted image 20250726232831.png]]

# 3 Journal

## 3.1 Strategy

2025-07-26 Wk 30 Sat - 23:35

Since we're using free aim shots now for the 1 AP skill, we're adding luminas to boost those.

The current strategy is that we alternate defensive and offensive stances. When in offense stance, we use the Sword Ballet skill which has the most amount of hits and can do upwards so far of 29k damage. This switches us defensive so we use Offensive Switch the next turn to switch back offensive. Since Offensive Switch costs only 1 AP, and we have 9 AP, we spend 8 AP on headshot free aim shots which can apply burn and marked for increased damage.

![[Pasted image 20250726233714.png]]

![[Pasted image 20250726235628.png]]

![[Pasted image 20250726235642.png]]

Still need practice on always getting Sword Ballet Perfects though...



# 4 Investigations

## 4.1 On use of Roulette 

2025-07-26 Wk 30 Sat - 23:30

We currently use the `Roulette` lumina, which is as described:

> Every hit has a 50% chance to deal either 50% or 200% of its damage


> [!NOTE] Problem
Assuming every hit deals 100 damage, what's the expected amount of damage done after 50 attacks (a) with Roulette  and (b) without Roulette?

(a)

We have 50% chance to deal 50 damage and 50% chance to deal 200 damage. On average, we deal $\frac{50 + 200}{2}=125$ damage.

So we expect to deal more damage over time because the reward is worth our entire damage, while the punishment is only worth half our damage, and they are both equally as likely.

So on average we expect,

$$
\begin{aligned}
& \text{Expected} \\
& = 125 \ \frac{\text{dmg}}{\text{atk}} \times 50 \ \text{atk} \\
& = 6250 \ \text{dmg}
\end{aligned}
$$


(b) 

$$
\begin{aligned}
& \text{Expected} \\
& = 100 \ \frac{\text{dmg}}{\text{atk}} \times 50 \ \text{atk} \\
& = 5000 \ \text{dmg}
\end{aligned}
$$


So we should use this. Normally this adds risk because the enemies can deal 200% damage on us. But with golgra, we cannot afford to be hit even once. So we have all the reward and none of the risk.

# 5 Ideas
## 5.1 See if analysis here can be added to a wiki somewhere

### 5.1.1 Pend

# 6 References
