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

</details>

<details>
<summary>Inception of Layout A` CMOS Fabrication process</summary>

### Inception of Layout A` CMOS Fabrication process

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

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f3725674-0176-4019-b5bd-f9c70eab2f56)

`cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign`

` magic -T sky130A.tech sky130_inv.mag &`

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/311f0afd-d35d-4eab-bb49-4c59a65b78eb)

+ Press s 3 times on the layout and type `what` to view the selected layers as shown below

  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/84ba35ce-eccc-4d9c-9d82-592411e494a0)

+ ```
     extract all
     ext2spice cthresh 0 rethresh 0
     ext2spice
  ```   
![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/de22d1e3-e94b-4e6e-a45e-25ee42efed05)

This generates the sky130_in.spice file as shown above. This SPICE deck is edited to include pshort.lib and nshort.lib which are the PMOS and NMOS libraries respectively. In addition, the minimum grid size of inverter is measured from the magic layout and incorporated into the deck as: .option scale=0.01u. The model names in the MOSFET definitions are changed to pshort.model.0 and nshort.model.0 respectively for PMOS and NMOS. 

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/77547452-a0f4-42d9-b953-73bc76abaaa8)


</details>

<details>
<summary>Sky 130 Tech File Labs</summary>

***Steps to Create Standard Cell Layout and Extract Spice Netlist***
### Sky 130 Tech File Labs

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/d6d7c64d-5d19-485e-86bd-acf4be6c9304)

+ type box  in tkcon window to check the minimum value of the layout window.
+ we need to open the spice file using the command
  `gedit sky130_inv.spice`
+ We need to configure it to the below specifications.
  VDD VPWR 0 3.3V
  VSS VGND 0 0
  Va A VGND PUSLE(0V 3.3V 0 0.1ns 0.1 ns 2ns 4ns)
  .tran 1n 20n
  .control
  run 
  .endc
  .end
 ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/fb7dc0c6-c4a4-4013-91a8-8ab14f50f79d)


+ `ngspice sky130_inv.spice`

  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/9cbbf017-a123-4134-88d6-202a5c0f7f4c)

+ `plot y vs time a`
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/cff2096b-c46f-4825-8f45-efb5a94946ff)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/654ed9f5-4904-4161-b482-9c7d49c0660d)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/bbef72dc-b1a5-4910-bd69-0f38a34e6359)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/b023099b-59ca-440a-b352-8f0f059aba07)

***Sky130 PDKS and Steps to Download Magic Tool***

` wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz`

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/ba583356-4d0c-483a-99c4-a548c16cb235)


`mv drc_tests.tgz Desktop/`

`tar xfz drc_tests.tgz `

`magic -d XR`

+ We click 'file' and open the 'met3.mag' file.
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/5848d511-95c4-4fbc-8440-77a5f040660c)

+ If we select an area and type drc why in the tkcon wndow, it will show us the DRC error.
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/1d4a3bbf-215e-4d1d-bcbc-bd96b94bd1a4)

+ To add contact cuts to metal3, first select an area using left and right click. Then hovering over the m3contact we click middle mouse button.
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/2502ea93-3ef7-480c-bf4e-d4fe897fc343)

+ ```
  load tech sky130A.tech
  drc check
  ```
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/3a112cd0-7320-4313-a1f5-78e477ca4d5a)
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/bb6d58be-54fe-497c-bb8a-2d8266e7c459)

+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/e2e2ee4f-1261-42a0-b12e-8941a9f5fa8f)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/66ce6665-0be5-4ee7-8533-7edfa4c68466)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/e38dcfa3-59de-472b-91d5-8ccb37f7bfa5)
+ ```
  tech load sky130A.tech
  drc check
  drc style drc(full)
  drc check
  ```
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/038b521d-9af9-403b-88e0-98ee3f225af0)
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f2e69530-6bc2-478a-8e02-df25c8c6b919)
  - Select the existing nwell.4 and make a copy of it by selecting it and clicking 'c'.
  - Now select a small area on the nwell.4 and add an 'nsubstratecontact' by hovering over it and clicking middle mouse button.
  
</details>


## Pre-layout timing analysis and importance of good clock tree

<details>
<summary>Timing modelling using delay tables</summary>

### Timing modelling using delay tables

***Convert Grid Info to Track Info***

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/07e89800-3f41-4ddf-b455-c531f6c15f20)

`less tracks.info`
![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f81fbe51-73c8-4f74-83ff-84d9ff5f642c)

+ The 'tracks.info' file is used during the routing stage.
+ Routes are the metal traces.
+ Since the PNR is an automated flow, we need to specify where all we want the routes to go.
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/28110779-14da-42ae-98bf-e10351d3b257)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/5fe69c64-8720-41b1-8129-c5b63fdb34c3)
+ The next requirement is that the width of the cell should be the odd multiple of xpitch which is '0.46' as seen in the 'tracks.info' file.
+ As we can see it encloses two full boxes and two halves of one box, totally making three boxes as indicated by the white rectangle.

