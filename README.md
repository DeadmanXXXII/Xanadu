# Xanadu
Both Strawberry fields and Pennylane

### **Strawberry Fields CLI Overview**

Strawberry Fields is a full-stack library for designing, simulating, and optimizing quantum optical circuits. Below is a detailed list of commands, usage examples, and advanced techniques.

#### **1. Installation**

Ensure you have Strawberry Fields installed.
```bash
pip install strawberryfields
```

#### **2. Running a Simple Example**

Formulate and run a basic quantum circuit.
```python
import strawberryfields as sf
from strawberryfields import ops

# Create a 2-mode quantum circuit
prog = sf.Program(2)

with prog.context as q:
    ops.Sgate(0.54) | q[0]
    ops.Dgate(0.3 + 0.2j) | q[1]
    ops.BSgate(0.5) | (q[0], q[1])

# Run the circuit on the Fock backend
eng = sf.Engine("fock", backend_options={"cutoff_dim": 5})
result = eng.run(prog)

# Print the results
print(result.state)
```

#### **3. Advanced Circuit Design**

Creating and running a more complex quantum circuit.
```python
import strawberryfields as sf
from strawberryfields import ops

# Create a 4-mode quantum circuit
prog = sf.Program(4)

with prog.context as q:
    ops.Sgate(0.54) | q[0]
    ops.Dgate(0.3 + 0.2j) | q[1]
    ops.BSgate(0.5) | (q[0], q[1])
    ops.MZgate(0.1) | (q[2], q[3])
    ops.CXgate(0.5) | (q[1], q[3])
    ops.CZgate(0.2) | (q[0], q[2])

# Run the circuit on the Fock backend
eng = sf.Engine("fock", backend_options={"cutoff_dim": 10})
result = eng.run(prog)

# Print the results
print(result.state)
```

#### **4. Optimization with Strawberry Fields**

Using Strawberry Fields with optimization tools.
```python
import strawberryfields as sf
from strawberryfields import ops
from scipy.optimize import minimize

# Define a cost function
def cost_function(params):
    prog = sf.Program(2)
    with prog.context as q:
        ops.Sgate(params[0]) | q[0]
        ops.Dgate(params[1] + params[2]*1j) | q[1]
        ops.BSgate(params[3]) | (q[0], q[1])
    
    eng = sf.Engine("fock", backend_options={"cutoff_dim": 5})
    result = eng.run(prog)
    state = result.state
    return -abs(state.fidelity([1, 0, 0, 0]))

# Optimize the parameters
initial_params = [0.5, 0.3, 0.2, 0.5]
result = minimize(cost_function, initial_params, method='Nelder-Mead')
print(result.x)
```

#### **5. Visualization**

Visualizing quantum circuits and results.
```python
import strawberryfields as sf
from strawberryfields import ops, plotting

prog = sf.Program(2)
with prog.context as q:
    ops.Sgate(0.54) | q[0]
    ops.Dgate(0.3 + 0.2j) | q[1]
    ops.BSgate(0.5) | (q[0], q[1])

eng = sf.Engine("fock", backend_options={"cutoff_dim": 5})
result = eng.run(prog)

# Plot Wigner function
plotting.plot_wigner(result.state, mode=0)
```

### **PennyLane CLI Overview**

PennyLane is a machine learning library for quantum and hybrid quantum-classical computations. Below is a detailed list of commands, usage examples, and advanced techniques.

#### **1. Installation**

Ensure you have PennyLane installed.
```bash
pip install pennylane
```

#### **2. Running a Simple Example**

Creating and running a simple quantum node.
```python
import pennylane as qml
from pennylane import numpy as np

# Define a device
dev = qml.device("default.qubit", wires=1)

@qml.qnode(dev)
def circuit(params):
    qml.RX(params[0], wires=0)
    qml.RY(params[1], wires=0)
    return qml.expval(qml.PauliZ(0))

# Run the circuit
params = np.array([0.1, 0.2])
print(circuit(params))
```

#### **3. Optimization with PennyLane**

Using PennyLane's optimizers to minimize a cost function.
```python
import pennylane as qml
from pennylane import numpy as np

# Define a device
dev = qml.device("default.qubit", wires=1)

@qml.qnode(dev)
def circuit(params):
    qml.RX(params[0], wires=0)
    qml.RY(params[1], wires=0)
    return qml.expval(qml.PauliZ(0))

# Define a cost function
def cost(params):
    return (circuit(params) - 1)**2

# Initialize the optimizer
opt = qml.GradientDescentOptimizer(stepsize=0.1)
params = np.array([0.1, 0.2])

# Optimize the parameters
for _ in range(100):
    params = opt.step(cost, params)
print(params)
```

#### **4. Advanced Techniques**

- **Using custom observables:**
  ```python
  import pennylane as qml
  from pennylane.operation import Observable

  class CustomObservable(Observable):
      num_wires = 1

      @staticmethod
      def decomposition(wires):
          return [qml.RZ(0.3, wires=wires), qml.RY(0.2, wires=wires)]

  dev = qml.device("default.qubit", wires=1)

  @qml.qnode(dev)
  def circuit():
      qml.RX(0.1, wires=0)
      return qml.expval(CustomObservable(wires=0))

  print(circuit())
  ```

- **Creating custom devices:**
  ```python
  import pennylane as qml
  from pennylane import Device

  class CustomDevice(Device):
      name = 'Custom device'
      short_name = 'custom.dev'
      pennylane_requires = '0.14'
      version = '0.1.0'
      author = 'Your Name'

      operations = {'RX', 'RY', 'RZ'}
      observables = {'PauliX', 'PauliY', 'PauliZ'}

      def __init__(self, wires):
          super().__init__(wires, shots=None)

      def apply(self, operations, **kwargs):
          for operation in operations:
              self.apply_operation(operation)

      def apply_operation(self, operation):
          # Implement the logic to apply the operation
          pass

      def expval(self, observable, wires, par):
          # Implement the logic to measure the expectation value
          pass

  qml.device.register('custom.dev', CustomDevice)

  dev = qml.device('custom.dev', wires=1)
  ```

### **Conclusion**

This comprehensive guide covers a wide range of tasks for working with Strawberry Fields and PennyLane. From installation and basic problem formulation to advanced techniques, optimization, visualization, and integration with other tools, this guide provides a thorough resource for quantum programming with Xanadu's quantum software.
