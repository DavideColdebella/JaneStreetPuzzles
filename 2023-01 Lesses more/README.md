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

### First step: the constrains
A tuple of four elements (x0, x1, x2, x3), each element in the set of natural numbers {0, 1, .., 10<sup>7</sup>}, is the solution of the problem. This means that the solution belongs to a set of size 10<sup>28</sup>, so a brute-force solution seems unlikely. <br>

The following code computes the number of steps to reduce a given tuple to the tuple (0, 0, 0, 0) by applying the steps described in the puzzle.
```
def naive(t):
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

### Second step: some easy cases
It can be proven that any tuple having some sort of symmetry quickly decays to the tuple (0, 0, 0, 0). As an example consider the tuple (A, A, A, A)
|Case AAAA      |
|---|---|---|---|
| A | A | A | A |
| 0 | 0 | 0 | 0 |

# APPENDIX
