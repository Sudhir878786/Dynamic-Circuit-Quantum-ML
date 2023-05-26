# IBM Quantum ML Challenge 2023
# Introduction to Dynamic Circuits and Repeat until success
According to the [Qiskit textbook,](https://learn.qiskit.org/course/ch-algorithms/quantum-circuits) 
>_"A quantum circuit is a computational routine consisting of coherent quantum operations on qubits ... It is an ordered sequence of quantum gates, measurements and resets, all of which may be conditioned on and use data from the real-time classical computation."_

You might not be familiar with the last part of the definition, but it has everything to do with dynamic circuits. Usually when we talk about quantum circuits, we're referring to _static_ quantum circuits, even if we don't explicitly say it. So what are dynamic circuits?

**Dynamic circuits** are quantum circuits that contain mid-circuit measurements where the results of those measurements are used to condition quantum gates later in the circuit. The ability to condition future quantum operations on the classical measurement results is known as classical feedforward.

Why do we need classical feedforward? An example may help: Consider the conditional reset operation, which may sound familiar if you've ever used the [`reset`](https://qiskit.org/documentation/stubs/qiskit.circuit.QuantumCircuit.reset.html) operation in Qiskit. While the reset operation is a non-unitary operation that resets the qubit to the zero state no matter the initial state, the conditional reset operation is performed on an already measured qubit to reset it to zero by applying a bit-flip X gate only if the qubit is in the one state, as determined from its measured value. The conditional reset can be a faster and less error-prone way to reset a qubit if it has already been measured.
## Hello Dynamic Circuit World
IBM Quantum hardware has already supported mid-circuit measurements and reset for some time. However, adding support for classical feedforward required a significant redesign of the control-systems and the associated software stack. Full support for dynamic circuits was introduced only last year (2022).

In Qiskit, the syntax for programming dynamic circuits has gone through several iterations, and full support has not yet been implemented. Currently, the only way to access the full capabilities of dynamic circuits is to submit programs written in OpenQASM 3. Nevertheless, in this challenge we will be working with what is currently available in Qiskit.

An earlier version of Qiskit introduced the [`c_if()`](https://qiskit.org/documentation/stubs/qiskit.circuit.Instruction.c_if.html) instruction, but this syntax will be deprecated in favor of the more flexible [`if_test()`](https://qiskit.org/documentation/stubs/qiskit.circuit.QuantumCircuit.if_test.html) method of QuantumCircuit, which is the method we'll be mostly using in this challenge.

To get you started, we're going to review a simple example where we'll use this function. We'll build a circuit that demonstrates the conditional reset operation by taking the following steps:

1. Initialize a circuit with 1 qubit and 2 classical bits. ($q_{0}$, $b_{0}$  and $b_{1}$)
2. Apply a Hadamard gate to $q_{0}$.
3. Measure that qubit and save the result in $b_{0}$.
4. Begin an `if_test` block conditioned on $b_{0}$ being equal to 1.
5. In the `if_test` block, specify the operation to do if the condition is met, in this case, flipping $q_{0}$ to 0 state.
6. Measure $q_0$ again into $b_{1}$ to check that we always get 0.
