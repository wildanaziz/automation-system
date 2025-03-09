# Automation System Task Part 1

## Wildan Aziz Hidayat - Automation System C

This notebook covers the mathematical modeling and analysis of a DC motor using Laplace Transforms and transfer functions. It also includes a simulation of the step response using Python's `control` library.

## Topics Covered

1. **Mathematical Model of DC Motor**  
   - Mechanical and electrical equations
2. **Laplace Transform on DC Motor**  
   - Conversion of mechanical and electrical equations into the Laplace domain
3. **Transfer Function Derivation**  
   - Relationship between angular velocity (Ω) and voltage (V)
4. **Simulation of Step Response**  
   - Using `matplotlib` and `control` libraries to visualize the system behavior

---

## Steps in the Notebook

### 1. Import Required Libraries
The necessary libraries are imported to facilitate symbolic computation and visualization:

```python
import sympy as sp
from sympy import init_printing, Eq, symbols, solve, simplify
from IPython.display import display
```

- `sympy` is used for symbolic computation.
- `init_printing()` is used for better equation formatting.

### 2. Define Symbolic Variables
The following symbolic variables are defined:

```python
# Time and Laplace variables
t, s = symbols('t s')

# System variables
V_s, I_s, Omega_s = symbols('V_s I_s Omega_s')
R, L, J, B, K = symbols('R L J B K')
```

### 3. Apply Laplace Transform to Equations
The DC motor's electrical and mechanical equations are transformed into the Laplace domain:

#### **Electrical Equation**

```python
Eb_s = K * Omega_s  # E_b(s) = K * Omega(s)
I_s_expr = (V_s - Eb_s) / R  # I(s) = (V(s) - E_b(s)) / R
```

#### **Mechanical Equation**

```python
mech_eq = Eq(J * s * Omega_s + B * Omega_s, K * I_s)
```

### 4. Solve for Transfer Function
We eliminate \( I(s) \) and solve for \( \Omega(s) \):

```python
mech_eq_substituted = mech_eq.subs(I_s, I_s_expr)
Omega_s_solution = solve(mech_eq_substituted, Omega_s)[0]
```

The transfer function is then computed as:

```python
G_s = simplify(Omega_s_solution / V_s)
```

### 5. Simulate Step Response
A numerical simulation is conducted using the `control` library. First, install it if necessary:

```sh
pip install control
```

Then, define system parameters and compute the step response:

```python
import numpy as np
import matplotlib.pyplot as plt
import control as ctrl

# Define numerical values for parameters
params = {R: 1.0, J: 0.01, B: 0.1, K: 0.01}
G_s_numeric = G_s.subs(params)

# Convert transfer function to numeric format
num, den = sp.fraction(G_s_numeric)
num = [float(i.evalf()) for i in sp.Poly(num, s).all_coeffs()]
den = [float(i.evalf()) for i in sp.Poly(den, s).all_coeffs()]

# Create system transfer function
system = ctrl.TransferFunction(num, den)

# Simulate step response
time, response = ctrl.step_response(system)

# Plot response
plt.figure(figsize=(8, 5))
plt.plot(time, response, label='Step Response')
plt.xlabel('Time (s)')
plt.ylabel('Omega (rad/s)')
plt.title('Step Response of the DC Motor')
plt.legend()
plt.grid()
plt.show()
```

---

## Output and Results
- The transfer function of the DC motor is computed symbolically.
- A step response is simulated to observe how the system reacts to an input voltage.
- The generated plot provides insights into the motor’s dynamic behavior.

This notebook serves as a fundamental study of DC motor control using Laplace Transforms and transfer functions.