# Classiq-x-DuQIS-FLIQ-Challenge
Science track challenge for FLIQ 2025 hackathon

\documentclass[11pt]{article}

\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{amsmath, amssymb, mathtools}
\usepackage{hyperref}
\usepackage{enumitem}
\usepackage{graphicx}
\usepackage{braket}

\title{Classifying Quantum Phases of Matter}
\author{Classiq Technologies \& DuQIS}
\date{May 2025}

\begin{document}

\maketitle

\section*{Challenge Summary}
The task is to build a Quantum Machine Learning (QML) model capable of classifying different phases of quantum matter using measurement data obtained in randomized bases.

Unlike standard datasets, inputs are given as \textit{classical shadows}: efficient classical representations of quantum states obtained via randomized local measurements. Your model should infer the phase label of a given quantum state based only on this information.

\section*{Background}

\subsection*{Classical Shadows}

A classical shadow is a compact representation of a quantum state constructed from local randomized measurements. Suppose an experiment prepares an \( n \)-qubit state \( \rho \). Rather than reconstructing \( \rho \) in full (which would be exponentially costly), we perform \( T \) rounds of random single-qubit Pauli measurements.

For each round \( t \), each qubit is measured in a random Pauli basis, collapsing \( \rho \) to a product state:
\[
\ket{s^{(t)}} = \bigotimes_{i=1}^n \ket{s_i^{(t)}}, \quad \ket{s_i^{(t)}} \in \{\ket{0}, \ket{1}, \ket{+}, \ket{-}, \ket{+i}, \ket{-i}\}.
\]

From these, we form an estimate:
\[
\sigma_T(\rho) = \frac{1}{T} \sum_{t=1}^T \sigma_1^{(t)} \otimes \dots \otimes \sigma_n^{(t)}, \quad \sigma_i^{(t)} = 3\ketbra{s_i^{(t)}}{s_i^{(t)}} - I.
\]

These \( nT \) measurement outcomes are what you will use to extract features and train your classifier. You may also compute reduced density matrices for specific subsystems:
\[
\rho^A \approx \frac{1}{T} \sum_{t=1}^T \bigotimes_{i \in A} \sigma_i^{(t)}.
\]

For more information, see Refs.~\cite{huang2020predicting, huang2022provably}.

\subsection*{QML Models}

Participants are expected to design a parameterized quantum circuit (PQC) to classify the input data. The architecture is up to you: choices include ansatz type, encoding strategy, and number of qubits.

Suggested references:
\begin{itemize}
    \item X. Huang et al., ``Predicting many properties of a quantum system from very few measurements,'' \textit{Nature Physics}, 2020.
    \item X. Huang et al., ``Provably efficient machine learning for quantum many-body problems,'' \textit{Nature}, 2022.
    \item S. Sim et al., ``Expressibility and entangling capability of parameterized quantum circuits for hybrid quantum-classical algorithms,'' 2019.
    \item Y. Cong et al., ``Quantum convolutional neural networks,'' 2019.
\end{itemize}

\section*{Dataset Format}

Each data point consists of:
\begin{itemize}
    \item A list of \( T \) measurement outcomes, each a list of \( n \) elements \( s_j^{(i,t)} \in \{\text{\textit{"0"}}, \text{\textit{"1"}}, \text{\textit{"+"}}, \text{\textit{"-"}}, \text{\textit{"+i"}}, \text{\textit{"-i"}}\} \)
    \item A label such as \texttt{"Z2"} indicating the phase
\end{itemize}

Example:
\begin{align*}
x^{(i)} &= [
[\text{\textit{"-"}}, \text{\textit{"+i"}}, \dots, \text{\textit{"0"}}],\ 
[\text{\textit{"0"}}, \text{\textit{"-i"}}, \dots, \text{\textit{"1"}}],\ 
\dots ] \\
y^{(i)} &= \texttt{"Z2"}
\end{align*}

It is your responsibility to determine which reduced density matrices to extract as features (e.g., single-site, two-site). Avoid reconstructing the full \( \rho \), which is a \( 2^n \times 2^n \) object.

\section*{Your Task}

Design a quantum circuit that:
\begin{itemize}
    \item Takes as input reduced density matrices derived from the measurement outcomes
    \item Maps these inputs to a prediction of the phase label
    \item Is optimized for both performance and efficiency
\end{itemize}

\section*{Grading Criteria}

Each submission will be evaluated using the scoring function:
\[
f(A, P, D, W) = \alpha A - \beta P - \gamma D - \delta W
\]

Where:
\begin{itemize}
    \item \( A \) is the classification accuracy on a held-out test set
    \item \( P \) is the number of trainable parameters
    \item \( D \) is the circuit depth (counting only 1-qubit unitaries and CNOTs)
    \item \( W \) is the number of qubits (circuit width)
    \item \( \alpha, \beta, \gamma, \delta \) are fixed positive constants
\end{itemize}

Higher scores correspond to better models.

\section*{Hints and Tips}

\begin{itemize}
    \item The structure of the quantum state may be captured using only a small subsystem.
    \item Different encoding strategies (e.g., angle encoding, amplitude encoding) may be more efficient depending on feature dimension.
    \item You may perform classical preprocessing, but the model itself must be quantum.
\end{itemize}

\end{document}
