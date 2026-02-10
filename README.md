# Automated PID Controller Tuning Using Genetic Algorithms
## Hardware-in-the-Loop Systems Simulation

### Project Overview
This simulation demonstrates automatic tuning of PID (Proportional-Integral-Derivative) controllers using Genetic Algorithms (GA) in a Hardware-in-the-Loop (HIL) framework for electronic device control.

---

## Features

### 1. **Plant Models**
Three different electronic system models are available:
- **Second-Order System**: Generic transfer function G(s) = 1/(s² + 2s + 1)
- **DC Motor**: Transfer function G(s) = 10/(s² + 5s + 4)
- **Thermal System**: First-order system G(s) = 5/(10s + 1)

### 2. **PID Controller**
- Proportional (P), Integral (I), and Derivative (D) control
- Anti-windup protection
- Output saturation limits
- Configurable time step

### 3. **Genetic Algorithm**
- Population-based optimization
- Tournament selection
- Uniform crossover
- Gaussian mutation
- Elitism strategy
- Multi-objective fitness function

### 4. **Fitness Function**
The GA optimizes based on:
- **ISE** (Integral of Squared Error): Minimizes tracking error
- **Overshoot**: Prevents output exceeding setpoint
- **Settling Time**: Fast stabilization to setpoint
- **Rise Time**: Quick initial response

---

## Results from Simulation

### Optimal PID Parameters Found:
```
Kp = 20.0000 (Proportional gain)
Ki = 0.0000  (Integral gain)
Kd = 4.3231  (Derivative gain)
```

### Performance Metrics:
- **Overshoot**: 0.00% (excellent - no overshoot)
- **Steady-State Error**: 0.0714 (acceptable)
- **Optimization Time**: ~114 seconds (30 generations)

### Key Observations:

1. **System Response (Top-Left Plot)**:
   - Smooth tracking of setpoint changes
   - No overshoot on step response
   - Quick settling with minimal oscillations
   - Handles setpoint change at t=2.5s effectively

2. **PID Control Output (Top-Right Plot)**:
   - Initial spike to drive system quickly
   - Smooth control action during steady state
   - Appropriate response to setpoint changes

3. **Tracking Error (Bottom-Left Plot)**:
   - Rapid error reduction
   - Small steady-state error
   - Quick recovery after disturbance

4. **GA Convergence (Bottom-Right Plot)**:
   - Fast initial convergence (first 15 generations)
   - Population diversity maintained
   - Converged to near-optimal solution

---

## How to Use

### Basic Usage:
```python
python pid_ga_tuning.py
```

### Customization Options:

#### 1. Change Plant Type:
```python
plant_type = 'dc_motor'    # Options: 'second_order', 'dc_motor', 'thermal'
```

#### 2. Adjust GA Parameters:
```python
ga = GeneticAlgorithm(
    population_size=50,      # Number of individuals
    generations=30,          # Optimization iterations
    mutation_rate=0.15,      # Probability of mutation
    crossover_rate=0.8,      # Probability of crossover
    elitism_rate=0.1         # Percentage of best kept
)
```

#### 3. Modify PID Bounds:
```python
self.bounds = np.array([
    [0.1, 20.0],   # Kp range
    [0.0, 10.0],   # Ki range
    [0.0, 5.0]     # Kd range
])
```

#### 4. Adjust Fitness Weights:
```python
fitness = (1.0 * ise + 
          0.5 * overshoot + 
          2.0 * settling_time + 
          1.0 * rise_time)
```

---

## Technical Details

### HIL Simulation Architecture:
```
┌─────────────────┐
│   Setpoint      │
└────────┬────────┘
         │
    ┌────▼────┐
    │   PID   │◄─── Genetic Algorithm
    │Controller│     (Parameter Optimization)
    └────┬────┘
         │ Control Signal
    ┌────▼────┐
    │  Plant  │
    │  Model  │
    └────┬────┘
         │ Output
         └─────► Feedback
```

### Genetic Algorithm Flow:
1. **Initialize** random population of PID parameters
2. **Evaluate** fitness by simulating each parameter set
3. **Select** best individuals (tournament selection)
4. **Crossover** pairs of parents to create offspring
5. **Mutate** offspring with small random changes
6. **Replace** population with new generation
7. **Repeat** until convergence or max generations

---

## Dependencies

```bash
pip install numpy scipy matplotlib
```

Required Python packages:
- `numpy`: Numerical computations
- `scipy`: Signal processing and control systems
- `matplotlib`: Visualization

---

## Applications

This simulation framework can be adapted for:

### Electronic Devices:
- Temperature control in ovens/furnaces
- Voltage regulation in power supplies
- Current control in battery chargers
- Speed control in DC/AC motors
- Position control in servo systems

### Industrial Control:
- Process control (chemical plants)
- HVAC systems
- Robotics and automation
- Manufacturing equipment

### Embedded Systems:
- Microcontroller-based control
- Real-time systems
- Arduino/Raspberry Pi projects

---

## Extension Ideas

1. **Add More Plant Models**:
   - Add custom transfer functions
   - Include nonlinear systems
   - Model real hardware

2. **Enhanced GA Features**:
   - Adaptive mutation rates
   - Multi-objective optimization (Pareto fronts)
   - Island model (parallel populations)

3. **Real Hardware Integration**:
   - Serial communication with microcontroller
   - Real-time data acquisition
   - Hardware-based plant

4. **Advanced Control**:
   - Fuzzy PID
   - Adaptive PID
   - Model Predictive Control (MPC)

5. **Visualization**:
   - Real-time plotting
   - 3D fitness landscape
   - Parameter sensitivity analysis

---

## Advantages of GA-based Tuning

### vs. Manual Tuning:
✓ Automatic - no trial and error
✓ Finds global optimum
✓ Handles complex systems
✓ Multi-objective optimization

### vs. Ziegler-Nichols:
✓ Better performance
✓ No system oscillation required
✓ Customizable objectives
✓ Works with any plant model

### vs. Analytical Methods:
✓ No mathematical model needed
✓ Handles nonlinearities
✓ Real-world constraints included
✓ Practical implementation

---

## Performance Tips

1. **Increase Population Size** for better exploration (50-100)
2. **More Generations** for complex systems (50-100)
3. **Adjust Fitness Weights** based on priority
4. **Tighten Bounds** if parameters are known roughly
5. **Use Parallel Evaluation** for faster computation

---

## Troubleshooting

**Problem**: GA not converging
- Increase population size
- Reduce mutation rate
- Widen parameter bounds

**Problem**: System unstable
- Check PID output limits
- Verify plant model
- Reduce proportional gain bounds

**Problem**: Slow optimization
- Reduce population size
- Decrease simulation time
- Use fewer generations

---

## License
Educational and research use. Modify as needed for your projects.

## Author
Created for Electronic Devices course project
Hardware-in-the-Loop Systems
