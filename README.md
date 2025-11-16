# ⚛️ The Deutsch Quantum Algorithm

The Deutsch algorithm is one of the **first and simplest examples** of a quantum algorithm that demonstrates an **exponential speedup** over its classical counterpart for a specific problem. It was proposed by **David Deutsch** in 1985.

---

## ❓ The Problem

The algorithm addresses a query problem for a single-bit function.

### Classical Problem Setup
We are given a black-box function $f$ that takes one binary input and returns one binary output:

$$f: \{0, 1\} \rightarrow \{0, 1\}$$

There are only **four possible functions** $f(x)$:

1.  **Constant:** $f(0) = 0$ and $f(1) = 0$, OR $f(0) = 1$ and $f(1) = 1$.
2.  **Balanced:** $f(0) = 0$ and $f(1) = 1$, OR $f(0) = 1$ and $f(1) = 0$.

The goal is to determine if $f$ is **Constant** or **Balanced**.

### Classical Solution
To determine the type of function classically, you must evaluate the function for both possible inputs, $x=0$ and $x=1$. This requires **two queries** to the black-box function $f$.

$$\text{Classical Queries} = 2$$

### Quantum Solution
The Deutsch algorithm solves this problem with only **one quantum query** to the function, achieving a speedup.

$$\text{Quantum Queries} = 1$$

---

## 📐 Quantum Implementation: The Oracle

The black-box function $f$ is implemented as a **quantum oracle** (or unitary operator) $U_f$. This operator acts on two qubits, $|x\rangle$ (the input) and $|y\rangle$ (the auxiliary/output qubit), transforming them as follows:

$$U_f |x\rangle |y\rangle = |x\rangle |y \oplus f(x)\rangle$$

where $\oplus$ is the addition modulo 2 (XOR operation).

To utilize the **phase kickback** technique central to the Deutsch algorithm, the auxiliary qubit $|y\rangle$ is initialized in the state $|-\rangle$.

---

## ✨ The Algorithm (Circuit Steps)

The Deutsch algorithm uses a two-qubit quantum register, initially set to $|01\rangle$:

$$|\psi_0\rangle = |0\rangle |1\rangle$$

### Step 1: Initial Hadamard Transform
Apply the **Hadamard gate** ($H$) to both qubits. The Hadamard gate creates an equal superposition of all computational basis states.

$$|\psi_1\rangle = (H \otimes H) |\psi_0\rangle = (H |0\rangle) \otimes (H |1\rangle)$$

Recall the Hadamard transformations:
$$H |0\rangle = \frac{1}{\sqrt{2}} (|0\rangle + |1\rangle)$$
$$H |1\rangle = \frac{1}{\sqrt{2}} (|0\rangle - |1\rangle) = |-\rangle$$

Substituting these gives the state:
$$|\psi_1\rangle = \frac{1}{\sqrt{2}} (|0\rangle + |1\rangle) \otimes \frac{1}{\sqrt{2}} (|0\rangle - |1\rangle)$$
$$|\psi_1\rangle = \left[ \frac{1}{\sqrt{2}} \sum_{x \in \{0, 1\}} |x\rangle \right] \otimes |-\rangle$$

### Step 2: The Quantum Oracle Query
Apply the unitary operator $U_f$ to the state $|\psi_1\rangle$. This is the **single query** to the function $f$.

$$|\psi_2\rangle = U_f |\psi_1\rangle = U_f \left[ \frac{1}{\sqrt{2}} \sum_{x \in \{0, 1\}} |x\rangle \right] \otimes |-\rangle$$

Due to the property of the $U_f$ operator when the second qubit is in $|-\rangle$ (the phase kickback effect):

$$U_f |x\rangle |-\rangle = (-1)^{f(x)} |x\rangle |-\rangle$$

The state after the oracle becomes:

$$|\psi_2\rangle = \left[ \frac{1}{\sqrt{2}} \sum_{x \in \{0, 1\}} (-1)^{f(x)} |x\rangle \right] \otimes |-\rangle$$

**Key Insight:** The value of $f(x)$ has now been encoded into the **phase** of the input qubit's state. The auxiliary qubit remains in the $|-\rangle$ state and can be ignored from here on.

### Step 3: Final Hadamard Transform
Apply a final Hadamard gate only to the **first qubit** (the input qubit).

$$|\psi_3\rangle = (H \otimes I) |\psi_2\rangle = H \left[ \frac{1}{\sqrt{2}} \left( (-1)^{f(0)} |0\rangle + (-1)^{f(1)} |1\rangle \right) \right] \otimes |-\rangle$$

Applying $H$ to the first qubit's components: $H |0\rangle = \frac{1}{\sqrt{2}} (|0\rangle + |1\rangle)$ and $H |1\rangle = \frac{1}{\sqrt{2}} (|0\rangle - |1\rangle)$.

Substituting and simplifying:
$$|\psi_3\rangle = \frac{1}{2} \left[ (-1)^{f(0)} (|0\rangle + |1\rangle) + (-1)^{f(1)} (|0\rangle - |1\rangle) \right] \otimes |-\rangle$$

Group terms by $|0\rangle$ and $|1\rangle$:
$$|\psi_3\rangle = \frac{1}{2} \left[ \left( (-1)^{f(0)} + (-1)^{f(1)} \right) |0\rangle + \left( (-1)^{f(0)} - (-1)^{f(1)} \right) |1\rangle \right] \otimes |-\rangle$$

---

## 🔍 The Measurement and Result

The final step is to **measure the first qubit** ($|x\rangle$) to determine its final state.

The coefficient of the $|0\rangle$ term, which determines the probability of measuring **0**, is:
$$C_0 = \frac{1}{2} \left( (-1)^{f(0)} + (-1)^{f(1)} \right)$$

### Case 1: Constant Function ($f(0) = f(1)$)

If $f$ is **Constant**, then $f(0) = f(1)$ (either both are 0 or both are 1).

* If $f(0)=f(1)=0$, then $C_0 = \frac{1}{2} (1 + 1) = 1$.
* If $f(0)=f(1)=1$, then $C_0 = \frac{1}{2} (-1 + (-1)) = -1$.

In both constant sub-cases, the probability of measuring **0** is $P(0) = |C_0|^2 = 1^2 = \mathbf{1}$.

* **Conclusion:** If you measure **0** in the first qubit, the function **$f$ is Constant**.

### Case 2: Balanced Function ($f(0) \neq f(1)$)

If $f$ is **Balanced**, then $f(0) \neq f(1)$ (one is 0, the other is 1).

* If $f(0)=0$ and $f(1)=1$, then $C_0 = \frac{1}{2} (1 + (-1)) = 0$.
* If $f(0)=1$ and $f(1)=0$, then $C_0 = \frac{1}{2} (-1 + 1) = 0$.

In both balanced sub-cases, the probability of measuring **0** is $P(0) = |C_0|^2 = \mathbf{0}$. The probability of measuring **1** is $P(1) = 1 - P(0) = \mathbf{1}$.

* **Conclusion:** If you measure **1** in the first qubit, the function **$f$ is Balanced**.

### Summary of Result

| Measured Output of First Qubit | Function Type | Classical Queries | Quantum Queries |
| :---: | :---: | :---: | :---: |
| **0** | **Constant** | 2 | **1** |
| **1** | **Balanced** | 2 | **1** |

The Deutsch algorithm determines the property of the function (Constant or Balanced) with a **single evaluation** of the quantum oracle, a definitive speedup over the two evaluations required classically.
