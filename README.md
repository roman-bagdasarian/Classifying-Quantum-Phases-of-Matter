# Classiq x DuQIS FLIQ Challenge
Science track challenge for FLIQ 2025 hackathon

# Classifying Quantum Phases of Matter

A challenge hosted by **Classiq Technologies** and **DuQIS** (Duke Quantum Information Society) as part of the FLIQ Hackathon. Participants will apply **quantum machine learning** to distinguish between different phases of quantum matter using measurement data obtained in randomized bases.

## Challenge Summary

The task is to build a **Quantum Machine Learning (QML)** model capable of classifying different phases of quantum matter from measurement data.

Unlike typical datasets, your inputs are **classical shadows**: compressed representations of quantum states constructed via randomized measurements. Your model should learn to identify the phase label of a quantum state based only on this information.

## Background

### Classical Shadows

A classical shadow is a compact representation of a quantum state constructed from local randomized measurements. Suppose an experiment prepares an $n$-qubit state $\rho$. Instead of reconstructing $\rho$ directly (which is exponentially costly), we perform $T$ rounds of random single-qubit Pauli measurements.

For each round $t$, each qubit is measured in a random Pauli basis, collapsing $\rho$ into a product state:

$$
\ket{s^{(t)}} = \bigotimes_{i=1}^n \ket{s_i^{(t)}}, \quad \ket{s_i^{(t)}} \in \{ \ket{0}, \ket{1}, \ket{+}, \ket{-}, \ket{+i}, \ket{-i} \}.
$$

We then form an estimate:

$$
\sigma_T(\rho) = \frac{1}{T} \sum_{t=1}^T \sigma_1^{(t)} \otimes \dots \otimes \sigma_n^{(t)}, \quad \sigma_i^{(t)} = 3\ketbra{s_i^{(t)}}{s_i^{(t)}} - I.
$$

You are provided with $nT$ measurement results per data point. You may choose to compute reduced density matrices for specific subsystems, such as:

$$
\rho^A \approx \frac{1}{T} \sum_{t=1}^T \bigotimes_{i \in A} \sigma_i^{(t)}.
$$

For more details, see:

- Huang et al., *Predicting Many Properties of a Quantum System from Very Few Measurements* (2020), [https://arxiv.org/abs/2002.08953](arXiv:2002.08953)
- Huang et al., *Provably efficient machine learning for quantum many-body problems* (2022), [Provably efficient machine learning for quantum many-body problems](arXiv:2106.12627)

### QML Models

Participants are expected to build a parameterized quantum circuit to classify measurement data. The exact architecture — including encoding scheme, number of qubits, and circuit layers — is up to you.

Suggested reading:

- Sim et al., *Expressibility and Entangling Capability of Parameterized Quantum Circuits for Hybrid Quantum‐Classical Algorithms* (2019), [https://arxiv.org/abs/1905.10876](arXiv:1905.10876)

## Dataset Format

Each data point consists of:

- A list of $T$ measurement outcomes, each a list of $n$ elements $s_j^{(i,t)} \in \{\text{"0"}, \text{"1"}, \text{"+"}, \text{"-"}, \text{"+i"}, \text{"-i"}\}$
- A label $y^{(i)}$ such as `"Z2"` indicating the phase

Example:

$$
x^{(i)} = \left[
\left[\text{"-"}, \text{"+i"}, \dots, \text{"0"}\right],\ 
\left[\text{"0"}, \text{"-i"}, \dots, \text{"1"}\right],\ 
\dots
\right], \quad y^{(i)} = \text{"Z2"}
$$

It is up to you to choose which reduced density matrices to extract as features (e.g., 1-qubit, 2-qubit). Avoid reconstructing the full $\rho$, which is a $2^n \times 2^n$ object.

## Your Task

Build a quantum circuit that:

- Takes as input reduced density matrices constructed from the measurement data
- Outputs a prediction of the quantum phase
- Is optimized for both accuracy and efficiency

## Grading Criteria

Each submission will be scored using the function:

$$
f(A, P, D, W) = \alpha A - \beta P - \gamma D - \delta W
$$

Where:

- $A$: accuracy on the test set  
- $P$: number of trainable parameters  
- $D$: circuit depth (with gate set of 1-qubit unitaries and CNOTs)  
- $W$: number of qubits (circuit width)  
- $\alpha, \beta, \gamma, \delta$: fixed positive constants

Higher values of $f$ indicate better solutions.

## Tips

- Reduced density matrices of small subsystems may already carry enough information about the phase.
- Consider different encoding strategies such as angle or amplitude encoding. You can find the pre-defined Classiq method for angle encoding [here](https://docs.classiq.io/latest/explore/functions/qmod_library_reference/classiq_open_library/variational_data_encoding/variational_data_encoding/#encode-on-bloch)

- You may apply classical preprocessing, but the model must ultimately be quantum.
