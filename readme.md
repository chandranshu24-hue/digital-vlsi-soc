# Digital VLSI SoC Design and Planning

---

## **Introduction**

A **System on Chip (SoC)** represents the integration of multiple complex subsystems onto a
single silicon die using VLSI (Very Large Scale Integration) technology.  
Modern electronic systems demand **high performance, low power consumption, reduced form
factor, and cost efficiency**, which makes SoC-based design a fundamental requirement in
today’s semiconductor industry.

Digital VLSI SoC design focuses on transforming **abstract system-level functionality** into
a **manufacturable silicon implementation**, while meeting strict constraints on **timing,
power, area, and reliability**.

---

## **What is a System on Chip (SoC)?**

A **System on Chip (SoC)** is an integrated circuit that consolidates all major components of
a complete electronic system onto a single chip. Unlike traditional board-level designs,
where processors, memory, and peripherals exist as separate ICs, an SoC embeds these elements
within one silicon substrate.

### Typical Components of a Digital SoC:
- **Processing Units**  
  (CPU cores such as RISC-V, ARM, DSPs)
- **Memory Subsystems**  
  (SRAM, cache controllers, memory interfaces)
- **Interconnect Fabric**  
  (buses, crossbars, NoC)
- **Peripheral Interfaces**  
  (UART, SPI, I2C, GPIO, timers)
- **Clock and Reset Networks**
- **Power Management Logic**

> In essence, an SoC behaves like an entire electronic system implemented inside a single IC.

---

## **Why SoC Design is Critical in Modern Electronics**

The evolution of semiconductor technology has enabled billions of transistors to be placed on
a single die. SoC integration leverages this capability to achieve:

- **Reduced Power Consumption**  
  Shorter interconnects reduce switching power
- **Higher Performance**  
  On-chip communication is faster than off-chip signaling
- **Smaller Form Factor**  
  Essential for mobile and embedded applications
- **Lower System Cost**  
  Fewer external components and simplified PCB design

These advantages make SoCs the backbone of:
- Smartphones
- IoT devices
- Automotive electronics
- AI accelerators
- Embedded control systems

---

## **Role of Planning in Digital VLSI SoC Design**

SoC design is not just about writing RTL code. **Planning is a decisive phase** that determines
the success of the chip before physical implementation begins.

### Key Planning Aspects:
- **Architectural Planning**  
  Selecting IPs, bus structures, and processing hierarchy
- **Performance Planning**  
  Clock frequency targets and pipeline depth
- **Power Planning**  
  Voltage domains, power grids, and decoupling strategy
- **Area Planning**  
  Core utilization, aspect ratio, and macro placement
- **Technology Planning**  
  Choice of PDK, standard cell libraries, and design rules

Poor planning can lead to:
- Routing congestion
- Timing violations
- Excessive power consumption
- Costly design iterations

---

## **From Concept to Silicon**

Digital VLSI SoC design bridges the gap between **system-level intent** and **physical silicon
realization**. Each planning decision made at the early stage directly impacts downstream
steps such as synthesis, floorplanning, placement, routing, and signoff.

This repository documents a **complete, open-source SoC design methodology** using
**OpenLANE and Sky130 PDK**, demonstrating how theoretical concepts translate into a working
silicon layout.

---

## Introduction to QFN-48 Package, Chip, Pads, Core, Die and IPs

Modern VLSI systems are not limited to internal circuit design alone. The **package,
interconnects, and foundry-provided building blocks** play a critical role in ensuring that
a fabricated chip can communicate reliably with the external world.

---

### QFN-48 Package

The **QFN-48 (Quad Flat No-Lead)** package is a surface-mount IC package that provides
48 electrical connections without external leads.

#### Key Advantages:
- Low parasitic inductance and resistance
- Compact form factor
- Improved electrical and thermal performance
- Suitable for high-speed digital SoCs

The QFN-48 package mechanically and electrically protects the silicon die while enabling
signal and power exchange with the PCB.

---

