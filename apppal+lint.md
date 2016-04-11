% Checking the properties of AppPAL policies
% Joseph Hallett

Properties of AppPAL policies
=============================

Three interesting properties an AppPAL policy may have:

- **Completeness**: given a rule and a policy do we have enough statements in that policy to use the rule successfully?

- **Consistency**:  if we have rules of the form `can` and `cannot` are there examples of things which both are true?

- **Redundancy**:   can we spot when two rules are the same, or when a rule checks something which is always checked as part of a conditional?


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


Redundancy
----------

### Examples

~~~~
'x' says A p
  if A q,
     A q.
~~~~

Obviously this query contains a redundant conditional as `A q` is duplicated.

~~~~
'x' says A p
  if A q,
     A r.

'x' says A r
  if A q,
     A s.
~~~~

In the rule `'x' says A p...` the check that `'A q` is redundant as it is duplicated in the rule for `'x' says A r...`.

~~~~
'x' says A p
  if A q.
	 
'x' says A q
  if A s, 
     A t.
~~~~

This time the `'x' says A p...` is redundant as any proof of `'x' says A q` implies `'x' says A p`.  We don't need both statements.

~~~~
'x' says A p
  if A q.

'x' says A q
  if A s,
     A t.
	 
'x' says 'y' can-say A p.
~~~~

In this case, however, `'x' says A p...` is _not_ redundant as there are multiple means for determining `'x' says A p`, and no one statement has the same proof. 

~~~~
'x' says A p
  if A q.
  
'x' says A p
  if A q,
     A r,
     A s,
     A t.
~~~~

Here the second rule is redundant 

### Checking

#### Naive strategy

When constructing a proof tree for an AppPAL statement the leaves of the tree must be ground facts.
A proof can be thought of as the subset of ground facts, and the subset of rules from the assertion context that can be used to prove the statement.

Given a rule we expand by unifying and inlining all the possible proofs of it's conditionals until the rule only depends on the presence of facts (i.e. no further rules are needed to evaluate the statement).
For example: given the policy:

~~~~
'x' says A p
  if A q,
     A r.
	
'x' says A r 
  if A q,
     A s.
~~~~

Then we expand the rule `'x' says A p...` thus by substituting the `A r` for its respective rule's conditionals.

~~~~
'x' says A p
  if A q,
     A q,
	 A s.
~~~~

After expanding we see that all the expansions of that rule depend on the conditional `A q` multiple times.
Therefore there is some redundancy in the statement.

Another example:

~~~~
'x' says A p
  if A q.
	 
'x' says A q
  if A s, 
     A t.
~~~~

This time we expand both rules:

~~~~
'x' says A p
  if A s,
     A t.
	 
'x' says A q
  if A s, 
     A t.
~~~~

Since all the rules for `'x' says A p...` have an identical rule for `'x' says A q...`, they are equivalent and therefore one is redundant (probably `'x' says A p` as it depends on `'x' says A q` but not the other way round).

A third example: say after expansion we have the following two rules:

~~~~
'x' says A p
  if A q.
  
'x' says A p
  if A q,
     A r,
     A s,
     A t.
~~~~

Again there is redundancy as the conditional facts needed for one rule is a subset of all the other ones.


Consistency
-----------

Strictly speaking, AppPAL does not allow for any forms of negation.
With some predicates, however, it is natural to want to write a negated form: for instance `isInstallable` and `isNotInstallable`, `canConnectTo` and `cannotConnectTo`.

When checking consistency we want to ensure that rules would not trivially allow both forms to be true (and have to rely on fallbacks and conflict resolution).

### Examples

~~~~
'x' says A isP
  if A isNotP.
~~~~

Trivially inconsistent.

~~~~
'x' says A isP
  if A q.
  
'x' says A isNotP
  if A r.
~~~~

Consistent if `'x' says A q` and `'x' says A r` are mutually exclusive.

### Checking

As with the redundancy checks, expand all rules through unification and inlining.
If any of the expanded rules have an inverted form with the same conditional facts then there must be an inconsistency.

~~~~
'x' says A isP
  if A q,
     A r.
	 
'x' says A isNotP
  if A s,
     A t.
	 
'x' says A q 
  if A s,
     A r.
  
'x' says A q
  if A t.
  
'x' says A r
  if A s,
     A u.
~~~~ 

This expands to:

~~~~
'x' says A isP
  if A s,
     A s,
	 A u,
     A s,
	 A u.
	 
'x' says A isP
  if A t,
     A s,
	 A u.
 
 'x' says A isNotP
   if A s,
      A t.
~~~~

Which is inconsistent if `'x' says A u` is mutually exclusive with `'x' says A isNotP`.

#### Way I'd actually do it

Check at run time that whenevr an `is`* or `can`* statement is used, and the negated form exists in the assertion context, check that the two are not in conflict.
Additionally keep track of facts said by other speakers to ensure which ones are consistent with each other.
