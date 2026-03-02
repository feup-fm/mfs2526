# Week 01: Logic preliminaries

This practical reviews core ideas from propositional and predicate logic. You'll work through short, timed exercises on truth tables, implications, quantifiers, set operations, and translating between natural language and formal notation. Aim to complete each exercise in the suggested time and discuss answers with a colleague.

## Exercise 1: Evaluating Propositional Formulas

Let:

- $F$: “ $x > 0$ ”
- $G$: “ $x$ is even ”

For each $x$ below, evaluate the truth of the formulas:

1. $F \land G$  
2. $F \lor G$  
3. $\neg F$  
4. $F \Rightarrow G$

| $x$ | $F$ | $G$ | $F \land G$ | $F \lor G$ | $\neg F$ | $F \Rightarrow G$ |
|----:|:---:|:---:|:-----------:|:----------:|:--------:|:-----------------:|
| -2  | false | true  | false | true  | true  | true  |
| 3   | true  | false | false | true  | false | false |
| 4   | true  | true  | true  | true  | false | true  |

**Question:** Why is an implication $F \Rightarrow G$ considered true when $F$ is false?

**Solution:**  
By definition, $F \Rightarrow G$ is true iff $F$ is false **or** $G$ is true.  
Intuition: “If $F$ then $G$” makes a promise only when $F$ happens; if $F$ does not happen, the promise is not violated.

---

## Exercise 2: Truth Tables and Tautologies

Construct a truth table for:

$$
(F \Rightarrow G) \equiv (\neg F \lor G)
$$

| $F$ | $G$ | $F \Rightarrow G$ | $\neg F$ | $\neg F \lor G$ | $(F \Rightarrow G) \equiv (\neg F \lor G)$ |
|:---:|:---:|:-----------------:|:--------:|:---------------:|:------------------------------------------:|
| T | T | T | F | T | T |
| T | F | F | F | F | T |
| F | T | T | T | T | T |
| F | F | T | T | T | T |

1. **Is it a tautology?**  
   **Solution:** Yes. The final column is true in all rows.

2. **What is a tautology?**  
   **Solution:** A propositional formula that evaluates to true for **all** possible truth assignments of its variables.

---

## Exercise 3: Working with Sets

Let:

- $A = \lbrace 1,2,3,4 \rbrace$
- $B = \lbrace 3,4,5 \rbrace$
- $C = \lbrace 2,4,6 \rbrace$

Compute:

1. $A \cap B$  
2. $A \cup C$  
3. $A \setminus B$  
4. Is $C \subseteq A$? Justify.  
5. Find a set $D$ such that $D \subseteq A$ and $|D| = 2$.

### Solutions

1. $A \cap B = \lbrace 3,4 \rbrace$

2. $A \cup C = \lbrace 1,2,3,4,6 \rbrace$

3. $A \setminus B = \lbrace 1,2 \rbrace$

4. $C = \lbrace 2,4,6 \rbrace$ and $6 \notin A$, so  
   $C \nsubseteq A$.

5. Example answer (many possible):
   $D = \lbrace 1,4 \rbrace$.

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
2. “The request is accepted only if it is authenticated.”  
3. “The buffer is empty or the operation fails.”
4. “The system is running if and only if the log file exists.”

### Solutions

1. $R \Rightarrow L$

2. Let $Acc$ mean “request is accepted”:
   $$Acc \Rightarrow A$$

3. $E \lor F$

4. $(R \Rightarrow L) \land (L \Rightarrow R)$

---

## Exercise 5: Quantifiers (Predicate Logic)

Let $S = \lbrace 1,2,3,4,5 \rbrace$.

Evaluate the truth of:

1. $\forall x \in S : x < 6$  
2. $\exists x \in S : x^2 = 16$  
3. $\forall x \in S : (x \text{ is even}) \Rightarrow (x/2 \in S)$

### Solutions

1. **True** — every element of $S$ is less than $6$.

2. **True** — choose $x = 4$, since $4^2 = 16$.

3. **True** — check even elements only:
   - $x = 2 \Rightarrow x/2 = 1 \in S$
   - $x = 4 \Rightarrow x/2 = 2 \in S$

   For odd $x$, the implication is vacuously true.

---