### Chip and Die

- **Die**:  
  The **die** is the actual piece of silicon on which transistors, interconnects, and
  functional blocks are fabricated.

- **Chip**:  
  The term **chip** generally refers to the packaged version of the die, including the
  silicon, package, and external connections.

> In physical design terminology, layout generation focuses on the **die**, while system-level
integration considers the **chip** as a whole.

---

### Core Area

The **core** is the central region of the die where the main digital logic is placed.

#### Core Contains:
- Standard cells (logic gates, flip-flops)
- Memory blocks
- Clock tree
- Local routing

The size and utilization of the core directly affect:
- Timing performance
- Routing congestion
- Power consumption

---

### Pads and IO Interface

**Pads** form the interface between the internal circuitry and the external environment.

#### Types of Pads:
- Input pads
- Output pads
- Bidirectional pads
- Power pads (VDD, GND)

> Any signal that enters or leaves the chip **must pass through IO pads**.

Pads provide:
- ESD protection
- Voltage level compatibility
- Signal buffering

---

### Wire Bonds

**Wire bonds** are fine metallic wires (typically gold or aluminum) that connect the
bond pads on the die to the corresponding package terminals.

#### Purpose of Wire Bonds:
- Transfer signals between die and package
- Connect power and ground
- Maintain electrical continuity

Wire bonding enables communication between the **external world** and the **internal silicon
logic**.

---

### Foundry IPs

**Foundry IPs** are pre-verified, process-specific components provided by the semiconductor
foundry.

#### Examples:
- Standard cell libraries
- IO cells
- Decoupling capacitors
- Well tap cells
- Antenna diodes

Using foundry IPs ensures:
- Design rule compliance
- Electrical reliability
- Manufacturability

---

### Intellectual Property (IP)

**IP (Intellectual Property)** refers to reusable functional blocks integrated into a design.

#### Common Digital IPs:
- RISC-V processor cores
- Memory controllers
- Peripheral interfaces (UART, SPI, I2C)
- Bus interconnects

IPs reduce:
- Design time
- Verification effort
- Development cost
> SoC design is fundamentally the task of **integrating multiple IPs** into a single,
cohesive silicon system 

## Explanation of RISC-V Software-to-Hardware Flow (Image Description)

The above figure illustrates the complete transformation of a **RISC-V based software program**
into its **physical hardware implementation on silicon**. It visually connects three major
abstraction layers: **software**, **architecture**, and **physical design**.

---

### Left Section: RISC-V Software and Architecture View

The left side of the image shows a **RISC-V software execution environment**, where a C program
is compiled using RISC-V toolchains.

- The **C source code** represents the highest level of abstraction.
- This code is compiled into **RISC-V assembly instructions** that strictly follow the
  RISC-V Instruction Set Architecture (ISA).
- The assembly is further translated into **machine-level binary instructions**, which
  are sequences of 0s and 1s understood by hardware.

This section emphasizes that **RISC-V ISA acts as the contract between software and hardware**.
The compiler generates instructions that the hardware implementation is guaranteed to execute.

---

### Middle Section: Instruction Execution Flow

The transformation sequence shown below the image highlights the mandatory execution path:

C Program → Assembly Program → Machine Code (Binary)

Each arrow represents a **loss of abstraction and gain of hardware specificity**:
- C code focuses on algorithmic intent
- Assembly reflects processor operations
- Binary directly controls digital logic

Without this conversion chain, software cannot be executed on silicon.

---

### Bottom Section: RTL Implementation (picorv32 CPU Core)

The bottom-left portion of the image highlights the **RTL implementation of the RISC-V processor**,
specifically the `picorv32` core.

This RTL code:
- Decodes binary instructions
- Generates control signals
- Activates the datapath (ALU, registers, memory interface)

Each RISC-V instruction triggers a unique combination of logic transitions inside the core.
Thus, the ISA behavior is physically realized through **RTL hardware structures**.

---

### Right Section: Physical Layout (qflow / OpenLANE)

