# Lesses more
## JANUARY 2023: PUZZLE
![image info](./images/lesses-more.png)

Assign four nonnegative integers to the corners of a square, which we designate the active square. During a step, for each side of the active square, the absolute difference between the numbers on that side’s endpoints is assigned to its midpoint. Then these four new midpoints are connected into a new square (tilted 45 degrees from the previous). This new smaller square becomes the active square. Continue these steps until the active square has all zeroes on its corners.
<br><br>
Define f(a, b, c, d) to be the total number of squares drawn during this process when beginning with the numbers (a, b, c, d) written on the starting square in clockwise order. For example, given a starting arrangement of (10, 6, 3, 1), we would get the sequence of
<br><br>
(4, 3, 2, 9) <br>
(1, 1, 7, 5) <br>
(0, 6, 2, 4) <br>
(6, 4, 2, 4) <br>
(2, 2, 2, 2) <br>
(0, 0, 0, 0) <br>
<br>
where the game ends (pictured above). So f(10, 6, 3, 1) = 7. And trivially, f(0, 0, 0, 0) = 1.<br>
<br>
Consider the set S = {(a, b, c, d) | a, b, c, and d are all integers with 0 <= a, b, c, d <= 10,000,000}. Let M be the maximum value f obtains on S. Find (a, b, c, d) in S with minimum sum (a+b+c+d) where f(a, b, c, d) = M. Enter your answer as a semicolon-separated list, 10;6;3;1 for example.

# SOLUTION
Jane Street's official solution is elegant and concise and [can be found here](https://www.janestreet.com/puzzles/lesses-more-solution/). Here I'd like to offer a geometric interpretation.

## Preliminaries
### 1 - the constrains
The solution of the problem is a tuple of four elements $(A, B, C, D)$ where each element in the set of natural numbers {0, 1, .., 10<sup>7</sup>}. This means that the tuple solving the problem belongs to a set of size 10<sup>28</sup> and consequently a brute-force solution seems unlikely. <br>

The following code computes the number of steps to reduce a given tuple to the tuple $(0, 0, 0, 0)$ by applying the steps described in the puzzle.
```
def naive(t: List[int]) -> int:
    # t is a mathematical tuple (x0, x1, x2, x3)
    c = 1
    while sum(t)!=0:
        t = [
            abs(t[1] - t[0]), 
            abs(t[2] - t[1]), 
            abs(t[3] - t[2]), 
            abs(t[0] - t[3])
        ]
        c+=1
    return c
```
Trying a for loop iterating over each possible value for each element in the tuple quickly proves to be inconclusive.

### 2 - some easy cases
It can be proven that any tuple having some sort of symmetry quickly decays to the tuple $(0, 0, 0, 0)$. As an example consider the tuple $(A, A, A, A)$. <br>
**Case AAAA**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $A$ | $A$ | 
| $0$ | $0$ | $0$ | $0$ |

2 steps are needed to reach the base case. Let's make another example. <br>
**Case 000A**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $0$ | $0$ | $0$ | $A$ |
| $0$ | $0$ | $A$ | $A$ |
| $0$ | $0$ | $A$ | $A$ |
| $0$ | $A$ | $0$ | $A$ |
| $A$ | $A$ | $A$ | $A$ | 
| $0$ | $0$ | $0$ | $0$ |

Notice that the second last step is the same as **Case AAAA**.
A total of 5 steps is needed (to see more cases go to the Appendix at the end). Symmetry leads to a quick decay to $(0, 0, 0, 0)$ so the solution will not have two or more equal
elements even after many iterations. <br>

### 3 - dropping an element
Given a tuple $(A, B, C, D)$ with $A,B,C,D>0$ then the $(0, \vert A-B\vert, \vert B-C\vert, \vert D-A\vert)$ takes the same number of steps to reach $(0, 0, 0, 0)$. The state space can be restricted to the set of all tuples of the kind $(0, A, B, C)$.

### 4 - rotation invariance
Given a tuple $(A, B, C, D)$ then $(B, C, D, A)$, $(C, D, A, B)$ and $(D, A, B, C)$ have the same solution.

## The general **CASE 0ABC**
Consider the tuple $(A^1, B^1, C^1, D^1)$. Let $A^1>0$, $B^1>0$, $C^1>0$, $D^1>0$, $A^1\neq B^1 \neq C^1 \neq D^1$. Without loss of generality let $A^1$ be the minimum of the four elements. Then $(0, A, B, C)$ (with A=B^1-A^1, ecc) takes the same number of steps to reach $(0, 0, 0, 0)$. <br>

|   |   |   |   |
|---|---|---|---|
| $0$ | $A$ | $B$ | $C$ |
| $A$ | $\vert B-A \vert$ | $\vert B-C \vert$ | $C$ |

<b><i>(1)</b></i> $A>B$

|   |   |   |   |
|---|---|---|---|
| $A$ | $A-B$ | $\vert B-C \vert$ | $C$ |

<b><i>(1+)(2)</b></i> $A>B$ and $B>C$ => $A>B>C$

