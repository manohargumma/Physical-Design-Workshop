# Physical-Design-Workshop


![Workshop](https://img.shields.io/badge/Workshop-Physical--Design-blueviolet)
![Domain-VLSI](https://img.shields.io/badge/Domain-VLSI-green)
![Tool-OpenROAD](https://img.shields.io/badge/Tool-OpenROAD-blue)
![Tool-OpenLane](https://img.shields.io/badge/Tool-OpenLane-darkgreen)
![PDK-Sky130](https://img.shields.io/badge/PDK-Sky130-orange)
![Platform-RISC_V_SoC](https://img.shields.io/badge/Platform-RISC--V_SoC-green)
![Container-Docker](https://img.shields.io/badge/Container-Docker-blue)
![OS-Ubuntu](https://img.shields.io/badge/OS-Ubuntu-orange)
![Community-OpenSource](https://img.shields.io/badge/Community-Open--Source-success)



### `OpenLane Flow Toolchain Overview`

The OpenLane flow utilizes a suite of open-source tools to efficiently transform RTL designs into manufacturable layouts. Each stage of the design process is supported by specialized tools, ensuring optimal performance and compliance with design rules.

#### RTL Synthesis, Technology Mapping, and Formal Verification
- **Tools Used**: 
  - **Yosys**: For RTL synthesis, converting high-level design into a gate-level netlist.
  - **ABC**: For technology mapping and formal verification, optimizing logic for specific technology nodes.

#### Static Timing Analysis
- **Tools Used**: 
  - **OpenSTA**: For static timing analysis, ensuring the design meets timing constraints.

#### Floor Planning
- **Tools Used**: 
  - **init_fp**: For initial floorplanning, defining the physical layout of the chip.
  - **ioPlacer**: For I/O placement, organizing input/output pins efficiently.
  - **pdn**: For power distribution network planning, ensuring robust power delivery.
  - **tapcell**: For tap cell insertion, maintaining well connections across the design.

#### Placement
- **Tools Used**: 
  - **RePLace**: For global placement, arranging standard cells within the floorplan.
  - **Resizer**: Optional tool for resizing cells to optimize area and performance.
  - **OpenPhySyn**: Formerly used for placement optimization.
  - **OpenDP**: For detailed placement, finalizing cell positions.

#### Clock Tree Synthesis
- **Tools Used**: 
  - **TritonCTS**: For clock tree synthesis, distributing clock signals uniformly.

#### Fill Insertion
- **Tools Used**: 
  - **OpenDP**: For filler placement, ensuring density requirements are met.

#### Routing
- **Tools Used**: 
  - **FastRoute or CU-GR**: Formerly used for global routing.
  - **TritonRoute**: For detailed routing, ensuring signal integrity and minimizing congestion.
  - **DR-CU**: Formerly used for detailed routing.

#### SPEF Extraction
- **Tools Used**: 
  - **OpenRCX**: For Standard Parasitic Exchange Format (SPEF) extraction, capturing parasitic effects.
  - **SPEF-Extractor**: Formerly used for SPEF extraction.

#### GDSII Streaming Out
- **Tools Used**: 
  - **Magic and KLayout**: For viewing and editing GDSII files, preparing for fabrication.

#### Design Rule Checking (DRC) Checks
- **Tools Used**: 
  - **Magic and KLayout**: For DRC checks, ensuring compliance with manufacturing rules.

#### Layout vs. Schematic (LVS) Check
- **Tools Used**: 
  - **Netgen**: For LVS checks, verifying the layout matches the schematic.

#### Antenna Checks
- **Tools Used**: 
  - **Magic**: For antenna checks, preventing damage during fabrication.







### OpenLANE Directory structure:

```text
├── OOpenLane             -> directory where the tool can be invoked (run docker first)
│   ├── designs          -> All designs must be extracted from this folder
│   │   │   ├── picorv32a -> Design used as case study for this workshop
│   |   |   ├── ...
|   |   ├── ...
├── pdks                 -> contains pdk related files 
│   ├── skywater-pdk     -> all Skywater 130nm PDKs
│   ├── open-pdks        -> contains scripts that makes the commerical PDK (which is normally just compatible to commercial tools) to also be compatible with the open-source EDA tool
│   ├── sky130A          -> pdk variant made especially compatible for open-source tools
│   │   │  ├── libs.ref  -> files specific to node process (timing lib, cell lef, tech lef) for example is `sky130_fd_sc_hd` (Sky130nm Foundry Standard Cell High Density)  
│   │   │  ├── libs.tech -> files specific for the tool (klayout,netgen,magic...) 
```



### `Step-by-Step OpenLANE Synthesis Flow Commands for picorv32a design`



#### Step 1: Launch the Docker container (OpenLANE interactive shell)
```shell
docker
```

#### Step 2: Once inside the Docker container, launch the OpenLANE interactive shell using:
```shell
./flow.tcl -interactive
```


#### Step 3: Run the OpenLANE Flow Commands from the Interactive Shell
```shell
# Load the required OpenLANE package for proper functionality
package require openlane 0.9

# Prep the design (creates necessary directory structure, config files, and copies RTL files)
prep -design picorv32a
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2019-28-30.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2019-28-38.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2019-28-43.png)

```shell
# Run synthesis step
run_synthesis
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2019-30-43.png)


#### Step 4: View the Yosys Synthesis Report
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2021-07-38.png)

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2021-07-45.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/609dcc0612bff50a90630a300227f188c61607da/images/Screenshot%20from%202025-10-30%2021-07-53.png)




### `Calculation of Flop Ratio and DFF %`
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/43f24eaf0abd539ec71242c0b43fa55d21ebfd96/images/Screenshot%20from%202025-10-30%2022-10-02.png)

To evaluate the sequential logic content in the synthesized design:

- **Total Cells** = 14596
- **DFF Cells** = 1613 (from `sky130_fd_sc_hd__dfxtp_2`)

#### ➤ Flop Ratio:

Flop Ratio = DFFs / Total Cells = 1613 / 14596 ≈ 0.11050972

#### ➤ DFF Percentage:

DFF % = (DFFs / Total Cells) × 100 = (1613 / 14296) × 100 ≈ 11.050972%
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/43f24eaf0abd539ec71242c0b43fa55d21ebfd96/images/Screenshot%20from%202025-10-30%2022-13-57.png)
###  Good floorplan vs bad floorplan and introduction to library cells



```shell
Utilisation Factor =  Area occupied by netlist
                     __________________________
                         Total area of core
                         

Aspect Ratio =  Height
               ________
                Width
```

**Pre-placed cells :** Pre-placed cells are essential functional blocks, such as memory, custom processors, and analog circuits, positioned manually in fixed locations. These blocks are crucial for the chip’s performance and remain fixed during placement and routing to preserve their functionality and layout integrity.

**Decoupling Capacitors :** Decoupling capacitors are placed near logic circuits to stabilize power supply voltages during transient events. Acting as local energy reserves, they help reduce voltage fluctuations, crosstalk, and electromagnetic interference (EMI), ensuring reliable power delivery to sensitive circuits.

**Power Planning:** A robust power planning strategy includes creating a power and ground mesh to distribute VDD and VSS evenly across the chip. This setup ensures stable power delivery, minimizes voltage drops, and improves overall efficiency. Multiple power and ground points reduce the risk of instability and voltage drop issues, supporting the design’s power needs effectively.

**Pin Placement:** Pin placement (I/O planning) is crucial for functionality and reliability. Strategic pin assignment minimizes signal degradation, preserves data integrity, and helps manage heat dissipation. Proper positioning of power and ground pins supports thermal management and enhances signal strength, contributing to overall system stability and manufacturability.


**Explanation of Key Parameters**

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/0316f1e22bbe73f0fcb6fd1566d4c72af63873b1/images/Screenshot%20from%202025-10-30%2023-55-31.png)
> config.tcl — Design Configuration File


#### Step 5: Floorplaning using OpenLANE & view in Magic

```shell
run_floorplan
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/6a99f4af4366326d6d13c53980f368d50c861922/images/Screenshot%20from%202025-10-31%2019-21-45.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/0316f1e22bbe73f0fcb6fd1566d4c72af63873b1/images/Screenshot%20from%202025-10-31%2000-44-15.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/0316f1e22bbe73f0fcb6fd1566d4c72af63873b1/images/Screenshot%20from%202025-10-31%2000-46-23.png)


⚠️ **Potential Errors During Floorplanning**

When running:

```shell
run_floorplan
```
you may encounter errors, especially for macro-less designs like picorv32a. These errors are typically due to macro_placement steps within the OpenROAD flow that expect macros to be present in the design.

🧩 **Why the Error Occurs?**
The script or_basic_mp.tcl, which handles macro placement, assumes that the design contains macros (like SRAMs or IPs). If no such macros are present, OpenROAD may crash or throw errors during this step:
```shell
macro_placement -global_config glb.cfg
```
For designs without any macros, this command should be skipped to avoid unnecessary failures.

🛠️ **How to Fix It?**
You need to modify the or_basic_mp.tcl script to check whether the design contains macros, and only run macro_placement if macros are found.

✅ This change ensures macro placement is only performed when necessary.

Replace the contents of:
```shell
/openLANE_flow/scripts/openroad/or_basic_mp.tcl
```

#### Step 6: Viewing the Floorplan DEF in Magic

To visually inspect the floorplan (DEF) file in the Magic layout editor, use the following command in your terminal from the floorplan results directory:

```shell
magic -T Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/0316f1e22bbe73f0fcb6fd1566d4c72af63873b1/images/Screenshot%20from%202025-10-31%2000-46-28.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/0316f1e22bbe73f0fcb6fd1566d4c72af63873b1/images/Screenshot%20from%202025-10-31%2001-08-32.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ddce85b06304ecf2a5ce66e1ac338c87f99aa263/images/Screenshot%20from%202025-10-31%2001-16-23.png)

- Press s and v to fit the layout properly on the screen.

- Move the cursor onto a particular object and then press s.

- Go to the tkcon main window.

- Type the command shown in the screenshot (or observe the details displayed).

- The tkcon window will show the details of the selected object.

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ddce85b06304ecf2a5ce66e1ac338c87f99aa263/images/Screenshot%20from%202025-10-31%2001-19-12.png)



#### Step 7: Run Placement using OpenLANE

After successful floorplanning, the next major step in the digital backend flow is cell placement. This stage involves arranging the standard cells within the defined core area while minimizing congestion, wire length, and optimizing performance metrics like timing and power.

To initiate placement:
```shell
run_placement
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ea7869831e0f395a507fc7b91e3b585e9c59ecda/images/Screenshot%20from%202025-10-31%2009-12-50.png)



#### Step 8: Viewing the Placement DEF in Magic
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ea7869831e0f395a507fc7b91e3b585e9c59ecda/images/Screenshot%20from%202025-10-31%2009-27-43.png)
```shell
magic -T  /home/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ea7869831e0f395a507fc7b91e3b585e9c59ecda/images/Screenshot%20from%202025-10-31%2009-28-07.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/ea7869831e0f395a507fc7b91e3b585e9c59ecda/images/Screenshot%20from%202025-10-31%2009-29-24.png)


