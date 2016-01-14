Properties of AppPAL policies
=============================

Three interesting properties an AppPAL policy may have:

Completeness
:   given a rule and a policy do we have enough statements in that policy to use the rule successfully?

Consistency
:   if we have rules of the form `can` and `cannot` are there examples of things which both are true?

Redundancy
:   can we spot when two rules are the same, or when a rule checks something which is always checked as part of a conditional?


Completeness
------------

### Examples

~~~~
'x' says A p
  if A q,
     A r.

'x' says 'y' r.
~~~~

Here the statement `'x' says 'y' r.` is complete (facts don't require any further information to be true); but `'x' says A p...` is not as we have no means to determine whether `'x' says A q.`

~~~~
'x' says 'y' can-say A p.
~~~~

In this example we don't know whether `'x' says A p.` is complete: we know we
can delegate to `'y'` to help make the decision but as we don't have any
statements from `'y'` we'd need to confirm with them.

~~~~
'x' says 'y' can-say A p.

'y' says A p
  if A q.
~~~~

This time we know that `'y' says A q.` is incomplete, and hence `'y' says A
p...` is incomplete. Since `'x' says 'y'.` depends on an incomplete statement
(from `'y'`) it must be incomplete too.

### Checking

We want to check whether a predicate `p` is complete for a speaker `'x'` given
an assertion context `AC`. To do this we define a set `ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖` of speakers and
predicate, where membership means that the speaker has rules to say that
something satisfies the predicate which are themselves complete.

#### Cond rule

A predicate is complete for a speaker if there exists a rule with zero or more
conditionals, where each conditional predicate is also complete for the speaker.
Hence all facts (rules without conditionals) are complete as they satisfy this
rule trivially.

    ∃('x' says ∗ p if ∗ p₁, ..., ∗ pₙ) ∈ AC s.t.
    ∀i ∈ 1..n.  AC ⊢ ('x', pᵢ) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖
    -------------------------------------------- (completeness-cond)
    AC ⊢ ('x', p) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖


#### Can-Say rule

For the can-say rule we need two statements: the `can-say` statement allowing
the delegation, and the confirmation from the delegated party that the rules
outcome is valid.

When checking completeness we need to know the delegation is complete for the
speaker, and that the delegated predicate is complete for the delegated speaker.
Since `can-say` is superficially different from the standard predicates we
transform it into a *Y`-can-say-`p* predicate using the same strategy as the
cond completeness checks.


    ∃('x' says 'y' can-say ∗ p if ∗ p₁, ..., ∗ pₙ) ∈ AC s.t.
    ∀i ∈ 1..n.  AC ⊢ ('x', pᵢ) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖
    -------------------------------------------------------- (completeness-can-say-delegation)
    AC ⊢ ('x', 'y'-can-say-p) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖

Finally the full test for completeness is that the delegation is complete for
the speaker, and the delegated statement is complete for the delegated speaker.

    ∃'y' ∈ speakers(AC) s.t.
    AC ⊢ ('x', 'y'-can-say-p) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖
    AC ⊢ ('y', p) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖
    ------------------------------------ (completeness-can-say)
    AC ⊢ ('x', p) ∈ ℂ𝕠𝕞𝕡𝕝𝕖𝕥𝕖


Consistency
-----------

**TODO**


Redundancy
----------

### Examples

~~~~
'x' says A p
  if A q,
     A r.

'x' says A r
  if A q,
     A s.
~~~~

In the rule `'x' says A p...` the check that `'A q` is redundant as it is duplicated in the rule for `'x' says A r...`.


