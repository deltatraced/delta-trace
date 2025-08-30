---
similar_to: "[[Wk 27 000 Expedition 33 Lune Fire Rage Scaling]]"
status: pend
resolved: "false"
---
2025-08-28 Wk 35 Thu - 16:38
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

## 4.2 Strings

We play without sound, and only by carrying a string on a cue (feet landing). We read `.` as pause and `-` as blank. `.` is a key that doesn't do anything, but exists so that we can have accurate tempo.

So far the successful strings have been 

| Fast       | Swift         | Impressive      |
| ---------- | ------------- | --------------- |
| ..-..-...- | .....-...-..- | ...-..-..-....- |

Strings tried:

|      | Fast        | Swift        | Impressive      |
| ---- | ----------- | ------------ | --------------- |
|      | ..-.-...-   | ....-...-.-  | ..-.-.-.-...-   |
| win  | 1           | 1            |                 |
| lose | 0           | 3            | 1               |
|      | ..-.-..-    | ...-...-.-   | ..-.-.-.-....-  |
| win  | 2           |              |                 |
| lose | 0           |              |                 |
|      | ..-..-..-   | ....-...-..- | ...-.-.-....-   |
| win  | 2           |              |                 |
| lose | 2           |              |                 |
|      | ..-..-...-  |              | ...-..-..-....- |
| win  | 0           |              |                 |
| lose | 2           |              |                 |
|      | ..-..-....- |              |                 |
| win  | 0           |              |                 |
| lose | 0           |              |                 |

## 4.3 Golgra Damage

Times means times encountered (or at least recorded).

| Times | Color | Damage | Which | Attack     |
| ----- | ----- | ------ | ----- | ---------- |
| 2     | white | 8785   | third | fast       |
| 4     | white | 8785   | first | impressive |
| 5     | white | 3514   | first | swift      |

| Times | Color  | Damage | Which  | Attack     |
| ----- | ------ | ------ | ------ | ---------- |
| 1     | yellow | 19767  | first  | fast       |
| 1     | yellow | 13178  | second | fast       |
| 2     | yellow | 13178  | third  | fast       |
| 2     | yellow | 13178  | first  | impressive |
| 2     | yellow | 5271   | first  | swift      |

## 4.4 Maelle Damage

| Stat  | Meaning                                               |
| ----- | ----------------------------------------------------- |
| `Md`  | Marked                                                |
| `So`  | Offensive Stance                                      |
| `Sd`  | Defensive Stance                                      |
| `Sl`  | Stanceless                                            |
| `QNN` | Quick Action. 0 = Perfect, 1 = Hit, 2 = Miss, - = NA. |
| `BmN` | We see "Burn" N times                                 |
| `R--` | Roulette 50%                                          |
| `R++` | Roulette 200%                                         |
| `R??` | Roulette unknown                                      |
| `R-?` | Roulette likely 50%                                   |
| `R+?` | Roulette likely 200%                                  |
| `Fo`  | First offensive                                       |
| `Br`  | Brulerum                                              |
| `Bk`  | Breaker                                               |
| `Cb`  | Critical Burn                                         |
| `Aa`  | Augmented aim                                         |
| `Vr`  | Versatile                                             |
| `Ms`  | Marking shot                                          |
| `Bs`  | Burning shot                                          |
| `BNN` | NN = Number of burns read before "Golgra burns."      |
| `bNN` | NN = Number of burns read after "Golgra burns."       |
| `--`  | Format Placeholder                                    |
| `---` | Format Placeholder                                    |

In general,

$$
\text{white} = \frac{2}{3}\text{yellow}
$$


We increment variant for same attack but different damage. We increment subvariant for same attack same damage different stats.


## 4.5 Procedure

2025-08-24 Wk 34 Sun - 09:22

### 4.5.1 Sampling second parry

| Times | Color  | Stats                 | Damage | Attack  | Variant |
| ----- | ------ | --------------------- | ------ | ------- | ------- |
| 1     | white  | `Sl --- Bk -- R--`    | 1361   | counter | 1       |
| 1     | white  | `Sl --- Bk -- R++`    | 5445   | counter | 2       |
| 1     | yellow | `Sl Bm1 Bk Br R--`    | 2042   | counter | 3.1     |
| 4     | yellow | `Sl Bm1 Bk Br R-- Cb` | 2042   | counter | 3.2     |
| 1     | yellow | `Sl Bm1 Bk Br R++`    | 8168   | counter | 4.1     |
| 3<br> | yellow | `Sl Bm1 Bk Br R++ Cb` | 8168   | counter | 4.2     |

2025-08-24 Wk 34 Sun - 09:47