## Exercise 6: Expanding Quantified Statements

Write each formula as an explicit conjunction or disjunction.

1. $\forall x \in \lbrace a,b,c \rbrace : P(x)$  
2. $\exists x \in \lbrace 1,2,3 \rbrace : x > 2$

### Solutions

1. Universal quantification becomes a conjunction:
   $$P(a) \land P(b) \land P(c)$$

2. Existential quantification becomes a disjunction:
   $$(1>2) \lor (2>2) \lor (3>2) \equiv false \lor false \lor true \equiv true$$

---

## Exercise 7: Small Specification

Suppose a variable `mode` may take values:
$$\lbrace \text{Idle}, \text{Running}, \text{Error} \rbrace.$$

Write formulas expressing:

1. "`mode` is always one of the allowed values."  
2. "`mode` is never both Running and Error."  
3. "If the system is Running, it is not Idle."

### Solutions

Let:
$$Modes = \lbrace \text{Idle}, \text{Running}, \text{Error} \rbrace.$$

1. Allowed-value constraint:
   $$mode \in Modes$$

2. Mutual exclusion (for clarity):
   $$\neg (mode = \text{Running} \land mode = \text{Error})$$

3. Running excludes Idle:
   $$(mode = \text{Running}) \Rightarrow (mode \neq \text{Idle})$$

**Note:** The second formula is actually redundant given the first, since `mode` can only take one value at a time. However, it explicitly states the mutual exclusion between Running and Error. We can also model mode as a set of states, in which case the second formula would be necessary to ensure that both states cannot be active simultaneously. In that case, a possible solution for the three formulas would be:
1. Allowed-value constraint:
   $$mode \subseteq Modes$$
2. Mutual exclusion:
   $$\neg (\text{Running} \in mode \land \text{Error} \in mode)$$
3. Running excludes Idle:
   $$(\text{Running} \in mode) \Rightarrow (\text{Idle} \notin mode)$$

## Exercise 8: A Simple Counter
Suppose that we have a natural variable $x$ that might be updated by some process. Whenever $x$ is updated, we refer to its new value as $x'$. We want to specify that $x$ can only be updated in one of two ways:
- It can be incremented by 1, i.e., $x' = x + 1$
- It can be reset to 0, i.e., $x' = 0$

Answer the following:
1. The initial value of $x$ is 0. Define a predicate $Init$ that captures this.
2. Define a predicate $Next$ that captures the allowed updates to $x$.
3. Assume that we are given the additional constraint that $x$ can only take values from 0 to 4 (inclusive). How do you update $Next$ to reflect this constraint?

### Solutions
1. The initial condition can be defined as:
   $Init == x = 0$
2. The allowed updates can be defined as:
   $Next == x' = x + 1 \lor x' = 0$
3. To ensure that $x$ only takes values from 0 to 4, we can add a constraint to $Next$:
   
   $Next == (x < 4 \land x' = x + 1) \lor (x' = 0)$
   
   However, it's better to introduce a separate predicate to keep $Next$ clean:
   
   $Range == x \in \lbrace 0..4 \rbrace$
   
   Then we can write:
   
   $Next == (Range \land x' = x + 1) \lor (x' = 0)$

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

### Step 6: Understanding the Specification

Answer the following:

1. What does the variable `x` represent?
2. What is the role of `Init`?
3. What is the role of `Next`?

#### ✔ Solution

1. `x` represents the entire state of this simple system (a counter).
2. `Init` defines the allowed initial states.
3. `Next` defines all allowed transitions between states.

### Step 7: Add a Property to Check

Define a new invariant in the module:

```tla
NonNegative == x >= 0
```

Add `NonNegative` to the invariants checked by TLC. Run TLC again. You'll notice that TLC confirms that this property holds (no errors are found).

### Step 8: Adding a Incorrect Invariant
Now, define a new invariant:

```tla
DifferentFromFour == x /= 4
```

Add `DifferentFromFour` to the invariants checked by TLC. Run TLC again. This time, TLC will find a counterexample that violates this invariant (when `x` reaches 4).

You'll get the message "Invariant DifferentFromFour is violated." and a counterexample trace showing how the system can reach a state where `x = 4`. Play around with different invariants that are not valid and see how TLC finds counterexamples.