The right side of the image shows the **physical layout view** of the synthesized RISC-V core.

This layout represents:
- Standard cells placed within the core region
- Metal interconnects routing signals
- Clock and power distribution networks

At this level:
- Instructions are no longer abstract concepts
- Execution corresponds to **actual transistor switching**
- Software behavior manifests as physical electrical activity

---
<img width="624" height="236" alt="image" src="https://github.com/user-attachments/assets/bf0fd156-17ca-4f8b-bf15-4cdde39cbe3f" />
<img width="624" height="342" alt="image" src="https://github.com/user-attachments/assets/d39367ab-bcce-4bc9-aade-0097a89b126d" />



The image demonstrates that **software execution on a processor is ultimately a physical event**.
Every instruction written in C eventually becomes:
- A sequence of binary values
- Logic transitions in standard cells
- Voltage changes on silicon interconnects

Software → Architecture → RTL → Layout → Silicon


Understanding this relationship is fundamental to **Digital VLSI SoC Design**, as decisions made
at the software or architectural level directly impact physical design complexity, power
consumption, and performance.

### FROM APPLICATION SOFTWARE TO HARDWARE
<img width="624" height="337" alt="image" src="https://github.com/user-attachments/assets/ace39069-69b3-4072-8375-a269ee6c4e92" />
<img width="398" height="218" alt="image" src="https://github.com/user-attachments/assets/4b2d4fef-04d5-43a6-b796-c87afc348c50" />
<img width="348" height="322" alt="image" src="https://github.com/user-attachments/assets/260b6041-1b44-47a3-823d-a2794020c341" />

## Introduction to the Components of ASIC Design

Application Specific Integrated Circuit (ASIC) design is a structured engineering process
that transforms a functional requirement into a **fabricated silicon chip**. This process
relies on the coordinated interaction of **design descriptions, automation tools, and
process-specific data**.

At a high level, **Digital ASIC Design** is built on three foundational pillars:
1. RTL IPs
2. EDA Tools
3. PDK Data

Each component plays a critical role in ensuring that the final design is **functionally
correct, physically realizable, and manufacturable**.

---

## Digital ASIC Design

Digital ASIC design focuses on implementing logic using **digital abstraction**, where
signals assume discrete values (`0` or `1`). The design flow progresses through multiple
levels of abstraction, ultimately resulting in a physical layout.

The following subsections describe the core building blocks of a digital ASIC design flow.

---

## RTL IPs (Register Transfer Level Intellectual Property)

### What is RTL?

RTL (Register Transfer Level) describes how data moves between registers and how logical
operations are performed on that data within a clock cycle. RTL is typically written in
hardware description languages such as **Verilog** or **VHDL**.

RTL represents the **functional behavior** of the design without any physical information.

---

### RTL IPs in ASIC Design

**RTL IPs** are reusable, pre-designed logic blocks that perform specific functions.

#### Examples of RTL IPs:
- Processor cores (RISC-V, ARM)
- Arithmetic units (ALU, multiplier, divider)
- Memory controllers
- Peripheral interfaces (UART, SPI, I2C)
- Bus protocols (AXI, AHB, Wishbone)

RTL IPs:
- Are technology-independent
- Can be reused across multiple designs
- Serve as the starting point for synthesis

> RTL IPs define *what the chip does*, not *how it is physically built*.

---

## EDA Tools (Electronic Design Automation)

### Role of EDA Tools

EDA tools automate the complex steps involved in converting RTL into a manufacturable
layout. Manual implementation of ASICs is infeasible due to the massive scale and
complexity of modern designs.

EDA tools ensure:
- Correct logic implementation
- Timing closure
- Power optimization
- Physical rule compliance

---

### Major EDA Tool Categories

#### 1. Logic Synthesis Tools
- Convert RTL into a gate-level netlist
- Optimize for area, power, and timing

**Example tools:**
- Yosys
- Synopsys Design Compiler

---

#### 2. Physical Design Tools
- Floorplanning
- Placement
- Clock Tree Synthesis
- Routing