|   |   |   |   |
|---|---|---|---|
| $A$ | $A-B$ | $ B-C $ | $C$ |
| $B$ | $\vert A-2B+C \vert$ | $\vert B-2C \vert$ | $A-C$ |

If $\vert A-2B+C\vert = B$ and $A-2B+C   > 0$ => $A=3B+C$ => **CASE AABC**. <br>
If $\vert A-2B+C\vert = B$ and $A-2B+C &lt 0$ => $A= B-C$ => **CASE AABC**. <br>
If $\vert A-2B+C\vert = A-C$ and $A-2B+C   > 0$ => $C=B$ => impossible. <br>
If $\vert A-2B+C\vert = A-C$ and $A-2B+C &lt 0$ => $A=B$ => impossible. <br>
If $\vert A-2B+C\vert = B-2C$ and $A-2B+C   > 0$ => $A=3(B-C)$ => **CASE AABC**. <br>
If $\vert A-2B+C\vert = B-2C$ and $A-2B+C &lt 0$ => $A=  B-C$ => **CASE AABC**. <br>
If $\vert A-2B+C\vert = C-B$ and $A-2B+C   > 0$ => $A=  B+C$ => **CASE AABC**. <br>
If $\vert A-2B+C\vert = C-B$ and $A-2B+C &lt 0$ => $A=3(B-C)$ => **CASE AABC**. <br>

<b><i>(1+)(2+)(3)</b></i> $A>B>C$ and $A-2B+C>0$

<b><i>(1+)(2+)(3+)(4)</b></i> $A>B>C$ and $A-2B+C>0$ and $B-2C>0$

|   |   |   |   |
|---|---|---|---|
| $B$ | $ A-2B+C $ | $B-2C $ | $A-C$ |
| $\vert A-3B+C \vert$ | $\vert A-3B+3C \vert$ | $\vert B-A-C \vert$ | $\vert A-C-B \vert$ |

to be continued..

<b><i>(1+)(2)</b></i> $A>B$ and $B &lt C$

|   |   |   |   |
|---|---|---|---|
| $A$ | $A-B$ | $C-B$ | $C$ |
| $B$ | $\vert A-C \vert$ | $B$ | $A-C$ |

same as **CASE ABAB**.

<b><i>(1)</b></i> $A &lt B$

|   |   |   |   |
|---|---|---|---|
| $A$ | $B-A$ | $\vert B-C \vert$ | $C$ |

<b><i>(1-)(2)</b></i> $A &lt B$ and $B   > C$

|   |   |   |   |
|---|---|---|---|
| $A$ | $B-A$ | $B-C$ | $C$ |
| $\vert B-2A \vert $ | $\vert A-C \vert$ | $\vert B-2C\vert$ | $\vert A - C \vert$ |

same as **CASE ABAC**. <br>

<b><i>(1-)(2)</b></i> $A &lt B$ and $B &lt C$ => $A \lt B \lt C$
identical to case <b><i>(1+)(2)</b></i>, send $A &rarr; C$, $B &rarr; B$, $C &rarr; A$.

## Continued
Let's continue to develop point <b><i>(1+)(2+)(3+)(4+)</i></b> here. We found that all possible combinations of absolute values terminate except for <b><i>(1+)(2+)(3+)(4+)</b></i> and for <b><i>(1-)(2-)</b></i> which maps back to <b><i>(1+)(2+)</b></i>. These two cases have in common the fact that both are ordered, in the sense that either $A<B<C$ or
$A &lt B &lt C$.

# APPENDIX

---

**Case AAA0**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $A$ | $0$ |
| $0$ | $0$ | $A$ | $A$ |
| $0$ | $A$ | $0$ | $A$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

---

**Case AA00**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $0$ | $0$ |
| $0$ | $A$ | $0$ | $A$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

---

**Case AABB**, $A>0$, $B>0$, $A\neq B$<br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $B$ | $B$ |
| $0$ | $\vert A-B\vert$ | $0$ | $\vert A-B\vert$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ |
| $0$ | $0$ | $0$ | $0$ |

---

**Case A0A0**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $0$ | $A$ | $0$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

---

**Case ABAB**, $A>0$, $B>0$, $A\neq B$<br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $B$ | $A$ | $B$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ |
| $0$ | $0$ | $0$ | $0$ |

and notice that since $A\neq B => A-B\neq 0$. <br>

---

**Case AAAB**, $A>0$, $B>0$, $A\neq B$ <br>
If $A>B$ => **CASE AAAB** = **CASE AAA0**. <br>
If $A &lt B$ => **CASE AAAB** = **CASE 000A**. <br>

---

**Case ABA0**, $A>0$, $B>0$, $A\neq B$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $B$ | $A$ | $0$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $A$ | $A$ |
| $0$ | $\vert \vert A-B\vert -A \vert$ | $0$ | $\vert\vert A-B\vert-A\vert$|

If $\vert \vert A-B\vert -A \vert \neq 0$ => **CASE ABA0** = **CASE 0A0A**. <br>
If $\vert \vert A-B\vert -A \vert = 0$ => $(0, 0, 0, 0)$. <br>

---