It doesn't seem to make a difference what attack we parry, maelle will still deal the same damage under the same characteristics, so we'll just call it the counter attack.

Also Critical burn or no critical burn seems to make no damage difference given all else.

Burn mark `Bm` may be identical to `Brulerum` `Br`.

We sample second parry because the first one has the noise of first offensive which never occurs again.

We seem to always see a burn icon but not always a burn mark ("burn" text) for parry

2025-08-24 Wk 34 Sun - 10:08

For roulette, we may either get 50% damage or 200% damage. 

(math)

(1)

Let `Atk` be normal damage

(2)

$R^-$ represents the 50% damage

$$
R^- = \frac{1}{2} Atk
$$

$$
\text{Atk} = 2R^-
$$
(3)

$R+$ represents 200% damage

$$
R^+ = 2\text{Atk}
$$


$$
\begin{aligned}
& R^+ \\
& = 2\text{Atk} \\
& = 2(2R^-) \\
& = 4R^- \\
\end{aligned}
$$

(/math)

So if you divide two damages and see a factor of 4, know you went $R^-$ -> $R^+$.

So correcting

| Times | Color | Stats              | Damage | Attack  | Variant |
| ----- | ----- | ------------------ | ------ | ------- | ------- |
| 1     | white | `Sl --- Bk -- R-?` | 1361   | counter | 1       |
| 1     | white | `Sl --- Bk -- R+?` | 5445   | counter | 2       |

to

| Times | Color | Stats              | Damage | Attack  | Variant |
| ----- | ----- | ------------------ | ------ | ------- | ------- |
| 1     | white | `Sl --- Bk -- R--` | 1361   | counter | 1       |
| 1     | white | `Sl --- Bk -- R++` | 5445   | counter | 2       |

Also white -> yellow is a factor of 1.5, so we now know that those are related

| Times | Color  | Stats                 | Damage | Attack  | Variant |
| ----- | ------ | --------------------- | ------ | ------- | ------- |
| 1     | white  | `Sl --- Bk -- R++`    | 5445   | counter | 2       |
| 1     | yellow | `Sl Bm1 Bk Br R+?`    | 8168   | counter | 4.1     |
| 3<br> | yellow | `Sl Bm1 Bk Br R+? Cb` | 8168   | counter | 4.2     |

 Correct them to also be $R^+$.

2025-08-24 Wk 34 Sun - 10:24

Those are related with a 1.5x white -> yellow.

| Times | Color  | Stats                 | Damage | Attack  | Variant |
| ----- | ------ | --------------------- | ------ | ------- | ------- |
| 1     | white  | `Sl --- Bk -- R--`    | 1361   | counter | 1       |
| 1     | yellow | `Sl Bm1 Bk Br R-?`    | 2042   | counter | 3.1     |
| 3     | yellow | `Sl Bm1 Bk Br R-? Cb` | 2042   | counter | 3.2     |

Correct them to $R^-$.

2025-08-24 Wk 34 Sun - 10:25

So far the only relevant stats for parry damage are $R^-$/$R^+$ and white/yellow. Others like critical burn etc might be more relevant for chip damage via burns later or for increasing the likelihood that we get yellow rather than white (critical hit).

### 4.5.2 Sampling Second Turn Shots

2025-08-24 Wk 34 Sun - 10:33

| Times | Color  | Stats                       | Damage | Attack | Variant |
| ----- | ------ | --------------------------- | ------ | ------ | ------- |
| 1     | white  | `Sl --- Bk -- R-- Cb Aa Ms` | 82     | shot   | 1       |
| 1     | white  | `Sl Bm1 Bk -- R-- Cb Aa Bs` | 82     | shot   | 1       |
| 6     | yellow | `Sl Bm1 Bk Br R++ Cb Aa`    | 490    | shot   | 2.1     |
| 1     | yellow | `Sl Bm2 Bk Br R++ Cb Aa`    | 490    | shot   | 2.2     |
| 1     | yellow | `Sl Bm1 Bk Br R++ Cb Aa Vr` | 490    | shot   | 2.3     |
| 4     | yellow | `Sl Bm1 Bk Br R-- Cb Aa `   | 123    | shot   | 3.1     |
| 1     | yellow | `Sl Bm1 Bk Br R-- Cb Aa Vr` | 123    | shot   | 3.2     |
| 1     | yellow | `Sl Bm1 Bk Br R++ Cb Aa Md` | 735    | shot   | 4       |

2025-08-24 Wk 34 Sun - 10:45

123 -> 490 is a 4x factor. This is roulette effect.

123 -> 82 is a 1.5x factor. This is critical hit effect.

$\frac{735}{490} = 1.5$. This is a marked effect!

