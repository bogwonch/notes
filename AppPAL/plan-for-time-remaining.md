Plan for Remaining Time
=======================

## Dates

-   PhD funding runs out DEADLINE: *2017-06-30*
-   Third year report (first draft of Thesis?) *2016-11-01*;

## Publications

-   PAPER ESSoS-DS 2014
    -   SecPAL might be useful
-   POSTER SOUPS 2015
-   PAPER ESSoS 2016
    -   AppPAL intro
    -   People don't seem to follow policies
-   PAPER iFM-DS 2016
    -   Start of BYOD stuff


## Future Work

### Case Study

-   We have policies written up for:
    -   NHS
-   SANS
-   Some other junk ones

#### Open questions

-   How do we know the AppPAL formalization is correct
    -   We don’t but it doesn’t actually matter. AppPAL acts as a tool for reasoning and modelling policies It helps us spot when there may be mistakes and when we need to check our assumptions.
-   Linting
    -   Consistency
        -   Do you have two AppPAL statements that say opposite things that are both true
    -   We have a plan for how to do it but needs schemas for AppPAL
        -   Plan actually needs testing and more thought
    -   …and actual implementation
    -   Completeness
        -   Do we have enough information to make a decision
        -   Sorta implemented :-D
    -   Needs testing and understanding when it gets decision wrong
    -   Redundancy
        -   How can you spot when a branch of choices will never be made, or when multiple statements are checked multiple times
        -   Suspect this is the hardest

#### Paper plan

-   Introduce extensions to AppPAL for checking lint properties
    -   must be some prior art on this for XACML?
-   Show how we can formalize a policy in AppPAL
-   Show how we found problems in the policy using our linters


### Knowledge Distribution

-   Current implementation relies on complete Assertion Context
-   Alternative would be to allow principals to exist on network and ask them for information

#### What would we need to implement

-   Proper AppPAL signing of statements and import
    -   Not too hard add an additional stage before parsing and a keystore
-   Recognition of when to ask
    -   Has a can-say with remote delegation, remote delegation enabled, not asked since *an appropriate length of time*
    -   Do we stop or continue?
-   How to ask
    -   Knowledge distribution protocol
        -   Simplest possible version
            A->B: ‘b’ says fact?
            B->A: (YES ‘b’ says fact. #{‘b’ says fact} | NO)
    -   Some similarity to remote DB query statements (didn’t PL/SQL have something like this?)


### Uncertain Knowledge

-   I’d _really_ like to do this.
-   We might not always be sure about any AppPAL statement.
    -   Especially when static analysis is involved
        -   False positives and negatives
    -   Different levels of trust

#### What would we need to implement

-   Full AppPAL evaluation
    -   i.e. don’t stop when we reach a proof and keep checking
    -   would probably need multi-threading and parallelism back
-   Independence checks
    -   this is an interesting problem on its own.
    -   in general we can tell whether two proofs are independent by looking and seeing whether they use any of the same assertions
        -   or by tagging
-   Assertion combination strategies
-   Maybe negative assertions
    -   which we’ll get from the schemas anyway


## Other things which need doing

-   WRITE YOUR THESIS
-   Tidy AppPAL
    -   Write testsuite
        -   and fix the inevitable fallout
    -   Use consistent output
    -   Better error messages
    -   Documentation