**Case 0A0B**, $A>0$, $B>0$, $A\neq B$ <br>
|   |   |   |   |
|---|---|---|---|
| $0$ | $A$ | $0$ | $B$ |
| $A$ | $A$ | $B$ | $B$ |

same as **CASE AABB**. <br>

---

**Case AAB0**, $A>0$, $B>0$, $A\neq B$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $B$ | $0$ |
| $0$ | $\vert A-B\vert$ | $B$ | $A$ |

If $\vert A-B\vert = A$ and $A   > B$ => B=0 => impossible. <br>
If $\vert A-B\vert = A$ and $A &lt B$ => $B=2A$ => 4. <br>
If $\vert A-B\vert = B$ and $A   > B$ => $A=2B$ => 5. <br>
If $\vert A-B\vert = B$ and $A &lt B$ => $A=0$ => impossible. <br>
<br>
Assume $A>B$. <br>
|   |   |   |   |
|---|---|---|---|
| $0$ | $A-B$ | $B$ | $A$ |
| $A-B$ | $\vert A - 2B \vert$ | $A-B$  | $A$ |

If $\vert A-2B\vert =   A$ and $A   > 2B$ => $B=0$ => impossible. <br>
If $\vert A-2B\vert =   A$ and $A &lt 2B$ => $A=B$ => impossible. <br>
If $\vert A-2B\vert = A-B$ and $A   > 2B$ => $B=0$ => impossible. <br>
If $\vert A-2B\vert = A-B$ and $A &lt 2B$ => $A=3B/2$ => 7. <br>
Assume $A>B$, $A-2B>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A-B$ | $A-2B$ | $A-B$ | $A$ |
| $B$ | $B$ | $B$ | $B$ |

Assume $A>B$, $A-2B &lt 0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A-B$ | $-A+2B$ | $A-B$ | $A$ |
| $\vert 2A - 3B\vert$ | $\vert 2A - 3B\vert$ | $B$ | $B$ |

same as **CASE AABB** since $\vert 2A-3B \vert \neq B$ always. <br>

Assume $A &lt B$. <br>

|   |   |   |   |
|---|---|---|---|
| $0$ | $-A+B$ | $B$ | $A$ |
| $-A+B$  | $A$ | $-A+B$  | $A$ |

same as **CASE ABAB** (it is guaranteed that $A \neq B $) and, if
$\vert 2A -B\vert = 0$, it is the same as one of the cases discussed above. <br>

---

**Case 00AB**, $A>0$, $B>0$, $A\neq B$ <br>

|   |   |   |   |
|---|---|---|---|
| $0$ | $0$ | $A$ | $B$ |
| $0$  | $A$ | $\vert A-B \vert$  | $B$ |

If $\vert A-B\vert = B$ => same as **CASE 0AAB**<br>
If $\vert A-B\vert = A$ => same as **CASE 0AAB**<br>

Assume $A>B$. <br>

|   |   |   |   |
|---|---|---|---|
| $0$  | $A$ | $A-B$  | $B$ |
| $A$  | $B$ | $\vert A-2B \vert$  | $B$ |

If $\vert A-2B\vert = B$ and $A   > 2B$ => $A=3B$ => 7. <br>
If $\vert A-2B\vert = B$ and $A &lt 2B$ => $A=B$ => impossible. <br>

Assume $A>B$ and $A>2B$ <br>

|   |   |   |   |
|---|---|---|---|
| $0$  | $A$ | $A-B$  | $B$ |
| $A$  | $B$ | $A-2B$  | $B$ |
| $A-B$  | $\vert A-3B \vert$ | $\vert A-3B \vert$  | $A-B$ |

since $\vert A-3B \vert \neq 0$ always it is the same as **CASE AABB**. 
Assume $A>B$ and $A &lt 2B$ <br>

|   |   |   |   |
|---|---|---|---|
| $A$  | $B$ | $-A+2B$  | $B$ |
| $A-B$  | $A+B$ | $A+3B$  | $A-B$ |

same as **CASE AABB**. <br>

Assume $A &lt B$. <br>

|   |   |   |   |
|---|---|---|---|
| $0$  | $A$ | $B-A$  | $B$ |
| $A$  | $\vert B -2A\vert$ | $A$  | $B$ |

Assume $A &lt B$ and $B>2A$ <br>

|   |   |   |   |
|---|---|---|---|
| $A$  | $B-2A$ | $A$  | $B$ |
| $\vert B-3A \vert$  | $\vert B-3A \vert$  | $B-A$   | $B-A$ |

same as **CASE AABB**.

Assume $A &lt B$ and $B &lt 2A$ <br>

|   |   |   |   |
|---|---|---|---|
| $A$  | $2A-B$ | $A$  | $B$ |
| $B-A$  | $B-A$  | $B-A$   | $B-A$ |

same as **CASE AAAA**.

---

**Case AABC**, $A>0$, $B>0$, $C>0$, $A\neq B \neq C$ <br>

|   |   |   |   |
|---|---|---|---|
| $A$  | $A$ | $B$  | $C$ |
| $0$  | $\vert A - B \vert $  | $\vert B - C \vert$   | $\vert A - C \vert$ |

same as the general **CASE 0ABC**.