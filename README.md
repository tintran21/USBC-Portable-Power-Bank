# ⚡ USB-C Portable Power Bank Design

This project encompasses the hardware design process for a portable power bank using Altium Designer.

## Project Structure

The project is divided into clear modules:
* `ChargeManagerController.SchDoc`: The Li-Ion/Li-Po battery charge management circuit.
* `StepUpDCDCConverter.SchDoc`: The Boost (DC-DC Converter) circuit for 5V output.
* `portableCharger.PcbDoc`: The completed 2-layer PCB layout.

---

## Circuit Architecture & Operation

The system operates in two distinct phases, handled by two separate functional blocks:

### 1. Charge Controller (Input Block)
This block receives 5V from the wall adapter and safely charges the 3.7V Lithium-Polymer (Li-Po) battery.
* **J1 (USB Type-C):** 5V power input port (5VIN).
* **MCP (MCP73831):** The "brain" of the block. It manages the CC/CV (Constant Current / Constant Voltage) charging algorithm. Link Datasheet: [MCP73831 Datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/MCP73831-Family-Data-Sheet-DS20001984H.pdf)
* **R2 (2kΩ):** Programs the maximum charging current.
* **R3, R4 (5.1kΩ):** Type-C CC1/CC2 pull-down resistors. Mandatory for the Type-C standard to identify the board as a power-sink device (UFP), ensuring compatibility with modern C-to-C cables and PD chargers.
* **C1, C2 (4.7µF):** MLCC filter capacitors for input (5VIN) and output (VBAT) smoothing.
* **LED1 & R1 (1kΩ):** Connected to the STAT pin of the MCP73831 to indicate active charging status, with R1 acting as the current-limiting resistor.
* **S1 (Switch):** Main system power toggle, connecting or isolating the battery voltage (VBAT) from the output load (SW net).
* **P1, P2 (Battery Connectors):** Output headers (BAT OPT1 and BAT OPT2) for the physical Li-Po cell connection and routing to the next stage.

<img width="1173" height="732" alt="image" src="https://github.com/user-attachments/assets/11d7d57f-ee9d-43f6-bb42-ecd09e8e2920" />


### 2. Step-Up DC-DC Converter (Output Block)
This block boosts the variable 3.7V–4.2V battery voltage to a stable 5V output for device charging.
* **PAM (PAM2401):** The highly efficient step-up switching regulator IC. Link Datasheet: [PAM2401 Datasheet](https://cdn.sparkfun.com/assets/learn_tutorials/6/9/5/PAM2401.pdf)
* **L1 (2.2µH Inductor):** The core component that stores magnetic energy during the switching cycle to boost the voltage.
* **R6 (750kΩ) & R7 (100kΩ):** The feedback (FB) voltage divider network. It samples the +5 output voltage, allowing the IC to adjust its duty cycle and maintain a rock-solid 5V.
* **C3, C4 (22µF):** Bulk MLCCs to filter high-frequency switching noise and stabilize input (VBAT) and output (+5) voltage levels.
* **R5 (750kΩ):** Pull-up resistor connected to the EN (Enable) pin, ensuring the boost converter remains active when battery voltage is present.
* **R9, R10, R11, R12:** Apple ID reference voltage dividers (75kΩ and 51kΩ) connected to the D- and D+ pins. These generate specific voltage levels (~2.0V) crucial for Apple devices (iPhone/iPad) to recognize the charger and permit stable current draw.
* **R13, R14 (56kΩ):** Type-C CC1OUT/CC2OUT pull-up resistors tied to the +5 net. This correctly configures the port as a power source (DFP - Downward Facing Port) according to USB-C standards.
* **D1, D2 (40V 1A Schottky Diodes):** Placed in parallel across the output to provide transient voltage suppression and ESD protection for the USB port.
* **LED2 & R8 (4.7kΩ):** The power indicator LED and its current-limiting resistor, signifying an active 5V output.
* **J2 (USB Type-C):** The 5V output port for user devices.

<img width="1092" height="714" alt="image" src="https://github.com/user-attachments/assets/000d76a3-7f98-4c79-8493-03f327c7322f" />

## 🛠 Design Workflow & Techniques

### 1. Component Library Management
To maintain complete control over footprints and 3D models, the project utilizes an independent library management workflow:
* **Using SnapEDA:** Downloaded symbols and footprints for standard components (Headers, connectors, passives) to streamline the process.
* **Custom IC Footprints:** Using Altium's **IPC Compliant Footprint Wizard**. By directly inputting the mechanical dimensions from the datasheet's *Package Outline Dimensions* section, Altium automatically generated precise footprints and 3D models. 

<table>
  <tr>
    <td align="center">
      <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/8f99b6cf-8a7c-4052-aa76-b6a749b2bfa9" />
    </td>
    <td align="center">
      <img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/cccebc97-f569-48d1-b988-48e14532463c" />
    </td>
  </tr>
</table>

### 2. Component Placement & Routing (PCB Layout)
Routing a 2.5 x 4 cm board requires careful consideration regarding high current paths and high-frequency noise:

* **Component Placement: Input/Output Capacitors and Inductors are placed immediately adjacent to the IC pins to minimize the current loop area, reducing parasitic inductance and radiated EMI.
* **Routing Strategy:**
  * High-current paths (VCC/BAT) are routed using wide tracks or **Polygon Pours** to ensure current carrying capacity and minimize voltage drop.
* **Layer Transition & Via Techniques:**

<img width="804" height="510" alt="image" src="https://github.com/user-attachments/assets/0262d351-a79a-404d-9f30-55f40d329bbe" /> <img width="802" height="501" alt="image" src="https://github.com/user-attachments/assets/ada58ef0-638b-4ec9-8806-5a8d11aa2197" />


## How to Open This Project
1. Clone this repository to your device.
2. Open the `USBC_Charger.PrjPcb` file using Altium Designer (Version 20 or newer recommended).
3. Compile the project to verify sheet connections and check for any component errors.

## Contact
📧 [trantin2114@gmail.com](mailto:trantin2114@gmail.com)

