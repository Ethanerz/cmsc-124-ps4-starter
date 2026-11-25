# The Search and the Stack

## P07 trace

```text
== trace P07 ==
Call: grandparent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: parent(liz,X)
  Fail: parent(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: parent(bob,X)
  Exit: parent(bob,ann)
Exit: grandparent(tom,ann)
answer 1: X=ann
Redo: grandparent(tom,ann)
  Redo: parent(bob,ann)
  Exit: parent(bob,pat)
Exit: grandparent(tom,pat)
answer 2: X=pat
Redo: grandparent(tom,pat)
  Redo: parent(bob,pat)
  Fail: parent(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: grandparent(tom,X)
== 2 answers ==
```

`Call` enters a goal and pushes its frame onto the runtime stack. `Exit` and
`Fail` finish a goal and pop its frame. For example, the failed
`parent(liz,X)` frame is popped before Prolog redoes `parent(tom,liz)` to try
Tom's next child.

`Redo` resumes the search at a saved choice point to try another clause or
fact, restoring the state needed to continue backtracking. It is not a
returned frame: that frame has already been popped at `Exit`, so its local
execution has ended. The choice point preserves the alternatives and
continuation needed to resume the search.

The difference comes from goal order inside each recursive clause: `reach/2` calls itself before reading a fact, whereas `ancestor/2` reads a fact before calling itself.

In `cases/kb.pl`, `reach/2` is left-recursive: its recursive clause is `reach(X, Y) :- reach(X, Z), edge(Z, Y).` Because Prolog tries clauses top to bottom and goals left to right, putting that recursive clause before `reach(X, Y) :- edge(X, Y).` makes every call enter `reach` again immediately without consulting a single `edge/2` fact. Just like `Call: grandparent(tom,X)` pushes `Call: parent(tom,_)` in the `P07` trace, each `reach` call pushes another `reach` frame onto the stack; unlike `parent(liz,X)`, nothing ever triggers `Fail` or `Redo`, so Prolog never backtracks to the base `edge` clause and never answers. Swapping `reach(X, Y) :- edge(X, Y).` to the top lets each call read `edge/2` facts first and find `d`, though the `a -> b -> c -> a` cycle still keeps the search unbounded on backtracking.

By contrast, `ancestor_r/2` puts its recursive clause first as `ancestor_r(A, D) :- parent(A, P), ancestor_r(P, D).` Here `parent(A, P)` runs first, forcing Prolog to bind `P` from a concrete `parent/2` fact before recursing—mirroring how `parent(tom,_)` binds `liz` or `bob` before `parent(P,X)` runs in the trace. Since the `parent/2` facts form a finite, acyclic family tree, each recursive step moves closer to a leaf like `liz`, `jim`, or `sue` where `parent` eventually fails and unwinds via `Fail` and `Redo`. Putting the recursive clause first therefore terminates with the exact same answers, only visiting deeper descendants before direct children.