**Example tools:**
- OpenROAD
- Innovus

---

#### 3. Verification and Signoff Tools
- DRC (Design Rule Check)
- LVS (Layout vs Schematic)
- Timing and power analysis

**Example tools:**
- Magic
- Netgen
- OpenSTA

> EDA tools act as the bridge between **design intent** and **physical silicon**.

---

## PDK Data (Process Design Kit)

### What is a PDK?

A **Process Design Kit (PDK)** is a collection of files provided by the semiconductor foundry
that describes how transistors and interconnects behave for a specific manufacturing process.

PDK data ensures that the design can be **manufactured reliably**.

---

### Components of a PDK

A typical PDK contains:

- **Technology files**  
  (Design rules, layer definitions)

- **Standard Cell Libraries**  
  (Logical, timing, power, and physical views)

- **Device Models**  
  (SPICE models for simulation)

- **IO Libraries**  
  (Pads and interface cells)

- **Verification Rules**  
  (DRC, LVS, antenna rules)

---

### Importance of PDK in ASIC Design

- Determines transistor dimensions
- Defines routing layers and constraints
- Enables accurate timing and power estimation
- Ensures fabrication compatibility

> RTL without PDK is abstract; PDK gives **physical meaning** to the design.

---

## Relationship Between RTL, EDA Tools, and PDK

Digital ASIC design succeeds only when **RTL IPs, EDA tools, and PDK data** work together:

---
<img width="624" height="279" alt="image" src="https://github.com/user-attachments/assets/59877f70-8414-42ae-805d-b5b19c2336a6" />
<img width="624" height="265" alt="image" src="https://github.com/user-attachments/assets/3ccbc353-2788-4d3c-b9a4-3ec70d09cafd" />
<img width="624" height="316" alt="image" src="https://github.com/user-attachments/assets/4428740d-f1aa-4869-99f7-738e6c21cc1b" />

# RTL to GDSII Flow – Digital ASIC Design

This section explains the complete **RTL to GDSII flow** used in modern **digital ASIC design**, aligned with industry practices and open-source flows such as **OpenLane**. The intent is to provide a technically accurate and implementation-oriented explanation rather than a high-level overview.

---

## 1. RTL (Register Transfer Level)

RTL is the functional description of the digital circuit written using **Verilog/SystemVerilog**. At this stage, the design focuses purely on behavior and timing at the clock-cycle level, without any physical or technology-specific information.

RTL describes:

* Sequential logic (flip-flops, registers)
* Combinational logic
* Clock and reset behavior

The RTL must be synthesizable and free from constructs that cannot be mapped to hardware.

**Deliverable:** RTL source files (`.v`, `.sv`)

---

## 2. Synthesis

Synthesis converts the RTL into a **gate-level netlist** by mapping logic to standard cells provided by the PDK. This step uses timing constraints to ensure the design meets performance targets.

Key operations during synthesis:

* Boolean optimization
* Technology mapping (logic → standard cells)
* Timing-driven optimization

Inputs:

* RTL
* Standard cell liberty files (`.lib`)
* Timing constraints (`.sdc`)

Outputs:

* Gate-level netlist
* Area, power, and timing reports

The synthesized netlist is still logical; no physical placement exists yet.

---

## 3. Floorplanning and Power Planning (FP + PP)

### 3.1 Floorplanning

Floorplanning defines the **physical boundaries** of the design. It determines how much silicon area the design will occupy and how efficiently that area is used.

Key decisions include:

* Core size and aspect ratio
* Core utilization
* IO pin placement
* Placement blockages

A well-planned floorplan minimizes routing congestion and improves timing closure.

---

### 3.2 Power Planning

Power planning creates a robust **power distribution network (PDN)** to supply VDD and GND uniformly across the chip.

Components of power planning:

* Power rings around the core
* Horizontal and vertical power straps
* Standard cell power rails

This step is critical to avoid IR drop and electromigration issues.

