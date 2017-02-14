% AppPAL with Confidence
% Joseph Hallett

AppPAL with Confidence
======================

AppPAL is a language for expressing decisions and information about apps.
For example *Alice* might say that an app isn't malware:

~~~~
'alice' says 'angrybirds' isntMalware.
~~~~

This is fine but what if Alice isn't absolutely certain that it isn't malware?
Anti-virus vendors tend to change their minds from time to time about apps.
Alice might be only 80% confident about her statement: can we encode the level of trust in the assertion and what does it mean?

~~~~
'alice' say 'angrybirds' isntMalware
  with 80% confidence.
~~~~


Properties
----------

* If a statement is said with 100% confidence then this is equivalent to standard AppPAL.
* If a statement is said with 0% confidence then this is equivalent to the statement being missing.

Evaluation
----------

### Simple Rule

Take the following example policy:

~~~~
'alice' says App isInstallable
  if App isntMalware,
     App isGood.

'alice' says 'angrybirds' isntMalware
  with 80% confidence.

'alice' says 'angrybirds' isGood
  with 70% confidence.
~~~~

Alice queries whether the *Angry Birds* is installable and comes up with the following proof:

    AC ⊢ 'alice' says 'angrybirds' isInstallable
      AC ⊢'alice' says 'angrybirds' isntMalware with 80% confidence.
      --------------------------------------------------------------
      AC ⊢ 'alice' says 'angrybirds' isGood with 70% confidence.
      ----------------------------------------------------------

How confident is Alice in her statement?
One strategy might be to take the product of the probabilities of the conditionals.

    ∃(A says f if f₁, ... fₙ where c) ∈ AC
    ∀i ∈ [1..n]. AC, D ⊢ A says fᵢ.    ⊢ c
    ------------------------------------------------------------
    AC, D ⊢ A says f with (∏ᵢ confidence(A says fᵢ)) confidence.

What if Alice is only 90% confident that this rule is even a good way of determining installable apps?

    ∃(A says f if f₁, ... fₙ where c with p confidence.) ∈ AC
    ∀i ∈ [1..n]. AC, D ⊢ A says fᵢ.    ⊢ c
    ----------------------------------------------------------------
    AC, D ⊢ A says f with p * (∏ᵢ confidence(A says fᵢ)) confidence.

If the confidence of everything is 100% then the overall confidence is 100% (one multiplied by one is still one), so this is no different from standard AppPAL.
If the confidence of any statement is 0% then the overall confidence is also 0% (zero multiplied by anything is zero), so again this is no different.

We can do similar things for the *can-say* and *can-act-as* rules:

    AC, inf ⊢ A says B can-say D f with X confidence.    AC, D ⊢ B says f with Y confidence.
    ----------------------------------------------------------------------------------------
    AC, inf ⊢ A says f with (X*Y) confidence

    AC, D ⊢ A says B can-act-as C with X confidence.    AC, D ⊢ A says C p with Y confidence.
    -----------------------------------------------------------------------------------------
    AC, inf ⊢ A says B p with (X*Y) confidence

This, however, assumes that all the proofs are independent (i.e. they don't reuse parts of each other's proofs).


### Requirements on Confidence

Lets alter AppPAL's syntax again: this time conditionals are allowed to come with a requirement on their confidence.
In other words in any rule Alice can specify a minimum level of confidence she requires for a proof to be accepted.

~~~~
'alice' says App isInstallable
  if 90% confident App isntMalware,
     50% confident App isGood.
~~~~

If a conditional is missing its confidence annotation then it will be accepted if the confidence is >= 1%.
We always use >= to simplify the rules.

    ∃(A says f if r₁ confident f₁, ... rₙ confident fₙ where c with p confidence.) ∈ AC
    ∀i ∈ [1..n]. AC, D ⊢ A says fᵢ ∧ confidence(A says fᵢ) >= rᵢ .    ⊢ c
    -----------------------------------------------------------------------------------
    AC, D ⊢ A says f with p * (∏ᵢ confidence(A says fᵢ)) confidence.

Other places we could introduce required confidences:

  * Delegated statements: Bob can say something only if he is at least *r* confident in it.
  * Delegations: Bob is only allowed to say something if we are at least *r* confident we can delegate to him.
  * Role assignment: Bob *can-act-as* the IT manager if we are at least *r* confident he can fulfill this role.

### Multiple Proofs

Suppose Alice knows two ways of deciding if an app is good.

~~~~
'alice' says 'bob' can-say 'app' isGood.
'alice' says 'claire' can-say 'app' isGood with 60% confidence.

'bob' says 'app' isGood with 90% confidence.
'claire' says 'app' isGood with 95% confidence.
~~~~

