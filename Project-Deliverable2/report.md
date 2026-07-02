# Flora Farms Greenhouse Monitoring System: Prototyping Phase

### **Course:** ICS 4111: Embedded Systems & Internet of Things  
### **Deliverable 2:** Physical and Simulated Device Prototyping  
### **Group Name:** Group 6 (Sunflowers)  
### **Team Members:**
* Kian Muchemi
* Nicole Cheruiyot
* Timon Kisera
* Bridget Muturi
* Trina Kinyua
* Nyambura Wanjohi
* Albert Ngotho

---

## 1. Project Objective and Strategic Prototype Distribution

The core objective of Deliverable 2 is to realize the abstract hardware schematics designed in Deliverable 1 into working local sensory models. In order to optimize deployment and development times within our strict group timeline, our team leveraged a split implementation scheme combining **Wokwi virtual execution blocks** alongside **physical component integration** inside the university's engineering facility.

To satisfy the task's criteria for architectural interchangeability, our 4 prototype layouts are systematically divided as follows:

| Architecture ID | Core Hardware Component Topology | Prototyping Medium | Implementation Status |
| :--- | :--- | :--- | :--- |
| **Architecture (a)** | 1 × ESP32 + 1 × DHT22 + 1 × MQ-5 + 1 × I2C OLED Display | **Both** Physical & Simulated | Fully Functional / Verified |
| **Architecture (b)** | 1 × ESP32 (MQ-5) wired directly to 1 × ESP32 (DHT22) via UART/GPIO | **Physical Hardware Only** | Active Build Phase / In Progress |
| **Architecture (c)** | 1 × ESP32 (DHT22) $\rightarrow$ 1 × 5V Relay $\rightarrow$ Secondary ESP32 (MQ-5) | **Simulated Software Only** | Fully Functional / Verified |

---

## 2. Simulated Prototypes (Wokwi Frameworks)