---

## 4. Placement

Placement assigns **exact physical locations** to all standard cells within the defined core area.

Placement is typically done in two phases:

* Global placement (approximate positions)
* Detailed placement (legal, row-aligned positions)

Placement is timing- and congestion-aware, ensuring optimal performance while maintaining routability.

**Output:** Placed DEF database

---

## 5. Clock Tree Synthesis (CTS)

Clock Tree Synthesis builds a balanced clock network to distribute the clock signal to all sequential elements.

CTS addresses:

* Clock skew
* Clock latency
* Clock transition (slew)

The tool inserts buffers and inverters to ensure that clock arrival times are within acceptable limits across the chip.

A well-designed clock tree is essential for reliable setup and hold timing.

---

## 6. Routing

Routing physically connects all placed cells using the available metal layers.

Routing stages:

* Global routing: determines routing paths and congestion regions
* Detailed routing: assigns exact tracks, vias, and metal shapes

Routing must comply strictly with the design rules defined in the PDK.

**Checks during routing:**

* Shorts and opens
* Spacing violations
* Via and width rules

---

## 7. Sign-Off

Sign-off is the final verification stage before tape-out. No design changes are expected beyond this point.

Sign-off checks include:

### Timing Sign-Off

* Static Timing Analysis (STA)
* Setup and hold verification

### Power and Reliability

* IR drop analysis
* Power integrity checks

### Physical Verification

* DRC (Design Rule Check)
* LVS (Layout vs Schematic)
* Antenna checks

Only a clean sign-off qualifies the design for fabrication.

---

## 8. GDSII Generation

GDSII is the final layout database sent to the semiconductor foundry. It contains all geometric and layer information required to manufacture the chip.

At this stage:

* The design is frozen
* No functional or physical changes are allowed

The GDSII file is used to generate photomasks for silicon fabrication.

---

## 9. PDK (Process Design Kit)

The PDK provides all technology-specific data required throughout the flow, including:

* Standard cell libraries
* Design rules
* Timing and power models
* Technology and layer definitions

Examples include **sky130A**, 28nm, and other foundry-specific technologies.

The accuracy of synthesis, placement, routing, and sign-off directly depends on the quality of the PDK.

---

## Flow Summary

```
RTL → Synthesis → Floorplanning → Power Planning → Placement → CTS → Routing → Sign-Off → GDSII
```

This flow represents a standard, industry-accepted methodology for digital ASIC implementation.

###Introduction to OpenLANE detailed ASIC design flow
<img width="624" height="295" alt="image" src="https://github.com/user-attachments/assets/a59b7804-0cf1-4b63-8b63-7857edaf72f7" />

#Synthesis and Logic Verification
The initial stage converts RTL (Verilog) into a structural netlist.

RTL Synthesis: Performed using Yosys and abc.

Design for Test (DFT): Fault is utilized for scan insertion and ATPG. This ensures the hardware can be tested for manufacturing defects via scan-chains.

Logic Equivalence Check (LEC): Every time the netlist is modified (e.g., after Clock Tree Synthesis or Optimization), Yosys is used to formally prove that the functional behavior remains identical to the original RTL.

# Physical Implementation (Place & Route)
The design follows an automated PnR path within the OpenROAD environment:

Floor/Power Planning: Establishing the core area and power distribution network (VDD/GND).

Placement: Execution of Global and Detailed placement to minimize wire length and congestion.

Clock Tree Synthesis (CTS): Balancing the clock distribution to minimize skew across all flip-flops.

Routing: Global and Detailed routing performed via TritonRoute.

# Static Timing Analysis (STA)
Timing sign-off is performed using OpenSTA. We monitor both Setup and Hold slack.

Parasitic Extraction: RC values are extracted from the layout using DEF2SPEF.

Analysis: The flow performs multiple STA iterations (Pre-layout and Post-layout).

Results: In the current iteration (referencing jpeg_encoder), the design met all timing constraints with positive slack.

