# **Level 1 Roadmap: AI-Tuned Position Control**

**Goal:** Create a system where a DC motor automatically learns how to rotate to a specific angle (e.g., 90°) and stop precisely, without overshooting or vibrating, using a Genetic Algorithm.  
**Hardware Rig:** "The Servo Setup"

* **Actuator:** Brushed DC Motor \+ L298N Driver.  
* **Sensor:** Potentiometer (mechanically linked to the motor shaft).  
* **Controller:** Arduino or ESP32.

## **🛠 Phase 1: The Hardware Rig (Days 1-2)**

**Objective:** Build a physical setup that doesn't break itself.

1. **Mechanical Coupling:**  
   * Connect the motor shaft to the potentiometer knob.  
   * **CRITICAL:** Ensure the motor cannot rotate past the potentiometer's physical limits (\~270°). If it does, you will snap the pot.  
   * *Tip:* Use a gearbox motor (low RPM, high torque) to make this easier.  
2. **Wiring:**  
   * **L298N:** ENA to PWM pin, IN1/IN2 to Digital Pins.  
   * **Pot:** VCC to 5V/3.3V, GND to GND, Wiper to Analog Pin (A0).  
   * **Power:** External battery for motor. **Common Ground** with Arduino.  
3. **Sanity Check Code:**  
   * Write a script that reads A0 and prints it.  
   * Manually turn the motor. Does A0 go up/down smoothly?  
   * Apply PWM 50 to the motor. Does it move? If not, increase power until it barely moves (this is your "Deadzone" value).

## **💾 Phase 2: The Firmware (The "Slave") (Day 3\)**

**Objective:** Create code that waits for orders, runs a test, and sends back data.

1. **Serial Protocol:**  
   * **Input:** START:Kp,Ki,Kd,Setpoint (e.g., START:2.0,0.5,0.1,512)  
   * **Output:** Stream of Time(ms),Position(0-1023)  
2. **Safety Limits (Software Endstops):**  
   * In loop(), if analogRead(A0) \< 10 or \> 1000, force motor STOP (PWM 0). This prevents breaking the pot during AI testing.  
3. **The Test Routine:**  
   * On START command:  
     1. Enable PID myPID.SetTunings(...).  
     2. Record startTime.  
     3. Run for 1.5 seconds.  
     4. Stream data every 20ms.  
     5. Stop Motor. Send DONE.

*(Use the potentiometer\_firmware.ino provided earlier as your base).*

## **🐍 Phase 3: The Python Interface (Day 4\)**

**Objective:** Get Python to talk to Arduino reliably.

1. **Connection Test:**  
   * Use pyserial to send START:0,0,0,512.  
   * Verify Arduino parses it and tries to run (even if motor doesn't move because P=0).  
2. **Data Graphing:**  
   * Create a script that sends valid PID values (manually guessed, e.g., P=1, I=0, D=0) and plots the returned curve using matplotlib.  
   * **Visual Check:** Does the graph look like a step response? (Starts at X, goes to Y).

## **🧠 Phase 4: The AI Core (Day 5\)**

**Objective:** Replace the Simulation with Real Hardware.

1. **Adapt the Script:**  
   * Take ai\_pid\_simulation.py.  
   * Replace class SimulatedMotor with class RealMotor.  
   * In run\_step\_response, instead of calculating physics, use serial.write(...) and serial.read(...).  
2. **Cost Function Tuning (The "Teacher"):**  
   * **Overshoot:** Weight \= HIGH (10.0). We hate overshoot because it might hit the physical stops.  
   * **Steady State Error:** Weight \= MEDIUM (2.0). It needs to be accurate.  
   * **Settling Time:** Weight \= LOW (1.0). Speed is secondary to safety for Level 1\.

## **🚀 Phase 5: The "First Light" Run**

**Objective:** Watch the AI learn.

1. Start the Python script.  
2. **Gen 1:** The motor will spasm, hum, or do nothing. This is normal.  
3. **Gen 3:** Some individuals will move towards the target.  
4. **Gen 10:** The motor should snap to the target angle and hold it stiffly.

## **🔮 Looking Ahead (Context)**

Once this works:

* **Level 2:** We swap the Potentiometer for an Encoder and do **Velocity Control** (Drone prop speed).  
* **Level 3:** We combine them (Position Control via Velocity limits).  
* **Level 4:** We attach this to a chaotic system (Inverted Pendulum) where "Setpoint" is constantly changing based on the tilt angle.