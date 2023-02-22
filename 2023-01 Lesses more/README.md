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

## SOLUTION
Jane Street's official solution is elegant and concise and [can be found here](https://www.janestreet.com/puzzles/lesses-more-solution/). Here I'd like to offer a geometric interpretation.

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
Give a tuple $(A, B, C, D)$ with $A,B,C,D>0$ then the $(0, \vert A-B\vert, \vert B-C\vert, \vert D-A\vert)$ takes the same number of steps to reach $(0, 0, 0, 0)$. The state space can be restricted to the set of all tuples of the kind $(0, A, B, C)$.

### 4 - preserving order




# APPENDIX
**Case AAA0**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $A$ | $0$ |
| $0$ | $0$ | $A$ | $A$ |
| $0$ | $A$ | $0$ | $A$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

**Case AA00**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $0$ | $0$ |
| $0$ | $A$ | $0$ | $A$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

**Case AABB**, $A>0$, $B>0$, $A\neq B$<br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $A$ | $B$ | $B$ |
| $0$ | $\vert A-B\vert$ | $0$ | $\vert A-B\vert$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ |
| $0$ | $0$ | $0$ | $0$ |

**Case A0A0**, $A>0$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $0$ | $A$ | $0$ |
| $A$ | $A$ | $A$ | $A$ |
| $0$ | $0$ | $0$ | $0$ |

**Case ABAB**, $A>0$, $B>0$, $A\neq B$<br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $B$ | $A$ | $B$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ | $\vert A-B\vert$ |
| $0$ | $0$ | $0$ | $0$ |

and notice that since $A\neq B => A-B\neq 0$. <br>

**Case AAAB**, $A>0$, $B>0$, $A\neq B$ <br>
If $A>B$ => **CASE AAAB** == **CASE AAA0** <br>
If $A<B$ => **CASE AAAB** == **CASE 000A** <br>

**Case ABA0**, $A>0$, $B>0$, $A\neq B$ <br>
|   |   |   |   |
|---|---|---|---|
| $A$ | $B$ | $A$ | $0$ |
| $\vert A-B\vert$ | $\vert A-B\vert$ | $A$ | $A$ |
| $\0$ | $\vert \vert A-B\vert -A \vert$ | $0$ | $\vert\vert A-B\vert-A\vert$| <br>
if $\vert \vert A-B\vert -A \vert \neq 0$ => **CASE ABA0** == **CASE 0A0A** <br>
if $\vert \vert A-B\vert -A \vert = 0$ => $(0, 0, 0, 0)$ <br>
**Case AAB0** <br>
**Case 0A0B** <br>
**Case 00AB** <br>
**Case AABC** <br>