# Reliability: Antenna Rule Violations
During the Reactive Ion Etching (RIE) process of fabrication, metal segments can act as antennas, accumulating charge that can discharge through and destroy thin gate oxides.

Mitigation Strategy:
We adopted a preventive approach:

Fake Diode Insertion: A "Fake Antenna Diode" is placed next to every cell input during placement.

Antenna Checking: Magic runs an antenna check on the routed layout.

Diode Swapping: If a violation is found, a script replaces the "Fake Diode" with a "Real Diode" cell to leak away the accumulated charge to the substrate.

# Physical Verification (Sign-off)
Final validation is performed to ensure the layout is manufacturable.

DRC (Design Rule Check): Using Magic to verify the layout adheres to Sky130 manufacturing constraints (spacing, width, etc.).

LVS (Layout vs. Schematic): Using Magic and Netgen to compare the extracted SPICE netlist from the layout against the golden Verilog netlist

# Regression and Benchmarking
To maintain flow stability, we run OpenLane Regression Testing across ~70 designs. Results are compared against "Best Known Results" (BKR) to track runtime efficiency and cell density.

### OpenLANE Directory structure in detail
Src file is the place where our Verilog code will be present
<img width="624" height="238" alt="image" src="https://github.com/user-attachments/assets/f849b849-bf19-4a09-addf-21c2fc6097b9" />
<img width="624" height="383" alt="image" src="https://github.com/user-attachments/assets/b9e1f64a-c6ff-4e11-ac8d-64e5f48664ad" />
<img width="624" height="462" alt="image" src="https://github.com/user-attachments/assets/e2d65886-bb9f-4734-92bf-54184be2a4a5" />

### Steps to characterize synthesis results
<img width="624" height="265" alt="image" src="https://github.com/user-attachments/assets/bcf5b1cf-bd31-4be5-9607-696d400591af" />
. Printing statistics

Number of wires:              15482
Number of wire bits:          15864
Number of public wires:        1475
Number of public wire bits:    1857
Number of memories:               0
Number of memory bits:            0
Number of processes:              0
Number of cells:              15762

<img width="385" height="728" alt="image" src="https://github.com/user-attachments/assets/9afd8c1d-df9c-44f1-99bd-59f1a363617a" />
<img width="383" height="758" alt="image" src="https://github.com/user-attachments/assets/53d38311-fb48-4430-ba4a-f88b9006e4b5" />
<img width="382" height="776" alt="image" src="https://github.com/user-attachments/assets/b5f44fe4-cef2-4c75-9ec7-edf218a49c2c" />

 Total D flip flops=1613
 number of cells=15762
 flop ratio=9.77%

 ### Good floorplan vs bad floorplan and introduction to library cells

 # Utilization factor and aspect ratio
 <img width="533" height="193" alt="image" src="https://github.com/user-attachments/assets/d3391055-df35-4dfb-87c2-ad2e8ba33864" />

<img width="438" height="70" alt="image" src="https://github.com/user-attachments/assets/8ba268f3-bd60-488e-b38b-3bb4c20236eb" />

<img width="624" height="206" alt="image" src="https://github.com/user-attachments/assets/2b2c53a5-2ffe-44b9-9eb8-583fd730f02a" />

 <img width="624" height="251" alt="image" src="https://github.com/user-attachments/assets/a96ef999-8cac-492b-95b2-c98e28131e69" />

<img width="624" height="211" alt="image" src="https://github.com/user-attachments/assets/c2b2d984-e3a8-49c8-af45-565b372c7fa5" />

<img width="380" height="458" alt="image" src="https://github.com/user-attachments/assets/bdb5a461-d5ca-4503-808c-496ec0141864" />

Utilization factor= area occupied by the netlist/total area of the core.

 = 4x1 (sq unit)/2 unit x 2 unit= 4 sq unit/ 4 sq unit .

 Utilization factor=1

 Aspect ration =height/width

  Aspect=1 (signifies chip is sq shape)

