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

- Why does swapping the two clauses of `reach/2` change whether the search
  ever answers, while swapping the two clauses of `ancestor/2` only changes
  the order of the answers?
