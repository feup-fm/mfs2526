# Week 01: Logic Preliminaries

This practical reviews core ideas from propositional and predicate logic. You'll work through short, timed exercises on truth tables, implications, quantifiers, set operations, and translating between natural language and formal notation. Aim to complete each exercise in the suggested time and discuss answers with a colleague.

## Exercise 1: Evaluating Propositional Formulas

Consider the folllowing propositions:

- $F$: “ $x > 0$ ”
- $G$: “ $x$ is even ”

For each $x$ below in the table, evaluate the truth of the formulas:

1. $F \land G$  
2. $F \lor G$  
3. $\neg F$  
4. $F \Rightarrow G$

| $x$ | $F$ | $G$ | $F \land G$ | $F \lor G$ | $\neg F$ | $F \Rightarrow G$ |
|----:|:---:|:---:|:-----------:|:----------:|:--------:|:-----------------:|
| -2  | ? | ?  | ? | ?  | ?  | ?  |
| 3   | ?  | ? | ? | ?  | ? | ? |
| 4   | ?  | ?  | ?  | ?  | ? | ?  |

**Question:** Why is an implication $F \Rightarrow G$ considered true when $F$ is false?

---

## Exercise 2: Truth Tables and Tautologies

Construct a truth table for:

$$
(F \Rightarrow G) \equiv (\neg F \lor G)
$$

| $F$ | $G$ | $F \Rightarrow G$ | $\neg F$ | $\neg F \lor G$ | $(F \Rightarrow G) \equiv (\neg F \lor G)$ |
|:---:|:---:|:-----------------:|:--------:|:---------------:|:------------------------------------------:|
| T | T | ? | ? | ? | ? |
| T | F | ? | ? | ? | ? |
| F | T | ? | ? | ? | ? |
| F | F | ? | ? | ? | ? |

1. **Is it a tautology?**  

2. **What is a tautology?**  

---

## Exercise 3: Working with Sets

Consider the following sets:

- $A = \lbrace 1,2,3,4 \rbrace$
- $B = \lbrace 3,4,5 \rbrace$
- $C = \lbrace 2,4,6 \rbrace$

Compute:

1. $A \cap B$  
2. $A \cup C$  
3. $A \setminus B$  
4. Is $C \subseteq A$? Justify.  
5. Find a set $D$ such that $D \subseteq A$ and $|D| = 2$.

---

## Exercise 4: Translating Between Logic and English

Rewrite the following statements formally.

Let:

- $R$: system is running  
- $L$: log file exists  
- $A$: request is authenticated  
- $E$: buffer is empty  
- $F$: operation fails  

1. “If the system is running, then the log file exists.”  
2. “The request is accepted only if it is authenticated.” (_Note: For this one, you need to introduce a new proposition..._)
3. “The buffer is empty or the operation fails.”
4. “The system is running if and only if the log file exists.”

---

## Exercise 5: Quantifiers (Predicate Logic)

Let $S = \lbrace 1,2,3,4,5 \rbrace$.

Evaluate the truth of:

1. $\forall x \in S : x < 6$  
2. $\exists x \in S : x^2 = 16$  
3. $\forall x \in S : (x \text{ is even}) \Rightarrow (x/2 \in S)$

---

## Exercise 6: Expanding Quantified Statements

Write each formula as an explicit conjunction or disjunction.

1. $\forall x \in \lbrace a,b,c \rbrace : P(x)$  
2. $\exists x \in \lbrace 1,2,3 \rbrace : x > 2$

---

## Exercise 7: Small Specification

Suppose a variable `mode` may take values:
$$\lbrace \text{Idle}, \text{Running}, \text{Error} \rbrace.$$

Write formulas expressing:

1. "`mode` is always one of the allowed values."  
2. "`mode` is never both Running and Error."  
3. "If the system is Running, it is not Idle."


## Exercise 8: A Simple Counter
Suppose that we have a natural variable $x$ that might be updated by some process. Whenever $x$ is updated, we refer to its new value as $x'$. We want to specify that $x$ can only be updated in one of two ways:
- It can be incremented by 1, i.e., $x' = x + 1$
- It can be reset to 0, i.e., $x' = 0$