### Viewing the Placement PNG Output

After running the placement step (`run_placement`), a visualization of the placed standard cells is automatically generated.

📁 You can find this file in the placement results directory:

To open this PNG file from the terminal (inside the appropriate directory), use:

```bash
xdg-open picorv32a.placement.def.png
```
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/6a99f4af4366326d6d13c53980f368d50c861922/images/picorv32a.placement.def.png)

# Design library cell using Magic Layout 


#### 1. Clone custom inverter standard cell design from github repository

```bash
# Change directory to openlane
cd Desktop/work/tools/openlane_working_dir/openlane

# Clone the repository with custom inverter design
git clone https://github.com/nickson-jose/vsdstdcelldesign

# Change into repository directory
cd vsdstdcelldesign

# Copy magic tech file to the repo directory for easy access
cp /home/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .

# Check contents whether everything is present
ls

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of commands run

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2011-46-49.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2011-55-40.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2011-59-01.png)


NMOS and PMOS identified

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2014-07-00.png)
![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2014-07-17.png)

Output Y connectivity to PMOS and NMOS drain verified

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2014-11-51.png)


#### 3. Spice extraction of inverter in magic.

Commands for spice extraction of the custom inverter layout to be used in tkcon window of magic

```tcl
# Check current directory
pwd

# Extraction command to extract to .ext format
extract all

# Before converting ext to spice this command enable the parasitic extraction also
ext2spice cthresh 0 rthresh 0

# Converting to ext to spice
ext2spice
```

Screenshot of tkcon window after running above commands

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2014-51-52.png)

Screenshot of created spice file

![image](https://github.com/manohargumma/Physical-Design-Workshop/blob/33609411384f46bf305e193f1e5b57728d46bf8f/images/Screenshot%20from%202025-10-31%2014-51-32.png)