2025-08-27 Wk 35 Wed - 11:00

Ontology update, `Bm` for burn mark is not enough. Sometimes we inflict multiple burns, and we see the text "Burn" multiple times, so now we have `BmN` where N is some number. If you see it once, it's `Bm1`, if you see it twice it's `Bm2`, etc.

### 4.5.3 Sampling Offensive Switch on Start of Second Turn

2025-08-24 Wk 34 Sun - 10:58

| Times | Color  | Stats                    | Damage | Attack           | Variant |
| ----- | ------ | ------------------------ | ------ | ---------------- | ------- |
| 1     | yellow | `Sl Bm1 Bk Br R-? Cb`    | 599    | Offensive Switch | 1       |
| 1     | yellow | `Sl Bm2 Bk Br R-? Cb Md` | 980    | Offensive Switch | 2       |

### 4.5.4 Sampling Burn Chip Damage

2025-08-24 Wk 34 Sun - 11:04

| Times | Color | Stats                          | Damage | Attack | Variant |
| ----- | ----- | ------------------------------ | ------ | ------ | ------- |
| 1     | white | `Sl Bm1 Bk -- R-? B01 b01 `    | 726    | Burn   | 1       |
| 1     | white | `Sl Bm1 Bk -- R-? B02 b02 `    | 1425   | Burn   | 2       |
| 1     | white | `Sl Bm1 Bk -- R-? B02 b02 Cb ` | 1425   | Burn   | 2.2     |
| 1     | white | `So Bm1 Bk -- R-? B16 b16 Cb ` | 3312   | Burn   | 3       |
| 1     | white | `So Bm1 Bk -- R+? B17 b16 Cb ` | 9999   | Burn   | 4       |
| 1     | white | `So Bm1 Bk -- R+? B16 b15 Cb ` | 9999   | Burn   | 4.1     |
| 1     | white | `So Bm1 Bk -- R+? B07 b07 Cb ` | 5082   | Burn   | 5       |

2025-08-24 Wk 34 Sun - 11:08

![[Pasted image 20250824110819.png]]

This is on a burn. I'm assuming the `x2` is for 

2025-08-27 Wk 35 Wed - 12:05

On end turn, golgra loses burn mark but no burn damage. Also sometimes there's a steep drop like 16->2, suggesting that the burns expire or something.

### 4.5.5 Sampling Offensive Stance Parries

| Times | Color  | Stats              | Damage | Attack  | Variant |
| ----- | ------ | ------------------ | ------ | ------- | ------- |
| 0     | yellow | `Sl Bm1 Bk Br R--` | 2042   | counter | 3.1     |




### 4.5.6 Sampling Sword Ballet on Start of Third Turn

| Times | Color  | Stats                      | Damage | Attack      | Variant |
| ----- | ------ | -------------------------- | ------ | ----------- | ------- |
| 4     | yellow | `So Bm1 Bk Br R-? Cb Q12 ` | 1348   | SwordBallet | 1       |
| 1     | yellow | `So Bm1 Bk Br R+? Cb Q12 ` | 5391   | SwordBallet | 2       |
| 3     | yellow | `So Bm1 Bk Br R-? Cb Q00`  | 1715   | SwordBallet | 3       |
| 2     | yellow | `So Bm1 Bk Br R+? Cb Q00`  | 6861   | SwordBallet | 4       |

### 4.5.7 Sampling Sword Ballet on Start of Second Turn

| Times | Color  | Stats                      | Damage | Attack      | Variant |
| ----- | ------ | -------------------------- | ------ | ----------- | ------- |
| 3     | yellow | `Sl Bm1 Bk Br R-? Cb Q00 ` | 1143   | SwordBallet | 3       |
| 2     | yellow | `Sl Bm1 Bk Br R-? Cb Q00 ` | 4573   | SwordBallet | 4       |

### 4.5.8 Numerical Estimation of Golgra HP

2025-08-27 Wk 35 Wed - 12:23

![[Pasted image 20250827122349.png]]

This is 1:37 minutes in, died in the third turn first attack.

![[Pasted image 20250827122748.png]]

This box measures 16x7 pixels. 

2025-08-27 Wk 35 Wed - 12:37

![[Pasted image 20250827123703.png]]

Approximately 41 of those would fill the bar. 

Another quick way to measure is to approximately box the HP bar

![[Pasted image 20250827123754.png]]

This boundary is 659x7 pixels, and $\frac{659}{16} \approx 41.2$.

2025-08-27 Wk 35 Wed - 12:39

Now let's count the total damage for one box.

First Turn

9999, 726, 2042, 1452, 2042, 