<img width="314" height="172" alt="image" src="https://github.com/user-attachments/assets/81af3dba-a284-4dde-ac5f-5e3b6d2c51db" />

<img width="378" height="81" alt="image" src="https://github.com/user-attachments/assets/0a75a0ce-12fc-41ed-b49e-71082e5011c6" />

Uf=0.5

<img width="250" height="38" alt="image" src="https://github.com/user-attachments/assets/ae91704c-c276-427f-b339-23efe64217fb" />

# Concept of pre-placed cells

## Preplaced Cells in ASIC Physical Design

### What are Preplaced Cells?
Preplaced cells are **standard cells or macros whose physical locations are fixed before placement**.  
The placer is **not allowed to move** these cells during the automated placement stage.

They are commonly used when:
- Certain logic must remain at a fixed position
- Timing, power, or routing constraints demand fixed locations
- The design is divided into predefined blocks or regions

---

### Why Preplaced Cells are Needed

In large designs, the entire combinational logic is not placed freely.  
Instead, designers often:
- Split logic into **functional blocks**
- Fix important cells at known locations
- Allow remaining cells to be placed around them

This improves:
- Timing predictability
- Floorplanning control
- Congestion management

---

### Explanation Using the Diagram
<img width="624" height="319" alt="image" src="https://github.com/user-attachments/assets/218d4d90-3358-48be-a58f-6104a81798b4" />


#### 1. Original Combinational Logic
All logic gates (A1–A8) initially form a **single combinational cloud**.

Combinational Logic → A1, A2, A3, A4, A5, A6, A7, A8


---

#### 2. Cut Definition
Logical cuts (`cut1`, `cut2`) divide the design into regions.

- These cuts determine **which cells belong to which block**
- After cutting, some cells are assigned fixed regions

---

#### 3. Block Formation
The design is divided into multiple blocks:

Block 1: A1, A2, A3, A4
Block 2: A5, A6, A7, A8


Cells inside each block are **preplaced**:
- Their X/Y coordinates are fixed
- The placer cannot relocate them

---

#### 4. Routing Between Preplaced Blocks
Only the **interconnect routing** between blocks is optimized later.

- Internal block structure remains unchanged
- Global routing must respect fixed cell positions

---

### Key Characteristics of Preplaced Cells

- Fixed physical location
- Not moved by placement algorithms
- Used for macros, hard IPs, critical logic
- Common in hierarchical and block-based designs

---

### Examples of Preplaced Cells
- SRAM macros
- PLLs and analog IPs
- Clock gating cells
- I/O interface logic
- Timing-critical datapath elements

---

### Preplaced Cells in OpenLane

In OpenLane, preplaced cells can be defined using:
- DEF files
- Floorplanning constraints
- Manual macro placement scripts

These cells are honored throughout:
- Placement
- Clock Tree Synthesis (CTS)
- Routing

---

### Summary

| Aspect            | Preplaced Cells |
|-------------------|-----------------|
| Movable by placer | ❌ No |
| Location fixed    | ✅ Yes |
| Used for macros   | ✅ Yes |
| Improves control  | ✅ Yes |

Preplaced cells provide **deterministic physical structure**, enabling better timing closure and predictable layouts in complex ASIC designs.


<img width="562" height="550" alt="image" src="https://github.com/user-attachments/assets/ca7867d2-9065-4055-a44a-7d7a01c0fc40" />

<img width="588" height="290" alt="image" src="https://github.com/user-attachments/assets/eb695724-8bbf-4c28-beb8-594038bab50b" />

<img width="624" height="127" alt="image" src="https://github.com/user-attachments/assets/d56a42a4-9a32-42bf-9f78-19669ef4b58a" />

# Floorplanning and Placement of picorv32a using OpenLANE

This section explains how to run the **floorplan** and **congestion-aware placement** for the `picorv32a` design using the OpenLANE flow, calculate die area, and visualize results in **Magic**.

---

## 1. Running Floorplan for picorv32a Design

### Step 1: Navigate to OpenLANE Directory
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
````