### 2.1 Architecture (a): Unified Sensory Acquisition and Local Screen Interfacing
* **Wokwi Simulation URL:** [https://wokwi.com/projects/468361200859736065](https://wokwi.com/projects/468361200859736065)
* **Functional Mechanics:** This setup consolidates localized micro-climate tracking and safety validation into a unified compute unit. The ESP32 utilizes a single-wire digital interface to extract air temperature and relative humidity metrics from the DHT22. Simultaneously, it maps analog gas concentrations via its internal ADC from the MQ-5 sensor lines. These values are buffered, formatted as string matrices, and systematically rendered over an I2C communication bus to map visual readouts directly onto an SSD1306 128x64 OLED screen block.
* **Verified Simulation Output:**
  * **Temperature Data Stream:** 24.0°C *(Optimally aligned to standard sunflower growth curves)*.
  * **Relative Humidity Status:** 40.0%
  * **Analogue Gas Intensity Reading:** 210 units *(Confirmed safe ambient structural baseline limit)*.

---

### 2.2 Architecture (c): Decoupled Multi-MCU Relayed Control Interfacing
* **Wokwi Simulation URL:** [https://wokwi.com/projects/468354039508272129](https://wokwi.com/projects/468354039508272129)
* **Functional Mechanics:** This architecture models data isolation loops designed to offload processing tasks. The primary ESP32 dedicates its cycle exclusively to parsing single-variable metrics from the DHT22 climate sensor. If ambient factors breach safe operational limits, the primary MCU sets an isolated output pin high. This energizes the isolation coil of a 5V Low-Level Trigger Relay Module. The mechanical closure of the relay contacts switches an active input pull-up pin on a secondary, separated ESP32 safety core. This secondary MCU is tasked with managing high-power MQ-5 parsing routines and driving emergency exhaust ventilation relays.
* **Verified Simulation Output:**
  * **Active Primary Sensor Capture:** Air metrics tracked continuously.
  * **Relay Actuation State:** The relay triggers successfully when threshold exceptions are simulated, altering logic levels across the isolated processing loops.

---

## 3. Physical Prototypes

### 3.1 Architecture (a) Bench-Scale Physical Assembly
* **Prototyping Environment:** Strathmore School of Computing and Engineering Sciences Makerspace Lab.
* **Hardware Wiring Safety Protocols:**
  To maintain safe logic levels and protect the integrated circuits from electrical damage, our team added structural current-limiting pull-up resistors ($4.7\text{ k}\Omega$) directly across the I2C communication rails (SDA and SCL) linking the ESP32 to the OLED panel, as well as along the single-wire data line of the DHT22.

*(Note: Our group is completing the structural bench adjustments for the physical layout of Architecture b. High-resolution wiring photos, terminal output traces, and physical verification snapshots will be pushed to this repository directory shortly.)*

---

## 4. Engineering Challenges and Troubleshooting Logs

During our lab sprints, the team faced two key bottlenecks that hindered prototyping progress:

### 4.1 Challenge 1: Unstable Analogue Multi-Saturations on Physical MQ-5 Modules
* **The Problem:** During bench testing of the physical layout for Architecture (a), the MQ-5 analog data lines consistently outputted maximum saturation limits ($4095$ raw ADC count at 3.3V), completely obscuring changes in air quality.
* **Diagnostics & Explored Solutions:** We used a digital multimeter (DMM) to probe the sensor's power pins and found that the internal $\text{SnO}_2$ tin-dioxide heating matrix was cold to the touch. Measuring current draw from the laboratory power supply revealed the sensor was drawing less than $10\text{ mA}$, far below its specified $160\text{ mA}$ thermal rating. This confirmed a fractured internal heater coil trace inside the module.
* **Resolution Action:** The faulty module was discarded and replaced with an inspection-verified working component from the lab inventory. This allowed us to successfully capture a normal ambient baseline value.

### 4.2 Challenge 2: Lab Access Windows and Scheduling Conflicts
* **The Problem:** Balancing individual group lecture timetables with restricted lab opening hours limited our physical build and hardware testing windows.
* **Mitigation Action:** To maximize our limited bench time, the team adopted a virtual-first methodology. We completely coded, built, and debugged our program structures in the Wokwi simulator *prior* to handling physical components. This parallel approach ensured that when lab access was granted, we spent our time on structural wiring and physical troubleshooting rather than basic code syntax errors.

---

## 5. System Link-Layer Power Constraints Analysis

Our physical testing confirmed several critical power behavior patterns first identified in our CAT 2 Power Budget calculations:
1. **The Heating Element Overhead:** Bench testing verified that the MQ-5 requires a continuous current draw of $160\text{ mA}$ to maintain its operating temperature. Leaving this element powered indefinitely will quickly exhaust battery reserves during periods without sunlight. To address this, our production code will use a transistor-gating mechanism (P-channel MOSFET) to power down this line during deep-sleep windows.
2. **Topology Impact:** Because a Zigbee mesh configuration requires the radio transceiver to stay continuously awake ($33\text{ mA}$) to route data packets, our software structure favors the MIoTy deployment model, which allows our edge components to completely power down between transmission windows.

---

## 6. Evidence of Groupwork and Collaboration

In compliance with project specifications, our group maintained an active collaboration log. Below is the confirmation of our synchronous team session used to finalize the Wokwi configurations, verify circuit logic parameters, and plan our Makerspace laboratory hardware builds.

### Minutes of Team Discussion Summary:
* **Date & Time:** June 4, 2026 at 12:20 PM EAT.
* **Agenda:** Wokwi simulator verification, syntax alignment for I2C display drivers, and lab hardware assignments.
* **Task Assignment Matrix:**
    * *Kian Muchemi & Albert Ngotho:* Software framework design & Wokwi script debugging.
    * *Nicole Cheruiyot & Timon Kisera:* Physical pin mapping configurations and current calculations.
    * *Bridget Muturi & Trina Kinyua:* Hardware inspection and component testing.
    * *Nyambura Wanjohi:* Documentation layout and repository management.

> *Below is a verification capture of our synchronous group call during the architecture review panel session:*

![Evidence of Groupwork](./Screenshot from 2026-06-04 12-20-43.jpg)