Answer the following:
1. The initial value of $x$ is 0. Define a predicate $Init$ that captures this.
2. Define a predicate $Next$ that captures the allowed updates to $x$.
3. Assume that we are given the additional constraint that $x$ can only take values from 0 to 4 (inclusive). How do you update $Next$ to reflect this constraint?

## Exercise 9: Your First Steps with TLA+ and the Toolbox

In this exercise you will create and run your **first TLA+ specification** using the TLA+ Toolbox. We model a very simple system: a counter that can increase or reset to zero.
We will assume that our counter can only take natural numbers from 0 to 4 (inclusive). We use the variable `x` to represent the current value of the counter. The system starts with `x = 0`, and at each step, it can either increment `x` by 1 or reset it to 0. When `x` reaches 4, it cannot be incremented further, but it can still reset to 0.
We will also check that `x` never becomes negative.

### Step 1: Create the Specification

1. Open the **TLA+ Toolbox**.
2. Click **File → Open → Add New Spec...**.
3. Click `Browse...` and choose a directory to save your specification. Name it `SimpleCounter.tla`.
4. Name the specification `SimpleCounter`
5. Click **Finish**.
6. You should see a new empty module named `SimpleCounter`:
   
```tla
---------------- MODULE SimpleCounter ----------------

=============================================================================
```   

### Step 2: Write the Module

Replace the contents of the generated module with:


```tla
---------------- MODULE SimpleCounter ----------------
EXTENDS Naturals

(*
A simple bounded counter.

State variable: x ∈ 0..4

Initial state: x = 0

Next steps: either increment (if x < 4) or reset to 0
*)

VARIABLE x

Init ==
x = 0

Inc ==
/\ x < 4
/\ x' = x + 1

Reset ==
x' = 0

Next ==
Inc \/ Reset

(*
TLC needs a state constraint to restrict the explored states.
We constrain x to stay within 0..4.
*)
StateConstraint ==
x \in 0..4
=============================================================================
```

Save the file.

### Step 3: Create a Model

1. In the Spec Explorer pane, right-click `models...` and select **New Model...**.
2. Name the model `MC1` and click OK.

### Step 4: Configure the Model

Set the behavior specification to: `Initial Predicate and next-state relation`. In the Init field, enter `Init`. In the Next field, enter `Next`.

Expand the section Invariants. Add the invariant: `StateConstraint` (by clicking `Add` and entering `StateConstraint`).

### Step 5: Run TLC

Click **Run Model** (or click the play button).

In the sub-actions of next-state, you should see that TLC explores states where `x` takes values from 0 to 4, and it confirms that the invariant holds in all states. There is one state found for the Action `Init`; this is the initial state where `x = 0`. There are four states found (all distinct) for the Action `Inc` (where `x` is incremented); this corresponds to the transitions from 0 to 1, 1 to 2, 2 to 3, and 3 to 4. Finally, there are five states found (0 distinct) for the Action `Reset` (where `x` is reset to 0). This corresponds to the transitions from 0 to 0, 1 to 0, 2 to 0, 3 to 0, and 4 to 0. 

Because there are no errors, it means that no states violate the invariant, so TLC confirms that the property holds.

**This represents all possible executions of the system.**

## Step 6: Understanding the Specification

Answer the following:

1. What does the variable `x` represent?
2. What is the role of `Init`?
3. What is the role of `Next`?

## Step 7: Add a Property to Check

Define a new invariant in the module:

```tla
NonNegative == x >= 0
```

Add `NonNegative` to the invariants checked by TLC. Run TLC again. You'll notice that TLC confirms that this property holds (no errors are found).

## Step 8: Adding a Incorrect Invariant
Now, define a new invariant:

```tla
DifferentFromFour == x /= 4
```

Add `DifferentFromFour` to the invariants checked by TLC. Run TLC again. This time, TLC will find a counterexample that violates this invariant (when `x` reaches 4).

You'll get the message "Invariant DifferentFromFour is violated." and a counterexample trace showing how the system can reach a state where `x = 4`. Play around with different invariants that are not valid and see how TLC finds counterexamples.