AppPAL Linting
==============

Given an AppPAL policy we can use it as a basis for inference. Other
questions, however, include:

-   What decisions are *derivable* by a given speaker:
    -   from the assertion context supplied?
    -   by asking third parties who may provide additional facts when
        prompted?
-   Given two assertions for deriving the same statement:
    -   are they *equivalent*? (i.e. they check for the same facts)
    -   is one *redundant*? (i.e. one checks for a subset of the facts
        of the other)
-   Given a policy can we identify any rules which are:

    -   *Tautological*? (i.e. they are always true or always false)
    -   *Mutually exclusive*? (i.e. they can never **both** be true)
    -   If we know two facts should be *mutually exclusive*:
        -   Can we find examples of *contradiction*.

Examples
--------

### Simplification

We ought to report that this:

``` {.apppal}
'a' says X p if X r, X r.
```

Simplifies to:

``` {.apppal}
'a' says X p if X r.
```

Even though in practice the check of the second ='a' says X r= would be
quick (by caching).

Similarly something should be reported for policies of the form:

``` {.apppal}
'a' says X p if X q, X r.
'a' says X q if X r.
```

As the check for ='a' says X r= in the first rule is redundant by the
rule for ='a' says X q=.

### Decidability

Say we have just a rule (and no ground facts):

``` {.apppal}
'a' says X p.
```

We ought to report that it is not decidable.

Similarly the rules:

``` {.apppal}
'a' says X p if X q.
'a' says X q if X p.
```

Ought to be flagged as not decidable.

### Redundancy

Say we have two rules for inferring a fact:

``` {.apppal}
'a' says X p 
  if X q,
     X r.

'a' says X p
  if X r.
```

The first rule is redundant, as the second will be true whether or not
='a' says X p=.

### Tautology

A fact (an assertion with no conditionals or constraints) is always
true. A rule (an assertion with conditionals) is tautologically true if
all its conditions are always true and its constraint is true.

``` {.apppal}
'a' says 'b' p.
'a' says 'b' q if 'b' p.
```

∀ c ∈ cond(r): c ∈ 𝔸 constraint(r) = ⊤

------------------------------------------------------------------------

r ∈ 𝔸