Using the *can-say* rule we can construct the proofs

    P1: ⊢ 'alice' says 'app' isGood with 90% confidence
          ⊢ 'alice' says 'bob' can-say 'app' isGood.
          ------------------------------------------
          ⊢ 'bob' says 'app' isGood with 90% confidence.
          ----------------------------------------------

    P2: ⊢ 'alice' says 'app' isGood with 57% confidence
          ⊢ 'alice' says 'claire' can-say 'app' isGood with 60% confidence.
          -----------------------------------------------------------------
          ⊢ 'claire' says 'app' isGood with 95% confidence.
          -------------------------------------------------

Lets imagine Alice wants to be *really* sure that the App is good with confidence over 95%.
Neither proof gives her sufficient confidence the app is good, but when combined together they do (assuming Bob and Claire are independent).

    confidence('alice' says 'app' isGood) = confidence(P1) + confidence(P2) - confidence(P1) * confidence(P2).
                                          = 95.7%

Alternatively suppose Alice has the following policy:

~~~~
'alice says 'bob' can-say inf 'app' isGood.
'alice' says 'claire' can-say 'app' isGood with 60% confidence.

'bob' says 'claire' can-say 'app' isGood with 90% confidence.
'claire' says 'app' isGood with 95% confidence.
~~~~

This time the suppose that Bob delegates to Claire who is also trusted to make the same decision by Alice, but with much less confidence:

    P1: ⊢ 'alice' says 'app' isGood with 85.5% confidence
          ⊢ 'alice' says 'bob' can-say inf 'app' isGood.
          ----------------------------------------------
          ⊢ 'bob' says 'app' isGood with 85.5% confidence
            ⊢ 'bob' says 'claire' can-say 'app' isGood with 90% confidence.
            ---------------------------------------------------------------
            ⊢ 'claire' says 'app' isGood with 95% confidence.
            -------------------------------------------------


    P2: ⊢ 'alice' says 'app' isGood with 57% confidence
          ⊢ 'alice' says 'claire' can-say 'app' isGood with 60% confidence.
          -----------------------------------------------------------------
          ⊢ 'claire' says 'app' isGood with 95% confidence.
          -------------------------------------------------

This time P1 and P2 are not independent (they share a statement from Claire).
The confidence should not be combined as before, but I'm not sure how: possibly Bob's proo)f should be discarded.

One solution might be to ignore getting the maths right and just set the confidence on delegated statements much lower than the direct ones.

~~~~
'alice says 'bob' can-say 'app' isGood with 90% confidence.
'alice says 'bob' can-say inf 'app' isGood with 20% confidence.
~~~~

This seems to copy how trust works in engineering management:
  one person you don't trust says something,
  a lot of people you do trust go along with it because they do trust them,
  you end up having to go along with it because there enough other people are anyway.


### Confidence and Contradiction

We can imagine some rules having negative forms: *is* and *isNot*, *can* and *cannot*.
Whenever there is inconsistency between the two forms we need to decide how to resolve it.

One approach could be to define an additional metric *belief* as the
difference between the confidence in a statement and the confidence in
its negated form, or zero if the difference is negative.

	belief(X) = max(0, confidence(X) - confidence(¬X))
	
If the belief in a statement is 0, then we don't do it.

If the confidence in both $X$ and $¬X$ is high then the belief is low and this indicates a consistency problem.

If the confidence in both $X$ and $¬X$ is low then the belief is low; and may want to be treated as a _missing_ statement (i.e. remove both statements from the AC until we have some belief in one or other of them).

If the confidence in $X$ is high, and the confidence in it's negated form is low (or missing), then the belief is high, which mimics current behavior.


What Can We Do With This?
-------------------------

Having a logic of authorization with confidence allows us to express scenarios where we aren't fully confident in our answers.
This might be helpful in scenarios such as:

 *  __Unsound static analysis tools.__ Static analysis tools,
    especially heuristic based ones, are often not completely accurate
    and have a false positive/negative rate.  With confidence
    annotations we can include these confidences in our policies: for
    example consider a malware detection tool that has a 5% false
    positive rate, and a 20% false negative rate:
	
    ~~~~
    'user' says X isMalware with confidence 95%
      if X isApp
      where malwareTool(X) = true.
    
    'user' says X isNotMalware with confidence 80%
      if X isApp
      where malwareTool(X) = false.
    ~~~~
	
 *  __Repeating statements.__ where we might like to get the same
    statement from multiple entities to really confirm something.  For
    example we might only want to buy an app if three friends think it
    is good.  Under the SecPAL rules if any one says the app is good then 
    we have all the confidence we need.
    
    With AppPAL with confidences we can be more subtle: delegate the
    `isGood` decision to the friends, but only have 33% confidence any
    of them can get it right.  If we assume each of the friends are
    independent and we have statements of absolute confidence from each
    of them then (depending on how we calculate it) we might take
    agreement from all three to give us ~96% confidence overall (sum
    minus product) if all agree, and ~55% confidence if only two agree.
    If one disagrees and says so (and we calculate belief as described
    earlier we'd end with only ~22% belief.
