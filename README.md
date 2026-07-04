# Integrated EV Telemetry Dashboard & Advanced Driver Assistance System (ADAS)

An end-to-end embedded systems capstone project that bridges real-time firmware execution on an ARM Cortex-M architecture with an interactive visual telemetry software dashboard. This repository contains the modular STM32 firmware logic and the multithreaded Python GUI engine.

## 🚀 System Architecture overview
The project operates as a synchronized pipeline:
1. **Data Collection:** Continuous polling of analog control inputs (Accelerator/Brake) and a physical array of three ultrasonic sensors (Front, Left, Right).
2. **Edge Processing:** An STM32F103 Blue Pill micro-controller processes raw inputs, calculates active EV metrics, and handles hazard boundary checks.
3. **Telemetry Pipeline:** Packed telemetry data streams line-by-line serially out of the USART1 peripheral at a non-blocking 115200 baud rate.
4. **Visual Frontend:** A Python-based GUI canvas handles real-time string parsing on an isolated thread to dynamically redraw instrument gauges.

---

## 🛠️ Hardware Peripheral Configuration (STM32)
* **ADC (Analog-to-Digital Converter):** Configured for regular multi-channel voltage tracking of the workspace potentiometer inputs (`ACC_P` and `BRK_P`).
* **TIM1 (Timer 1):** Configured to generate a deterministic 10ms interrupt loop dedicated to executing vehicle state and battery physics math equations.
* **TIM2 (Timer 2):** Operated as a high-precision microsecond counter to calculate acoustic echo flight durations for the ultrasonic sensor modules.
* **GPIO Map:** Handles digital Trigger/Echo pulsing for spatial monitoring and commands the structural safety buzzer mapped to pin `PC13`.

---

## 💻 Firmware Modules (`Core/Src/`)
The C-backend firmware utilizes a highly decoupled, modular driver pattern:
* `ev_control.c`: Manages vehicle dynamic calculations including live motor speed curves, negative regenerative braking torque shifts, and battery State of Charge (SOC) discharge algorithms.
* `adas.c`: Monitors Time-to-Collision (TTC) thresholds across central and side fields of view simultaneously.
* `fault.c` / `uart_shell.c`: Actively intercepts raw sensor boundary violations to execute autonomous drive overrides and parses terminal configuration strings.

---

## 🚗 Core Features & ADAS Logic Demonstrated
* **Real-time Acceleration & Drain:** Smoothly scales virtual motor speed indicators and systematically drains battery SOC dynamically as the throttle input rises.
* **Regenerative Braking:** Shifting down the throttle and scaling up the brake forces torque metrics into negative vectors, simulating kinetic harvesting back into the power system.
* **Blind-Spot Monitoring:** Automated lateral warning indicator activation flags pop up dynamically on the canvas if proximity sensors detect obstacle fields on the left or right axes.
* **Autonomous Emergency Braking (AEB):** If the front ultrasonic sensor evaluates a critical distance violation, a safety override instantly forces motor torque to absolute zero, triggering an active hardware buzzer alert and a high-contrast red/black flashing hazard notification canvas.

---

## 📦 Tech Stack & Tools Used
* **Firmware IDE:** STM32CubeIDE
* **Emulation Environment:** PICSimLab (STM32F103C8T6 Blue Pill Board & Spare Parts Workspace)
* **Serial Interface:** CuteCom / Virtual COM Bridge Port Mapper
* **Frontend GUI Engine:** Python (Tkinter / Custom Graphic Libraries)

---

## 🔧 How to Run the Project
1. **Flash Firmware:** Compile the STM32 project source code using STM32CubeIDE and flash the generated `.hex`/`.bin` binary into the PICSimLab simulator workspace.
2. **Setup Ports:** Open your terminal utility (e.g., CuteCom) or launch a virtual COM port bridge to map your emulator serial line layout (e.g., `COM4`).
3. **Launch Frontend:** Initialize the Python UI engine terminal layout:
   ```bash
   python main_dashboard.py
