<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

Write one entry per case, before you run anything. Name the rule behind each
predicted field. One to three sentences is enough. The grader checks the
structure of this file only. It does not judge how good the reasoning is, and
it sets no word minimum.

For every case you predicted wrongly, add a short correction after you run
it: what you predicted, what the language printed, and which rule you
misread. Corrections belong in the correction commits, not the prediction
commit.

Commit this file together with `predictions.tsv`, before any case runs:

```bash
git add predictions.tsv REASONING.md
```

The example below uses a query that is not one of the 16 cases, so it gives
no answer away. `parent(pat, X)` reads `parent(pat, jim)` because the `parent`
facts are tried top to bottom and only that one has `pat` as its first
argument:

```text
## parent(pat, X)

The goal scans the parent facts in source order. Only parent(pat, jim) has
pat first, so X binds to jim. No second fact matches, so the first ; finds
nothing and the search ends after one answer.
```

## P01

The `parent(tom, X)` goal scans the `parent/2` facts in source order. Its first answer binds `X=liz`, the next successful fact binds `X=bob`, and no third fact with `tom` as the parent remains.

## P02

parent(tom, bob) is a ground query with no variables to bind, so Prolog just checks whether the exact term is provable. The fact parent(tom, bob) appears in the knowledge base, so the search succeeds immediately and prints true. Only one fact matches and no alternative remains, so the first ; finds nothing and the count is 1.

## P03

The reverse-form query `parent(X, tom)` asks for any person whose child is `tom`. The knowledge base has no fact matching that pattern, so the search fails; `false` is reported, there is no second answer, and the count is zero.

## P04

= is unification, not arithmetic or assignment. The first goal unifies X with the compound term f(Y, a), leaving Y unbound inside it; the second goal then binds Y = b, which retroactively fills the hole in X, making X = f(b, a).
Correction: Correct format for the output is X=f(b,a),Y=b
## P05

The terms `f(X, b)` and `f(a, Y)` unify by matching the outer function and corresponding arguments. `X` becomes `a` and `Y` becomes `b`; the result is one successful, most-general unifier and no second answer.

## P06

a = b asks whether the two atoms can be made identical by unification. They are different atoms and no binding can change that, so unification fails at once and the answer is false. Nothing is left to backtrack into, so the count is 0.

## P07

The `grandparent(tom, X)` rule follows `parent(tom, P)` and then `parent(P, X)`. Tom's child `liz` has no child, while Tom's child `bob` yields `ann` and then `pat`, so the answers are `X=ann` followed by `X=pat`, with a count of two.

## P08

The base clause is listed first: ancestor(A,D) :- parent(A,D). So Prolog first answers with tom's direct children in fact order — parent(tom, liz) then parent(tom, bob), giving X=liz first and X=bob second. Only after the base clause is exhausted does the recursive clause descend, adding ann, pat, sue, and jim, for six descendants total.

## P09

The recursive-first `ancestor_r(tom, X)` rule repeatedly follows `parent/2` before trying the direct-parent base. This produces the descendants in depth-first order: `sue`, `jim`, `ann`, `pat`, `liz`, and `bob`, giving six answers.

## P10

The base clause is first: parent(X, jim) matches only parent(pat, jim), so X=pat is the first answer.
Correction: the recursive clause continues upward from jim through pat, bob, and tom, giving three answers.
## P11

`reach/2` is left recursive: it calls `reach(X, Z)` before inspecting an edge in the base clause. That recursive search continually explores the graph cycle instead of reaching a stable first answer, so I predict an unbounded or non-terminating result for the first answer, second answer, and count.

## P12

reach_r/2 lists the base clause first, so I checked edge(a, d) first. I saw that no edge(a, d) fact exists in the graph and concluded the query would simply return false. 
Correction: when edge(a,d) fails, the recursive clause fires, reads one edge at a time, and finds the path a→b→c→d, so the first answer is true; backtracking then loops the cycle and keeps finding true, making the count unbounded.
## P13

`len([a, b, c], N)` recurses through the list and counts three elements. The recursive calls return `M=2`, then `M=1`, then `M=0`, producing `N=3`; there are no later answers.

## P14

join/3 is written base-clause first, and with the result [1, 2] ground the query enumerates every way to split that list. The base clause gives the empty front, X=[],Y=[1,2]; the recursive clause peels 1 and recurses on [2], giving X=[1],Y=[2]; peeling 2 leaves [], whose only split is X=[1,2],Y=[]. A list of length 2 has three splits, so the count is 3.

## P15

The goal `X > 2` is written before `elem(X, [1, 2, 3])`, so SWI-Prolog encounters the comparison before `X` has been bound. Arithmetic comparison with an unbound value raises an instantiation error, rather than finding a list element first. I predict `error` for the first answer, second answer, and count.

## P16

Goals run left to right, so elem(X, [1,2,3]) binds X before the comparison X > 2 is checked. The search tries X=1 and X=2, both of which fail > 2, then X=3, which succeeds, giving X=3 as the first answer. No other element exceeds 2, so there is no second answer and the count is 1.

