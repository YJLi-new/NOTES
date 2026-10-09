# CSC1001 — Handwritten Answer Sheet

> Transcribed from the photographed answer sheet [Written-Homework.pdf](Written-Homework.pdf). `(?)` marks unclear handwriting.

## Multiple choice (Q1–25)

| Q | Ans | Q | Ans | Q | Ans | Q | Ans | Q | Ans |
|---|---|---|---|---|---|---|---|---|---|
| 1 | B | 2 | A | 3 | B | 4 | A | 5 | C |
| 6 | D | 7 | D | 8 | C | 9 | A | 10 | B |
| 11 | D | 12 | D | 13 | C | 14 | C | 15 | A |
| 16 | ACD | 17 | ABD | 18 | BC | 19 | BD | 20 | AC |
| 21 | ABD | 22 | CD | 23 | AC | 24 | BD | 25 | BCD |

## Question 3

**26.**
1. `[9, 8, 6, 5, 4, 3, 2, 1]`
2. The average time complexity is $O(\text{len(lst)}^{\text{len(lst)}})$ (as written).

**27.** (fill in the blanks — bubble sort)
1. `len(lst)`
2. `lst_len - 1`
3. `lst[i] > lst[i+1]`
4. `temp = lst[i]`
5. `lst[i] = lst[i+1]`
6. `lst[i+1] = temp`
7. `lst_len -= 1`

**28.**

(1) Recursive:

```python
def fib(n):
    if n == 0 or n == 1:
        return 1
    elif n >= 2:
        return fib(n-1) + fib(n-2)
```

(2) Iterative:

```python
def fib(n):
    num1 = 1
    num2 = 1
    num_n = 0
    if n == 0 or n == 1:
        return 1
    elif n >= 2:
        for i in range(n-1):
            num_n = num1 + num2
            num1 = num2
            num2 = num_n
        return num_n

fib(n)
```

**29.**
1. This code aims to create a binary tree and do a depth-first search on the binary tree.
2. Assume $N$ is the number of edges in the tree; then the average time complexity is $O(N)$.
3. The output is: `2 5 9 4 7 6 11 5 2` (one per line).