490, 123, 123, 123, 490, 184, 490, 184,

980,

Second Turn

3312, 9999, 9999, 2552, 9999, 3829, 1452, 3063,

1715, 6861, 1715, 1715, 6861,

Third Turn

5082,

So in total, 

```sh
python3 -c "l=[9999, 726, 2042, 1452, 2042, 490, 123, 123, 123, 490, 184, 490, 184, 980, 3312, 9999, 9999, 2552, 9999, 3829, 1452, 3063, 1715, 6861, 1715, 1715, 6861, 5082,]; print(sum(l))"

# out
87602
```

So we estimate that golgra has $87,602 \times \frac{659}{16} \approx 3,608,107$ HP 

2025-08-27 Wk 35 Wed - 13:28

If we keep dealing 43,801 $\frac{\text{dmg}}{\text{turn}}$ we could win within 83 turns.

2025-08-27 Wk 35 Wed - 13:58

Every turn seems to take around 50 seconds (video of 2 turns is around 1:41 minutes), so 83 turns can expect to take $50\ \frac{\text{s}}{\text{turn}} \times 83\ \text{turn} = 4150\ \text{s} \approx 69\  \text{min}$.

## 4.6 Investigate parry error range

2025-08-27 Wk 35 Wed - 21:33

Created a script to sample parrying error ranges:

[3.1 Create Script to press keys at exact intervals](https://github.com/LanHikari22/lan-setup-notes/blob/webview/lan/topics/gaming/tasks/2025/000%20Create%20script%20to%20analyze%20expedition%2033%20golgra%20fight%20and%20count%20turns.md#31-create-script-to-press-keys-at-exact-intervals)

2025-08-27 Wk 35 Wed - 21:47

Testing on fast...

```python
INPUT_DEV='/dev/input/event{N}' ON_KEYS="KEY_KP6,KEY_2" KEY='18' INTERVALS_MS="380,1000,1000" python3 run_sequence.py
```

This is a pass for the first touch once golgra's feet lands on ground.

## 4.7 Sampling passing touches via run_sequence

We will tweak this script and see what ranges give us a passing parry:

```python
INPUT_DEV='/dev/input/event{N}' ON_KEYS="KEY_KP6,KEY_2" KEY='18' INTERVALS_MS="380,1000,1000" python3 run_sequence.py
```

The source of noise here is the time it takes for us to trigger the parry sequence. We try to do this right on golgra's feet landing, which is an error source.

There is code overhead from callback detection to pressing the first key of about 26.96 ms - 28.72 ms. This is also the overhead between each successful ydotool presses.

| Column         | Meaning                                                                                |
| -------------- | -------------------------------------------------------------------------------------- |
| Attack         | The name of the attack sampled                                                         |
| TouchN (ms)    | The interval before the Nth touch in a parry                                           |
| Passed (#/all) | Num of touches passed, or all                                                          |
| Other          | When re-running the test, we get N (M) where N is the number of times we get M passed. |
| Valid (#)      | Num of times this test was reproduced to reduce noise                                  |
| Invalid (#)    | Num of times this test failed to be reproduced                                         |

| Attack | Touch1 (ms) | Touch2 (ms) | Touch3 (ms) | Touch4 (ms) | Passed (#/all) | Other               | Valid (#) | Invalid (#) |
| ------ | ----------- | ----------- | ----------- | ----------- | -------------- | ------------------- | --------- | ----------- |
| fast   | 300         | 1000        | 1000        |             | 0              |                     | 1         | 0           |
| fast   | 350         | 1000        | 1000        |             | 2              |                     | 1         | 2           |
| fast   | 380         | 1000        | 1000        |             | 1              |                     | 1         | 0           |
| fast   | 400         | 1000        | 1000        |             | 1              | 2 (2) 1 (0) 1 (all) | 5         | 4           |
| fast   | 410         | 1000        | 1000        |             | 1              | 3 (2) 3 (0) 1 (all) | 4         | 8           |
| fast   | 420         | 1000        | 1000        |             | 0              |                     | 5         | 4           |
| fast   | 430         | 1000        | 1000        |             | 0              |                     | 8         | 6           |
| fast   | 450         | 1000        | 1000        |             | 0              |                     | 2         | 0           |


## 4.8 Sampling string performance as I play

2025-08-28 Wk 35 Thu - 00:54

I wrote a monitoring script that would cluster the events by time, each cluster having its events close to one another within 2s. Then, I'm able to validate the clusters for ones matching an expected string, finally... I reference all pN and bN (pause, blank) times from the first string key.

# 5 Ideas
## 5.1 See if analysis here can be added to a wiki somewhere

### 5.1.1 Pend

# 6 References
