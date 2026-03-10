# Week 03: TLA+ Specifications (Consolidation)
In this week, we will consolidate our understanding of TLA+ specifications by working on several activities that involve writing and analyzing TLA+ specifications. We will cover the following topics:
1. **Review of TLA+ Syntax and Semantics**: We will revisit the basic syntax and semantics of TLA+ to ensure that everyone is comfortable with writing specifications.
2. **Type Correctness**: We will discuss how to ensure that our TLA+ specifications are type correct and how to use the TLC model checker to verify this.
3. **Records and Tuples**: We will learn how to use records and tuples in TLA+ to structure our specifications more effectively.
4. **Functions and Sets**: We will explore how to define and use functions and explore more set operations in TLA+ specifications.

Some of the activities will involve going through videos from Leslie Lamport's TLA+ Video Course, which provides a practical introduction to TLA+ and its applications in modeling and verifying systems (we call these activities, instead of exercises). The videos are available on Leslie Lamport's website, and we will provide links to the relevant videos for each activity. If you are in the lab, please use headphones to watch the videos to avoid disturbing others. Of course, you are encouraged to discuss the problems and your approach with your peers, but make sure to write your own TLA+ specifications.

It is unlikely that you will be able to complete all the exercises and activities in this session. As indicated at the end of Lecture 3, the remainder activities are recommended homework.

## Exercise 1: Two-Phase Handshake Protocol (First Specification)
Review the TLA+ specification for the two-phase handshake protocol that we discussed in Lecture 3 (you can use Chapter 3 of the Specifying Systems book if you prefer). Make sure you understand the specification and how it models the behavior of the protocol. Then:

1. Use the TLA+ Toolbox to write the original specification discussed in the lecture (First Specification).
2. Use the TLC model checker to verify that the specification is correct. Analyze any counterexamples provided by TLC if the specification does not satisfy the properties.
3. Modify the specification to ensure that the initial value of `val` is -1. Ideally, add a new constant named `InitialValue`to represent this initial value. Note that you will need to adjust a few things in the initial specification.
4. Define an invariant that you check with TLC to ensure that a receive action will occur.

## Exercise 2: Two-Phase Handshake Protocol (Second Specification)
Now, we will use the second specification for the two-phase handshake protocol discussed in the lecture. This specification uses records and an operator `Send(d)`. Follow the same steps as in Exercise 1 to write the specification, verify it with TLC, and modify it to include an initial value for `val` (initially set to -1). Additionally, define an invariant that ensures that a receive action will occur and verify it with TLC. However, for this last step, define an operator `Receive(d)` that models that the receive action received value `d`, and use this operator in your invariant to check that value `d` will eventually be received (we assume that `d \in Data`).

## Activity 3: Die Hard
In this exercise, we will model the classic "Die Hard" problem using TLA+. The problem is as follows: You have two jugs, one with a capacity of 3 liters and another with a capacity of 5 liters. You need to measure exactly 4 liters of water using these jugs. You can fill the jugs, empty them, or pour water from one jug to the other until one of the jugs is either full or empty. This is the problem discussed in Leslie Lamport's TLA+ Video Course ((Video 4)[https://lamport.azurewebsites.net/video/video4.html]). Your task is to go through the video and follow along to model the problem in TLA+. You can find the TLA+ specification for this problem in the video description. After modeling the problem, use the TLC model checker to verify that your specification is correct and that it can find a solution to the problem.

The (video script)[https://lamport.azurewebsites.net/video/videos.html] is also available for reference. If you are in the lab, please use headphones to watch the video to avoid disturbing others. Of course, you are encouraged to discuss the problem and your approach with your peers, but make sure to write your own TLA+ specification.

## Activity 4: Transaction Commit and Two-Phase Commit
In this exercise, you will go through two videos from Leslie Lamport's TLA+ Video Course that discuss the transaction commit problem and the two-phase commit protocol. The videos are (Video 5)[https://lamport.azurewebsites.net/video/video5.html] and (Video 6)[https://lamport.azurewebsites.net/video/video6.html]. Your task is to watch these videos, follow along with the TLA+ specifications provided in the video descriptions, and use the TLC model checker to verify that your specifications are correct. 

The (video script)[https://lamport.azurewebsites.net/video/videos.html] is also available for reference. If you are in the lab, please use headphones to watch the video to avoid disturbing others. Of course, you are encouraged to discuss the problem and your approach with your peers, but make sure to write your own TLA+ specification.