# Week 02: TLA+ Specifications
In this practical, you'll learn how to write TLA+ specifications for simple systems. We'll cover the basics of TLA+ syntax, how to model system behavior, and how to use the TLC model checker to verify properties of your specifications.
The ["_TLA+ Cheatsheet_" (Summary of TLA+)](https://github.com/feup-fm/mfs2526/blob/main/resources/summary-standalone.pdf) can be useful for some of the exercises.

## Exercise 1: Modeling a Tiny C Program
Consider the following C program:

```c
int x = 0;
while (x < 4) {
    x = x + 1;
}
``` 
This program initializes a variable `x` to 0 and then repeatedly increments `x` until it reaches 4. We want to model this behavior in TLA+. We also want to check that `x` never becomes negative. Model this program in TLA+ and verify the property that `x` is always greater than or equal to 0.

**Hint:** Uncheck the "Check deadlock" option in the TLC model checker, because this program will reach a state where it cannot make any further transitions (when `x` reaches 4), and we want to verify that the property holds in all reachable states, including the terminal state. Checking for deadlock will be important in future exercises where we want to ensure that the system does not reach a state where it cannot proceed, but in this case, we are only interested in verifying the property about `x`, so we can safely ignore deadlock for this exercise.


## Exercise 2: Modeling a C Program with Conditional Logic
Consider the following C program:

```c
#include <stdio.h>
#include <assert.h>

int withdraw(int balance, int amount) {
    if (amount <= balance) {
        balance = balance - amount;
    }
    return balance;
}

int main() {
    int balance = 100;

    balance = withdraw(balance, 30);
    balance = withdraw(balance, 50);
    balance = withdraw(balance, 40);  // what should happen?

    printf("Final balance: %d\n", balance);
    return 0;
}
```
This program implements a simple withdrawal function that checks if the requested amount is less than or equal to the current balance before allowing the withdrawal. However, the program can improve. Model this program in TLA+ and identify any potential issues with the `withdraw` function. Suggest a way to fix the issue in the TLA+ model.

**Hint 1:** One way to model this is to use a variable `balance` that is initially set to 100, and then define a `Withdraw` action that takes an `amount` as input and updates the `balance` accordingly. With the `Withdraw` action defined, the `Next` action can be defined as an existential quantification over possible withdrawal amounts, allowing us to model the behavior of the program as it processes multiple withdrawal requests. Existential quantification is represented in TLA+ using the `\E` operator; for example `\E amount \in Nat : Withdraw(amount)` means "there exists an amount in the natural numbers such that the `Withdraw` action can be performed with that amount". 

**Hint 2:** The goal of this exercise is to demonstrate how specifications can help clarify the intended behavior of a system and identify potential issues. There is no need to run the TLA+ model checker for this exercise, as the issue can be identified through careful analysis of the specification. However, fix the issue in the specification and if you want to verify that the issue has been fixed, you can use the TLC model checker to check, for examnple, that the balance never becomes negative. You can use a finite set in the existential quantification to make the model checkable, for example by defining a constant `MAX_WITHDRAWAL` and using `\E amount \in 0..MAX_WITHDRAWAL : Withdraw(amount)`.
You can also check that if a withdrawal is successful, then the balance decreases, and if a withdrawal is unsuccessful, then the balance remains the same. However, this requires properties on the next state, which cannot be expressed as invariants, but can be checked as properties in TLC. We will discuss this in more detail in future exercises, but feel free to explore this on your own if you're interested!


## Exercise 3: Does This C Program Terminate?
Consider the following C program:

```c
#include <stdio.h>
#include <stdint.h>

#define MOD 8
#define STEP 2

static uint8_t seq = 1;

uint8_t poll_seq(void) {
    seq = (uint8_t)((seq + STEP) % MOD);
    return seq;
}

int main(void) {
    uint8_t target = 0;
    uint8_t s = seq;

    while (s != target) {
        s = poll_seq();
        // ... there could be more code here, but we abstract it away for this exercise.
    }

    puts("Finished.");
    return 0;
}
```
This program initializes a sequence number `seq` to 1 and then repeatedly calls the `poll_seq` function, which increments `seq` by a fixed step and wraps around using modulo. The program continues to call `poll_seq` until `s` equals a target value (0 in this case). Model this program in TLA+ and determine whether the program terminates. If it does not terminate, explain why and suggest a way to fix the issue in the TLA+ model.

**Hint:** We can use constants to represent the parameters of the program, such as `MOD`, `STEP`, `target`, and `seq`. This allows us to easily change these parameters in the model without modifying the specification itself. We can also define an invariant that captures the behavior of the sequence number and check whether it can ever reach the target value. In TLA+, we define a constant by using the `CONSTANTS` keyword as in the following example:
```tla
CONSTANTS MOD, STEP, target, seq
```


## Exercise 4: Modeling a Periodic Credit System
Consider the following C program:
```c
#include <stdio.h>
#include <stdint.h>

#define MAXT 8
#define PERIOD 3

static uint8_t last = 0;
static int credit = 0;

static int elapsed(uint8_t a, uint8_t b) {
    int d = (int)(b - a) % MAXT;
    return d;
}

static void tick(uint8_t now) {
    int dt = elapsed(last, now);

    if (dt >= PERIOD) {
        credit++;
    }

    last = now;
}

int main(void) {
    // A couple of "normal" inputs plus a wrap-around one.
    uint8_t inputs[] = {1, 4, 7, 0, 3};
    for (int i = 0; i < (int)(sizeof(inputs)/sizeof(inputs[0])); i++) {
        tick(inputs[i]);
    }

    printf("credit=%d\n", credit);
    return 0;
}
```
This program implements a periodic credit system where the `tick` function is called with a timestamp `now`, and it updates the `credit` based on the elapsed time since the last tick. The `elapsed` function calculates the time difference, accounting for wrap-around using modulo. However, there is a potential issue with how the `elapsed` function handles the wrap-around, which can lead to incorrect credit calculations. 
Model this program in TLA+ and identify the issue with the `elapsed` function.

Some notes:
 - `uint8_t` is an unsigned 8-bit integer, which means it can represent values from 0 to 255. When it reaches 255 and increments, it wraps around back to 0. This behavior is important to consider when modeling the `elapsed` function, as it can lead to negative values if not handled correctly.
 - The semantics of the division and modulo operations in C is different from TLA+. In C, the result of the modulo operation can be negative if the dividend is negative, while in TLA+, the result of the modulo operation is always non-negative. This difference can lead to discrepancies between the behavior of the C program and the TLA+ model if not accounted for properly. To mimic the C behavior in TLA+, we can define division and modulo operations as follows:
```tla
C_Div(x, y) == IF x < 0 THEN -((-x) \div y) ELSE x \div y
C_Mod(x, y) == x - y * C_Div(x, y)
``` 
 - With the previous definition, the `elapsed` function can be defined in TLA+ as follows:
```tla
Elapsed(a, b) == C_Mod((b - a), MAXT)
``` 


## Exercise 5: A Traffic Light System
Consider a simple traffic light system that cycles through three states: Green, Yellow, and Red. The traffic light changes state every 60 seconds, following the sequence: Green -> Yellow -> Red -> Green. 

  1. Model this traffic light system in TLA+. Ensure that the traffic light never transitions directly from Green to Red without passing through Yellow. Define an invariant `TypeOK` that captures the allowed states of the traffic light and check it using the TLC model checker.


  2. If we want to check properties about the next state, we cannot express them as invariants, but we can check them as properties in TLC. This means that properties are checked on the transitions between states, rather than on the states themselves. The property stating that if the current state is Green, then the next state must be Yellow can be expressed as follows:
```tla
[] [(state = "Green" => state' = "Yellow")]_state
```
This property states that in all reachable states, if the current state is Green, then the next state must be Yellow. The `[]` operator means "always", and the `_state` suffix indicates that the variable `state` might not change in the next state, but if it does, it must satisfy the condition specified in the property. We will discuss these "temporal operators" in more detail in future exercises, but for now, use this knowledge to verify that the traffic light cycles through the states in the correct order (Green -> Yellow -> Red -> Green).


  3. Check explicitly that it is not possible for the traffic light to transition directly from Green to Red without passing through Yellow.
  4. Extend the model to include a pedestrian crossing button. When the button is pressed, the traffic light should transition to Red at the next state change, regardless of the current state. Model this behavior in TLA+ and verify that the traffic light correctly responds to the pedestrian crossing button.

## Exercise 6: Records in TLA+
In TLA+, we can use records to group related variables together. For example, we can represent the state of a traffic light system using a record that contains the current color of the traffic light and the time remaining until the next state change. This could be modeled as follows. **Use TLA+ Toolbox to explore the following specification and verify that the traffic light system behaves as expected.** 

```tla
EXTENDS Naturals

VARIABLE tl
\* tl is a record: [color |-> ..., time |-> ...]

States == {"Green", "Yellow", "Red"}

\* Duration of each light
Duration(color) ==
    CASE color = "Green"  -> 60
       [] color = "Yellow" -> 5
       [] color = "Red"    -> 55

Init ==
    tl = [color |-> "Green", time |-> Duration("Green")]

Next ==
    \/ /\ tl.time > 0
       /\ tl' = [tl EXCEPT !.time = @ - 1]
       \* countdown while staying in the same color

    \/ /\ tl.time = 0
       /\ tl.color = "Green"
       /\ tl' = [color |-> "Yellow",
                 time  |-> Duration("Yellow")]

    \/ /\ tl.time = 0
       /\ tl.color = "Yellow"
       /\ tl' = [color |-> "Red",
                 time  |-> Duration("Red")]

    \/ /\ tl.time = 0
       /\ tl.color = "Red"
       /\ tl' = [color |-> "Green",
                 time  |-> Duration("Green")]
```
In this model, we use a record `tl` to represent the state of the traffic light system, which includes both the current color and the time remaining until the next state change. The `Next` action defines how the traffic light transitions between states and how the time is updated. The use of records allows us to group related variables together, making our model more organized and easier to understand. We can verify properties about the traffic light system using this record-based model, such as ensuring that the traffic light cycles through the states in the correct order and that the timing of each state is correct.

The line `tl' = [tl EXCEPT !.time = @ - 1]` is a shorthand for updating the `time` field of the record `tl` while keeping the other fields unchanged. The `EXCEPT` operator allows us to specify which fields of the record we want to update, and the `@` symbol refers to the current value of the record before the update. In this case, we are decrementing the `time` field by 1 while leaving the `color` field unchanged. This is a convenient way to model the countdown behavior of the traffic light system without having to manually specify the values of all fields in the record for each transition.

Also note the use of `CASE` in the `Duration` function, which allows us to define the duration of each traffic light color in a clear and organized way. This makes our model more readable and easier to maintain, as we can easily see the duration associated with each color without having to search through the code for specific values.

## Exercise 7: Missionaries and Cannibals Problem
The Missionaries and Cannibals problem is a classic problem in which three missionaries and three cannibals need to cross a river using a boat that can carry at most two people. The boat cannot cross the river by itself, and at no point can the number of cannibals on either bank be greater than the number of missionaries on that bank (if there are missionaries present), as the cannibals would eat the missionaries. The goal is to find a sequence of moves that allows all the missionaries and cannibals to safely cross the river. 

Study the problem and the [specification provided in the TLA+ Toolbox](https://github.com/tlaplus/Examples/tree/master/specifications/MissionariesAndCannibals). In particular, analyze the specification to understand how the state of the system is represented, how the transitions between states are defined, and how the properties of the system are verified. Pay special attention to the use of:
 - Constants to represent the number of missionaries and cannibals, the capacity of the boat, and the sides of the river. Notice that these are then configured in the model parameters, which allows us to easily change the problem parameters without modifying the specification itself.
 - The use of `FiniteSets` that allow the use of operators such as `Cardinality` to count the number of missionaries and cannibals on each side of the river, which simplifies the definition of the transitions and the properties.
 - The use of functions to represent the state of the system. The function `who_is_on_bank` is used to represent which missionaries and cannibals are on each side of the river. Also notice that `[x \in D |-> exp(x)]` means that we are defining a function that maps each element `x` in the domain `D` to the value of `exp(x)`. This allows us to easily define the state of the system in a compact and expressive way.

## Exercise 8: Cabbage, Wolf, and Goat Problem
Now that you studied the Missionaries and Cannibals problem, you can apply similar modeling techniques to solve the Cabbage, Wolf, and Goat problem. Here is the problem statement:

 > A farmer needs to transport a cabbage, a wolf, and a goat across a river using a boat. The boat can only carry the farmer and one of the three items at a time. The farmer cannot leave the wolf alone with the goat, or the goat alone with the cabbage, as the wolf would eat the goat and the goat would eat the cabbage. The farmer needs to find a way to transport all three items across the river without any of them being eaten. 
 
Model this problem in TLA+ and verify that there is a sequence of moves that allows the farmer to successfully transport all three items across the river without any of them being eaten.

