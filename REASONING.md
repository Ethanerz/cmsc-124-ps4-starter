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

Write one to three sentences here. Name the rule that fixes each field.

## P03

The reverse-form query `parent(X, tom)` asks for any person whose child is `tom`. The knowledge base has no fact matching that pattern, so the search fails; `false` is reported, there is no second answer, and the count is zero.

## P04

Write one to three sentences here. Name the rule that fixes each field.

## P05

The terms `f(X, b)` and `f(a, Y)` unify by matching the outer function and corresponding arguments. `X` becomes `a` and `Y` becomes `b`; the result is one successful, most-general unifier and no second answer.

## P06

Write one to three sentences here. Name the rule that fixes each field.

## P07

The `grandparent(tom, X)` rule follows `parent(tom, P)` and then `parent(P, X)`. Tom's child `liz` has no child, while Tom's child `bob` yields `ann` and then `pat`, so the answers are `X=ann` followed by `X=pat`, with a count of two.

## P08

Write one to three sentences here. Name the rule that fixes each field.

## P09

The recursive-first `ancestor_r(tom, X)` rule repeatedly follows `parent/2` before trying the direct-parent base. This produces the descendants in depth-first order: `sue`, `jim`, `ann`, `pat`, `liz`, and `bob`, giving six answers.

## P10

Write one to three sentences here. Name the rule that fixes each field.

## P11

`reach/2` is left recursive: it calls `reach(X, Z)` before inspecting an edge in the base clause. That recursive search continually explores the graph cycle instead of reaching a stable first answer, so I predict an unbounded or non-terminating result for the first answer, second answer, and count.

## P12

Write one to three sentences here. Name the rule that fixes each field.

## P13

`len([a, b, c], N)` recurses through the list and counts three elements. The recursive calls return `M=2`, then `M=1`, then `M=0`, producing `N=3`; there are no later answers.

## P14

Write one to three sentences here. Name the rule that fixes each field.

## P15

The goal `X > 2` is written before `elem(X, [1, 2, 3])`, so SWI-Prolog encounters the comparison before `X` has been bound. Arithmetic comparison with an unbound value raises an instantiation error, rather than finding a list element first. I predict `error` for the first answer, second answer, and count.

## P16

Write one to three sentences here. Name the rule that fixes each field.

