---
title: "Computing The Catalan Numbers In Racket"
date: 2022-10-22T11:07:37+03:00
tags:
  - catalan-numbers
  - racket
---

The Catalan numbers is a sequence (named after a Belgian mathematician and not the Romance language) which satisfies the recurrence relation:
$$C_{0} = 1,\hspace{6mu}C_{n+1} = \sum_{i=0}^{n} C_{i}C_{n - i}\qquad(n\geq 0)$$

Among other interesting applications, Cn counts the number of expressions containing n correctly matched pairs of parentheses.

This post will show the transformation of the above summation into a dynamic programming algorithm using Racket.

First of all we notice that if we translated directly the referenced definition into a recursive program, a call for the nth Catalan number would include $2*(n-1)$ recursive calls (minus one because we don't count the base case), which leads to a prohibitive exponential-time algorithm. So we will have to use some sort of memoization to reduce the time-complexity.

Let's visualise the summation process for the 8th Catalan number:
```
  0  1  2  3   4   5   6    7
 [1, 1, 2, 5, 14, 42, 132,  X]  C(7):

  a        *           A    +
     b     *       B        +
        c  *   C            +
          d*D               +
        E  *   e            +
     F     *       f        +
  G        *           g

```
Knowing the first 7 catalan numbers we see that in order to find the 8th one we have to:
- Multiply the 6th catalan with the 0th catalan,
- then multiply the 5th with the 1st,
- then the 4th with the 2nd,
- then the 3rd by itself,
- then the 2nd with the 4th,
- then the 1st with the 5th,
- then the 0th with the 6th
- and then sum up all these products to get the 8th catalan.

The process described above implies that as a first step we need to create a new list with the products. Since we already have a list with the first 7 catalan numbers, this could be achieved by duplicating this list in reversed order and then walking down both lists simultaneously multiplying the two elements.

This step can be easily handled by racket's map function which does exactly that for as many lists as we provide it with.
`(map (lambda (x y) (* x y)) ls (reverse ls))`

Now that we have a new list with the products we just need to sum them up, a classical use case for the fold function.
`(foldl + 0 the-products)`

Since addition is associative and commutative, the foldr function would return the same result. The difference between foldl and foldr is subtle and worth experimenting with. Possibly a subject for another post.

Notice that there is a repetition which has not been taken into account in the proposed algorithm. However, even if we took advantage of it, we would just reduce the time-complexity by a constant amount which is asymptotically unimportant.

Let's see a complete program.

```racket
(define (catalan n)

  (define (augment ls)                      ;1+3*len(ls)
    (cons                                   ;1
     (foldl + 0                             ;len(ls)
            (map (lambda (x y) (* x y))     ;len(ls)
                 ls
                 (reverse ls)))             ;len(ls)
     ls))

  (define (loop n ls)                       ;(3n^2 + n)/2
    (if (<= n 0)
        (reverse ls)                        ;n
        (loop (- n 1) (augment ls))))       ;(1+3*1) + (1+3*2) + ... + (1+3*n) =
	                                        ;(3n^2 - n)/2

  (loop n '(1)))

(catalan 25)                                ;n^2

'(1
  1
  2
  5
  14
  42
  132
  429
  1430
  4862
  16796
  58786
  208012
  742900
  2674440
  9694845
  35357670
  129644790
  477638700
  1767263190
  6564120420
  24466267020
  91482563640
  343059613650
  1289904147324
  4861946401452)
```

The final algorithm is one of $\mathcal{O}(n^2)$ time-complexity which, with the help of Racket and memoization, was achieved it in a functional and side-effect free way.