***Convert Magic Layout to Standard Cell LEF***

+ `save sky130_vsdinv.mag`
+ `lef write`
+ `less sky130_vsdinv.lef`
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/73ea100d-e028-48b1-b959-fe1e610f6410)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/92785598-ed73-404d-a66c-f493f516b5e5)

***Steps to Include New Cell in Synthesis image***

We copy the .mag file that we created to the 'src' folder of picorv32a folder.
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/1708b041-6734-41c5-bf35-198ddb860739)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/63ee6bf7-e0ce-490b-9dca-b10175cb70ff)
+ `vim config.tcl`
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f4223662-619f-4e71-a108-57b76ce09738)
+ add these commands
  ```
  set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"
  set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__slow.lib"
  set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__fast.lib"
  set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"

  set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
  ```
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/ed864ce0-9d39-46bb-9651-43f5aee11220)
+ Open the OpenLANE interactive window and retrieve the 0.9 package.
  ```
  prep -design picorv32a -tag 16-09_19-58 -overwrite
  set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
  add_lefs -src $lefs
  run_synthesis
  ```
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/5f581300-7a00-45f6-8977-1182557373c1)
  ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/7523edbd-56be-41b4-82d5-c34dcb13a13c)

+ `init_floorplan
   run_placement`
+ `magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &`
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f103f653-d5a6-4f75-814a-030835c02cde)

</details>

<details>
<summary>Timming analysis with ideal clocks using openSTA</summary>

### Timming analysis with ideal clocks using openSTA

+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/d679ec5e-fcca-41c0-b64b-8bbb497cbf8c)
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/10d0d230-a905-481b-8e25-cae8acd57a1f)
+ create two files in openalane
  
   - ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/2910fef6-106b-4adf-9fe8-db639a9cfadf)

   - ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/7f90314f-c86b-47c6-9877-dab5ee613e11)
+ `sta pre_sta.conf`
+ ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/58a913bd-2767-4b88-ae19-cc4ee899caa1)
  
</details>

<details>
<summary>Clock tree synthesis TrintonCTS and signal integrity</summary>

### Clock tree synthesis TrintonCTS and signal integrity

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/05f3ec72-6018-499a-990d-887571df46d0)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/3b61293c-86bd-4f23-af55-097097818c16)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/1b62459f-0e32-43e1-80c1-17f081060196)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/e80a6b15-faee-4bab-94ef-05b7a75f6979)

+ `run_cts`
 ![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/3527d6bc-7941-4421-9dcb-e2a9f957910f)

</details>

<details>
<summary>Timing analysis with real clocks using open STA</summary>

### Timing analysis with real clocks using open STA

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/732b9197-34f6-4b4b-a690-024aacdb11bd)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/62d346f0-9746-4bb7-81f0-8c2df3cabfd2)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/f2cd6568-33f7-4211-b365-2a7d3497905a)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/5f11307a-778e-4413-8a35-f5070fec38df)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/db1c73d3-b955-4a7d-a79e-65d35f4eaae4)

```
openroad
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/03-07_11-25/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock (all_clocks)
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/c56ce5a3-4eec-4e35-a964-f7644ce0a82e)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/6289ae65-36e9-41f5-87f3-6d8c970884b2)

![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/daca31c1-7909-4c73-8df9-689b76136750)

``
report_clock_skew -hold
report clock_skew -setup
``
![image](https://github.com/ShashidharReddy01/pes_openlane_pd/assets/142148810/a5481a54-ad53-4be8-b0ae-ea32345487e7)

</details>

## Final steps for RTL2GDS using tritonRoute and openSTA

<details>
<summary>Routing and design rule check(DRC)</summary>

### Routing and design rule check(DRC)

+ Introduction to Maze Routing Â LeeÂs algorithm
+ Lees Algorithm conclusion
+ Design Rule Check

</details>

<details>
<summary>Power Distribution Network and routing</summary>

### Power Distribution Network and routing

`gen_pdn`


We can confirm the success of PDN by checking the current def environment variable: `echo $::env(CURRENT_DEF)`

***Routing***
OpenLANE uses the TritonRoute tool for routing. There are 2 stages of routing:

Global routing: Routing region is divided into rectangle grids which are represented as course 3D routes (Fastroute tool)
Detailed routing: Finer grids and routing guides used to implement physical wiring (TritonRoute tool)
Features of TritonRoute:

Honouring pre-processed route guides
Assumes that each net satisfies inter guide connectivity
Uses MILP based panel routing scheme
Intra-layer parallel and inter-layer sequential routing framework
Running routing step in TritonRoute as part of openLANE flow:

`run_routing`
SPEF Extraction

To use this engine we need to go to
`cd Desktop/work/tools/SPEF_Extractor`
`python3 /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/16-09_19-58/tmp/merged.lef` `/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/16-09_19-58/results/routing/picorv32a.def`
SPEF file is created in 
`/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/16-09_19-58/results/routing/`


</details>