Step 2: Start OpenLANE in Interactive Mode
````bash
./flow.tcl -interactive
````
Step 3: Load OpenLANE Package
``` bash
package require openlane 0.9
````
Step 4: Prepare the picorv32a Design
```bash
prep -design picorv32a
```
<img width="624" height="198" alt="image" src="https://github.com/user-attachments/assets/47ed3f66-1f62-4c7e-b07a-1567a861bdbc" />

<img width="624" height="506" alt="image" src="https://github.com/user-attachments/assets/8a3d9344-c5a4-412f-b93c-6e5319e4c6e6" />

<img width="624" height="457" alt="image" src="https://github.com/user-attachments/assets/f72c726f-e436-4972-b334-db2abdba7a53" />

<img width="407" height="410" alt="image" src="https://github.com/user-attachments/assets/2be76948-c987-4b1e-b779-e90f7b2d26aa" />

Load generated floorplan def in magic tool and explore the floorplan.
<img width="1036" height="645" alt="image" src="https://github.com/user-attachments/assets/378e7cc7-39de-4fd1-9830-2fdbc5b6d231" />

Equidistant placement of ports & Port layer as set through config.tcl & Decap Cells and Tap Cells

<img width="656" height="125" alt="image" src="https://github.com/user-attachments/assets/49f05ef8-00d4-4756-9ff2-4822bc2f53b0" />

Diagonally equidistant Tap cells

<img width="807" height="705" alt="image" src="https://github.com/user-attachments/assets/39f0b970-1d78-48e0-8491-a5e38a1473b7" />

<img width="655" height="565" alt="image" src="https://github.com/user-attachments/assets/89cc4333-6d38-4e16-8bef-eff0013c145e" />

<img width="1222" height="608" alt="image" src="https://github.com/user-attachments/assets/83fb910b-7de1-408e-9f64-79b63b3727a1" />

### Design library cell using Magic Layout and ngspice characterization
```
# Change directory to openlane
cd Desktop/Openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp /home/user/Desktop/openlane/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
````
<img width="624" height="298" alt="image" src="https://github.com/user-attachments/assets/7ac0bcd3-b6bc-4411-81a3-ab9bdfa97db9" />

<img width="624" height="142" alt="image" src="https://github.com/user-attachments/assets/20d8cd7e-c84c-4af0-8e10-10eb249e8997" />

<img width="624" height="77" alt="image" src="https://github.com/user-attachments/assets/ffd20d7f-5742-4ec1-99dd-f70bb6cb27f4" />

<img width="484" height="400" alt="image" src="https://github.com/user-attachments/assets/0ff17780-61af-4936-8bcf-929a3e88f49f" />

<img width="380" height="593" alt="Screenshot 2025-12-12 025645" src="https://github.com/user-attachments/assets/8514161e-d7cb-49b0-90fb-d395f7eeb7a1" />

<img width="1125" height="448" alt="Screenshot 2025-12-12 025729" src="https://github.com/user-attachments/assets/a0250cf5-d6fa-45b4-91da-fc88b9a1cae0" />

<img width="1157" height="439" alt="Screenshot 2025-12-12 030102" src="https://github.com/user-attachments/assets/8f945df3-f494-48a8-b8b9-e59d2ed9f01e" />


`````
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
`````

<img width="847" height="246" alt="image" src="https://github.com/user-attachments/assets/eae0b4db-fbb4-4b98-8630-8f85eaed4269" />

<img width="791" height="586" alt="image" src="https://github.com/user-attachments/assets/0b28d7e6-fd7d-4aba-89d0-2113c273fd99" />


````
# Command to directly load spice file for simulation to ngspice
ngspice sky130_inv.spice

# Now that we have entered ngspice with the simulation spice file loaded we just have to load the plot
plot y vs time a
`````

<img width="1183" height="643" alt="image" src="https://github.com/user-attachments/assets/0b8d0da4-4524-43d3-9e97-248bfa96ecd2" />





























     








