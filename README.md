# OPENLANE 
- [Inception of open source EDA OpenLANE and Sky130 PDK](#Inception-of-open-source-EDA-OpenLANE-and-Sky130-PDK)
- [Good floorplan vs bad floorplan and introduction to library cells](#Good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
- [Design library cell using Magic Layout and ngspice characterization](#Design-library-cell-using-Magic-Layout-and-ngspice-characterization)
- [Pre-layout timing analysis and importance of good clock tree](#Pre-layout-timing-analysis-and-importance-of-good-clock-tree)
- [Final steps for RTL2GDS using tritonRoute and openSTA](#Final-steps-for-RTL2GDS-using-tritonRoute-and-openSTA)

##  Inception of open source EDA OpenLANE and Sky130 PDK

<details>
<summary>How to talk to computers</summary>

### How to talk to computers

+ Introduction to QFN-48 Package, chip, pads, core, die and IPs


**QFN-48 Package and Chip:**

- A QFN-48 package is a common type of package for microcontrollers.
- It measures 7mm x 7mm and houses a chip at its center.
- The chip is the central processing unit and is connected to the package using wire bonding.
- The chip contains components called "PADs" that link it to a circuit for signal processing.
- The open space within the chip is known as the "Core," which handles most of the processing tasks.
- The chip itself, known as the "Die," is a small piece of silicon where computations occur.
- Components like SRAM, ADC, DAC, and others within the chip are referred to as "Foundry IPs" (Intellectual Properties).

**Introduction to RISC-V:**

- RISC-V is an open-source instruction set architecture (ISA) based on reduced instruction set computing (RISC) principles.
- ISA defines the instructions a computer's processor can execute.
- To run a C program on specific hardware (e.g., QFN-48), it's compiled to RISC-V assembly language, then to machine language.
- Hardware Description Language (HDL) serves as an intermediary layer for hardware design.

**HDL (Hardware Description Language):**

- HDL is a specialized programming language used to describe electronic circuits and systems.
- Two primary types are Verilog and VHDL.

**RTL-GDS Flow:**

- RTL (Register-Transfer Level) is used to implement RISC-V specifications.
- Transitioning from RTL to GDS (Graphics Data System) aligns chip layout with RISC-V specifications.

**From Software Applications to Hardware:**

- Application software performs specific tasks for end-users.
- System software manages hardware resources and enables application execution.
- Operating systems control tasks like memory management, process scheduling, and file management.
- Compilers translate high-level code into assembly language.
- Assemblers translate assembly code into machine code.
- RTL represents digital circuit behavior.
- Hardware encompasses the physical components of a computer or electronic device.


</details>

<details>
<summary> Soc design and Open LANE</summary>

###  Soc design and Open LANE

**PDK (Process Design Kit):**
1. PDK is provided by semiconductor manufacturers.
2. It assists designers in creating integrated circuits (ICs) with specific fabrication processes.
3. PDK includes detailed information, models, and files tailored to the manufacturer's technology.
4. Designers rely on PDK to develop and validate their IC designs.
5. PDK ensures compatibility with the fabrication process and helps meet design requirements.

**EDA Tools (Electronic Design Automation Tools):**
1. EDA tools are software applications used for electronic system design.
2. They automate various design tasks for integrated circuits (ICs) and circuit boards.
3. EDA tools play a crucial role in verifying hardware functionality before manufacturing.
4. They enhance design efficiency and reduce errors in the design process.
5. EDA tools encompass a wide range of functions, from circuit simulation to layout design.

**Open-source Digital ASIC Design Components:**
1. RTL IP's (Register Transfer Level Intellectual Properties) are designs available from open repositories.
2. Open-source EDA tools, such as qflow and openROAD, are used for ASIC design and validation.
3. Open-source PDKs provide process-specific data and are essential for ASIC design.
4. The combination of these components enables fully open-source ASIC design.
5. Open-source resources promote transparency and accessibility in chip design.

**ASIC Design Flow:**
1. ASIC design flow is a structured process for creating custom integrated circuits (ICs).
2. It involves multiple stages, from RTL (Register Transfer Level) design to GDS2 layout generation.
3. Each stage has specific tasks, such as synthesis, placement, routing, and verification.
4. The goal is to transform a high-level RTL design into a manufacturable layout.
5. ASIC design flow ensures that the design adheres to fabrication and timing requirements.

**OpenLANE and Strive Chipsets:**
1. OpenLANE is an open-source toolchain for automating custom silicon chip design.
2. It reduces the need for human intervention in the design process.
3. OpenLANE is compatible with open PDKs like SkyWater and XFAB180.
4. Strive is a family of open-source System-on-Chips (SoCs).
5. Strive chipsets include open PDKs, EDA tools, and RTL designs.
6. Both OpenLANE and Strive contribute to the open-source hardware ecosystem, promoting accessibility and transparency in chip design.
</details>

<details>


<summary>Get familiar to open source EDA tools</summary>

### Get familiar to open source EDA tools

**Skywater-130 PDK (Process Design Kit):**
- The Skywater PDK files for this project are found under $PDK_ROOT.
- These files include:
  - **Skywater-pdk:** Contains all PDK-related files provided by the foundry.
  - **Open_pdks:** Includes scripts that bridge the gap between closed-source and open-source PDKs, ensuring compatibility with EDA tools.
  - **Sky130A:** Houses open-source compatible PDK files.

**Initiating OpenLane:**
- OpenLane is initiated using a key script called `flow.tcl`, which manages the design processes.
- To import the necessary software dependencies into OpenLane, use the command `package require openlane 0.9`.

**Components of OpenLane:**
- OpenLane consists of various components, including:
  - **Src folder:** Contains Verilog files and SDC (Synopsys Design Constraints) files.
  - **Config.tcl files:** These files store design-specific configuration settings used by OpenLane.

**Preparing the Design for the Flow:**
- To prepare the design for the flow, use the command `prep -design <design_name> -tag <tag>`.
- This step creates a "runs" directory where all the design results will be stored.

**Synthesis:**
- Synthesis is a crucial step in ASIC design.
- It is initiated using the command `run_synthesis`.
- An important early task is calculating the flop ratio, which is the ratio of D flip-flops to the total number of cells in the design.
</details>



## Good floorplan vs bad floorplan and introduction to library cells

<details>
<summary>Chip Floor planning and considerations</summary>

### Chip Floor planning and considerations

1. **Define Width and Height of Core and Die:**
   - The "die" encompasses the entire semiconductor chip, including the core, I/O pads, and additional features.
   - The "core" is the central area of the chip where most of the active circuitry resides, such as CPU, GPU, memory, and logic components.
   - Defining the width and height of both the core and die is crucial for setting the chip's overall dimensions and determining how much space each component can occupy within these boundaries.

2. **Utilization Factor:**
   - Utilization factor is a critical metric in floor planning that measures how efficiently the available chip area is utilized.
   - It is calculated as the ratio of the area occupied by the actual components (core, cells, etc.) to the total available chip area.
   - High utilization factors indicate efficient space usage, while low values may signify wasted space or inefficient placement.

3. **Location of Pre-Placed Cells:**
   - Pre-placed cells are specific blocks or cells (e.g., memories, clock gating cells) manually placed by the designer before automated placement tools are applied.
   - These cells have predetermined locations on the chip to optimize placement for performance, power, and routing considerations.
   - Careful placement of pre-placed cells can significantly impact the chip's overall performance and power efficiency.

4. **De-coupling Capacitors:**
   - De-coupling capacitors are essential components in large integrated circuits to address voltage drops and noise issues.
   - They work by storing electrical energy and discharging it quickly to filter out high-frequency noise and transient voltage fluctuations.
   - Properly sizing and placing de-coupling capacitors is critical for ensuring stable and noise-free operation of the chip.

5. **Power Planning:**
   - Power planning is a crucial aspect of floor planning that focuses on managing the distribution of power to various parts of the chip.
   - It aims to minimize noise in digital circuits caused by voltage droop (sudden voltage drops) and ground bounce (noise due to ground connections).
   - This involves designing robust power distribution networks (PDNs) with multiple power strap taps to reduce resistance and ensure a steady power supply to all components.

6. **Pin Placement:**
   - Pin placement optimization is vital for minimizing buffering, reducing power consumption, and meeting timing constraints.
   - HDL (Hardware Description Language) netlists guide the placement of specific pins, ensuring that critical connections are made efficiently.
   - Careful placement of pins is essential for achieving optimal chip performance and functionality.

7. **Steps to Run Floorplan using OpenLANE:**
   - Running floor planning in OpenLANE involves executing the "run_floorplan" command.
   - You need to navigate to the appropriate directory where the floorplan results are stored.
   - The "magic" tool is used with technology libraries (LEF and DEF files) to visualize the floorplan, allowing designers to inspect and refine the placement of components.

8. **Layout:**
   - Layout images provide a visual representation of the overall chip design, including detailed views of specific regions.
   - These images are essential for designers to assess the physical placement of components and identify any potential issues.

9. **Standard Cells:**
   - Standard cells are fundamental building blocks of digital circuits and include logic gates, flip-flops, and other basic components.
   - Displaying images of standard cells used in the chip design helps to understand the basic building blocks that make up the chip's digital logic.

</details>

<details>
<summary>Library binding and Placements</summary>

### Library binding and Placements

**Netlist Binding and Initial Place Design**

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/7c280c13-7fe3-4044-a5d5-e1777f75f922)

In real-life integrated circuit design, logic gates and cells are abstracted as rectangular or square components with specific dimensions. These dimensions are essential, as they determine how efficiently space is utilized on the chip. The placement of these components is a critical part of the design process.

**Library Consideration:**
- An essential aspect of chip design is the library, which contains various types of cells with different shapes, sizes, flavors, and timing characteristics.
- These cells serve as the fundamental building blocks of the chip and are selected based on their functionality and performance requirements.

**Placement in the Core Area:**
- Components from the netlist are strategically placed within the core area of the chip.
- Placement decisions take into account the convenience of distances from the pins and the signal propagation requirements of the circuit.
- For instance, when signals need to propagate quickly from one flip-flop (FF1) to another (FF2), they are placed in close proximity to minimize signal delay.
- Buffers may be added to ensure signal integrity when there is a slight delay for the signal to travel from the input pins to FF1.

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/425abd7c-a5e9-496b-a254-cf378d3cf712)


**Viewing the Placement:**
- To visualize the placement of components, the "run_placement" command is executed within the OpenLANE shell.
- This command reads the placement information from the '/picorv32a.placement.def' file generated during the placement step.
- By navigating to the placement directory using "cd ../placement/" and using the "magic" tool with technology libraries, designers can visualize the placement design.

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/33e3aa3a-dc6a-4c22-a0e0-1e582ba188af)

**Standard Ce
ll Rows:**
- The placement design consists of rows of standard cells, each containing logic gates, flip-flops, and other fundamental components.
- These rows are strategically organized to optimize chip performance, power efficiency, and manufacturability.
- Careful placement ensures that signals flow efficiently through the circuit while minimizing delays and maintaining signal integrity.

</details>

<details>
<summary>Cell binding and characterization flows</summary>

### Cell binding and characterization flows

**Cell Design Flow:**
- **Inputs:** Process Design Kits (PDKs) with DRC and LVS rules, SPICE models, library, and user-defined specs.
- **Design Steps:** Circuit Design, Layout Design (Euler Path and Stick Diagram), Characterization.
- **Outputs:** CDL (Circuit Description Language), GDSII, LEF, extracted SPICE netlist (.cir).

**Characterization Flow (for an inverter):**
1. **Read Model Files:** Gather information about the inverter's characteristics from model files.
2. **Read Extracted SPICE Netlist:** Obtain the SPICE netlist that describes the inverter's behavior.
3. **Recognize Buffer Behavior:** Identify the behavior of the inverter, which is essentially a buffer.
4. **Attach Power Sources:** Connect the necessary power sources to the circuit.
5. **Apply Stimulus:** Provide an input signal to the inverter.
6. **Read Sub-Circuit:** Analyze the sub-circuit of the inverter.
7. **Provide Output Capacitances:** Specify the required output capacitances.
8. **Provide Simulation Commands:** Set up simulation commands for characterization.


</details>

<details>
<summary>General timing characterization parameters</summary>

### General timing characterization parameters

**Timing Characterization:**
- **Slew Low Rise Threshold:** 20%
- **Slew High Rise Threshold:** 80%
- **Slew Low Fall Threshold:** 20%
- **Slew High Fall Threshold:** 80%
- **Input Rise Threshold:** 50%
- **Input Fall Threshold:** 50%
- **Output Rise Threshold:** 50%
- **Output Fall Threshold:** 50%
- **Propagation Delay:** Calculate as the time difference between time(out_fall_thr) and time(in_rise_thr).
- **Transition Time:**
  - On Rise: Calculate as time(slew_high_rise_thr) minus time(slew_low_rise_thr).
  - On Fall: Calculate as time(slew_high_fall_thr) minus time(slew_low_fall_thr).

These timing characteristics are vital for understanding and characterizing the inverter's performance and behavior within a digital circuit.
</details>

## Design library cell using Magic Layout and ngspice characterization

<details>
<summary>Labs for CMOS inverter ngspice simulations</summary>

### Labs for CMOS inverter ngspice simulations

**OpenLANE** allows users to make changes to environment variables on the fly. For instance, if we wish to change the pin placement from equidistant to some other style of placement we may do the following in the openLANE flow:

`set ::env(FP_IO_MODE) 2`
SPICE Deck creation & Simulation
A SPICE deck includes information about the following:

+ Model description
+ Netlist description
+ Component connectivity
+ Component values
+ Capacitance load
+ Nodes
+ Simulation type and parameters
+ Libraries included

***CMOS inverter Switching Threshold Vm***
Thw sitching threshold of a CMOS inverter is the point on the transfer characteristic where Vin equals Vout (=Vm). At this point both PMOS and NOMOS are in ON state which gives rise to a leakage current

**16 Mask CMOS Fabrication**

The 16-mask CMOS process consists of the following steps:

+ Selection of subtrate: Secting the body/substrate material
+ Creating active region for transistors: Isolation between active region pockets by SiO2 and Si3N4 deposition followed by photolithography and etching
+ N-well and P-well formation: Ion implanation by Boron for P-well and by Phosphorous for N-well formation
+ Formation of gate terminal: NMOS and PMOS gates formed by photolithography techniques
+ LDD (lightly doped drain) formation: LDD formed to prevent hot electron effect
+ Source & drain formation: Screen oxide added to avoid channelling during implants followed by Aresenic implantation and annealing
+ Local interconnect formation: Removal of screen oxide by HF etching. Deposition of Ti for low resistant contacts
+ Higher level metal formation: CMP for planarization followed by TiN and Tungsten deposition. Top SiN layer for chip protection

**Inverter Standard cell Layout & SPICE extraction**
The Magic layout of a CMOS inverter will be used so as to intergate the inverter with the picorv32a design. To do this, inverter magic file is sourced from vsdstdcelldesign by cloning it within the openlane_working_dir/openlane directory as follows:

`git clone https://github.com/nickson-jose/vsdstdcelldesign`

`cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign`

` magic -T sky130A.tech sky130_inv.mag &`
</details>

<details>
<summary>Inception of Layout A` CMOS Fabrication process</summary>

### Inception of Layout A` CMOS Fabrication process

+ Create Active regions
+ Formation of N-well and P-well
+ Formation of gate terminal
+ Lightly doped drain (LDD) formation
+ Source Â drain formation
+ Local interconnect formation
+ Higher level metal formation
+ Lab introduction to Sky130 basic layers layout and LEF using inverter
+ Lab steps to create std cell layout and extract spice netlist
</details>

<details>
<summary>Sky 130 Tech File Labs</summary>

### Sky 130 Tech File Labs

+ Lab steps to create final SPICE deck using Sky130 tech
+ Lab steps to characterize inverter using sky130 model files
+ Lab introduction to Magic tool options and DRC rules
+ Lab introduction to Sky130 pdk's and steps to download labs
+ Lab introduction to Magic and steps to load Sky130 tech-rules
+ Lab exercise to fix poly.9 error in Sky130 tech-file
+ Lab exercise to implement poly resistor spacing to diff and tap
+ Lab challenge exercise to describe DRC error as geometrical construct
+ Lab challenge to find missing or incorrect rules and fix them
</details>


## Pre-layout timing analysis and importance of good clock tree

<details>
<summary>Timing modelling using delay tables</summary>

### Timing modelling using delay tables


+ Lab steps to convert grid info to track info
+ Lab steps to convert magic layout to std cell LEF
+ Introduction to timing libs and steps to include new cell in synthesis
+ Introduction to delay tables
+ Delay table usage Part 1
+ Delay table usage Part 2
+ Lab steps to configure synthesis settings to fix slack and include vsdinv
</details>

<details>
<summary>Timming analysis with ideal clocks using openSTA</summary>

### Timming analysis with ideal clocks using openSTA

+ Setup timing analysis and introduction to flip-flop setup time
+ Introduction to clock jitter and uncertainty
+ Lab steps to configure OpenSTA for post-synth timing analysis
+ Lab steps to optimize synthesis to reduce setup violations
+ Lab steps to do basic timing ECO
</details>

<details>
<summary>Clock tree synthesis TrintonCTS and signal integrity</summary>

### Clock tree synthesis TrintonCTS and signal integrity

+ Clock tree routing and buffering using H-Tree algorithm
+ Crosstalk and clock net shielding
+ Lab steps to run CTS using TritonCTS
+ Lab steps to verify CTS runs
</details>

<details>
<summary>Timing analysis with real clocks using open STA</summary>

### Timing analysis with real clocks using open STA

+ Setup timing analysis using real clocks
+ Hold timing analysis using real clocks
+ Lab steps to analyze timing with real clocks using OpenSTA
+ Lab steps to execute OpenSTA with right timing libraries and CTS assignment
+ Lab steps to observe impact of bigger CTS buffers on setup and hold timing
</details>

## Final steps for RTL2GDS using tritonRoute and openSTA

<details>
<summary>Routing and design rule check(DRC)</summary>

### Routing and design rule check(DRC)

+ Introduction to Maze Routing Â LeeÂs algorithm
+ LeeÂs Algorithm conclusion
+ Design Rule Check

</details>

<details>
<summary>Power Distribution Network and routing</summary>

### Power Distribution Network and routing

+ Lab steps to build power distribution network
+ Lab steps from power straps to std cell power
+ Basics of global and detail routing and configure TritonRoute
</details>

<details>
<summary>TritonRoute Features</summary>

### TritonRoute Features

+ TritonRoute feature 1 - Honors pre-processed route guides
+ TritonRoute Feature2 & 3 - Inter-guide connectivity and intra- & inter-layer routing
+ TritonRoute method to handle connectivity
+ Routing topology algorithm and final files list post-route
</details>

