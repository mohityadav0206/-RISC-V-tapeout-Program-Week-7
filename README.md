# -RISC-V-tapeout-Program-Week-7
Moving forward to the Journey of RISC-V, this week complete physical design of Babyvsdbabysoc is done using OpenROAD.
### VSDBabySoC
VSDBabySoC is a small yet powerful RISCV-based SoC. The main purpose of designing such a small SoC is to test three open-source IP cores together for the first time and calibrate the analog part of it. VSDBabySoC contains one RVMYTH microprocessor, an 8x-PLL to generate a stable clock, and a 10-bit DAC to communicate with other analog devices.
- An SoC is a single-die chip that has some different IP cores on it. These IPs could vary from microprocessors (completely digital) to 5G broadband modems (completely analog).
- RVMYTH core is a simple RISCV-based CPU.
- A phase-locked loop or PLL is a control system that generates an output signal whose phase is related to the phase of an input signal. PLLs are widely used for synchronization purposes, including clock generation and distribution.
- DAC is a system that converts a digital signal into an analog signal. DACs are widely used in modern communication systems enabling the generation of digitally-defined transmission signals. As a result, high-speed DACs are used for mobile communications and ultra-high-speed DACs are employed in optical communications systems.

<img width="2270" height="1260" alt="image" src="https://github.com/user-attachments/assets/8c718dee-919b-424d-9f3e-5f5e54f56cb1" />
### OpenROAD
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts

![505649862-56dd2d00-0e82-43c6-afdb-0d47c318d398](https://github.com/user-attachments/assets/f3abc1c3-9082-4b0c-8cb2-d9428712c425)

cd OpenROAD-flow-scripts

sudo ./setup.sh

![505649547-24911343-2ccc-4956-bb96-0e5e781b44e7](https://github.com/user-attachments/assets/4e5c26b9-b919-4d57-8c16-d740ca0eb092)
<img width="838" height="533" alt="unnamed" src="https://github.com/user-attachments/assets/f279b024-1dc9-4e3d-82d3-ef8b055fbe09" />

./build_openroad.sh --local
<img width="809" height="506" alt="Screenshot 2025-11-14 203420" src="https://github.com/user-attachments/assets/4e2e24cf-2c86-415b-ac8a-da181e6f9e55" />

- Verify Installation
source ./env.sh

yosys -help
![505649610-1864b4e1-0368-4d82-a482-1eab2cff6570](https://github.com/user-attachments/assets/0e102440-0e49-4927-9785-0297c89ce058)

openroad -help
<img width="817" height="380" alt="unnamed (6)" src="https://github.com/user-attachments/assets/3680ffc6-9179-4adf-b06b-73bfb47f9bd9" />

cd flow

make
<img width="809" height="506" alt="Screenshot 2025-11-14 203420" src="https://github.com/user-attachments/assets/98459913-9e0e-4541-8fc6-d2b5a5fefc2f" />

make gui_final
<img width="1306" height="709" alt="unnamed" src="https://github.com/user-attachments/assets/d1ec4c50-bfd0-4159-b201-e76f240c6e86" />
 
### OpenROAD Directory Structure
-OpenROAD-flow-scripts             
 
 docker           -> It has Docker based installation, run scripts and all saved here
 
 docs             -> Documentation for OpenROAD or its flow scripts.  
 
 flow             -> Files related to run RTL to GDS flow  
 
 jenkins          -> It contains the regression test designed for each build update
 
 tools            -> It contains all the required tools to run RTL to GDS flow
 
 etc              -> Has the dependency installer script and other things
 
 setup_env.sh     -> Its the source file to source all our OpenROAD rules to run the RTL to GDS 

- Flow

 flow           
 
 design           -> It has built-in examples from RTL to GDS flow across different technology nodes

 makefile         -> The automated flow runs through makefile setup
 
 platform         -> It has different technology note libraries, lef files, GDS etc 
 
 tutorials        

 util            
 
 scripts             
<img width="814" height="582" alt="505648675-34df49e2-32d3-4d05-b549-b4e9d658f5bc" src="https://github.com/user-attachments/assets/7ec7be22-7758-4e4b-a19d-cd7d611be5d1" />

### Physical Design Steps for VSDBabySoC
- Create a directory vsdbabysoc inside OpenROAD-flow-scripts/flow/designs/sky130hd
- Now create a directory vsdbabysoc inside OpenROAD-flow-scripts/flow/designs/src and include all the verilog files here.
- Copy the folders gds, include, lef and lib from the VSDBabySoC folder in your system into this directory.
          -The gds folder would contain the files avsddac.gds and avsdpll.gds
          -The include folder would contain the files sandpiper.vh, sandpiper_gen.vh, sp_default.vh and sp_verilog.vh
          -The gds folder would contain the files avsddac.lef and avsdpll.lef
          -The lib folder would contain the files avsddac.lib and avsdpll.lib
-Now copy the constraints file(vsdbabysoc_synthesis.sdc) from the VSDBabySoC folder in your system into this directory.
- Also, copy the files(macro.cfg and pin_order.cfg) from the VSDBabySoC folder in your system into this directory.
- Create a config.mk file whose contents are shown below:

                          export DESIGN_NICKNAME = vsdbabysoc
                          export DESIGN_NAME = vsdbabysoc
                          export PLATFORM    = sky130hd

         # export VERILOG_FILES_BLACKBOX = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/IPs/*.v
         # export VERILOG_FILES = $(sort $(wildcard $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/*.v))
         # Explicitly list the Verilog files for synthesis
export VERILOG_FILES = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/vsdbabysoc.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/rvmyth.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/clk_gate.v

export SDC_FILE      = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/vsdbabysoc_synthesis.sdc

export vsdbabysoc_DIR = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)

export VERILOG_INCLUDE_DIRS = $(wildcard $(vsdbabysoc_DIR)/include/)
    # export SDC_FILE      = $(wildcard $(vsdbabysoc_DIR)/sdc/*.sdc)
    export ADDITIONAL_GDS  = $(wildcard $(vsdbabysoc_DIR)/gds/*.gds.gz)
    export ADDITIONAL_LEFS  = $(wildcard $(vsdbabysoc_DIR)/lef/*.lef)
    export ADDITIONAL_LIBS = $(wildcard $(vsdbabysoc_DIR)/lib/*.lib)
    # export PDN_TCL = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/pdn.tcl

    # Clock Configuration (vsdbabysoc specific)
    # export CLOCK_PERIOD = 20.0
    export CLOCK_PORT = CLK
    export CLOCK_NET = $(CLOCK_PORT)

    # Floorplanning Configuration (vsdbabysoc specific)
    export FP_PIN_ORDER_CFG = $(wildcard $(DESIGN_DIR)/pin_order.cfg)
    # export FP_SIZING = absolute

    export DIE_AREA   = 0 0 1600 1600
    export CORE_AREA  = 20 20 1590 1590

    # Placement Configuration (vsdbabysoc specific)
export MACRO_PLACEMENT_CFG = $(wildcard $(DESIGN_DIR)/macro.cfg)
export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600: -exclude right:* -exclude top:* -exclude bottom:*
    # export MACRO_PLACEMENT = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/macro_placement.cfg

    export TNS_END_PERCENT = 100
    export REMOVE_ABC_BUFFERS = 1

    # Magic Tool Configuration
    export MAGIC_ZEROIZE_ORIGIN = 0
    export MAGIC_EXT_USE_GDS = 1

    # CTS tuning
    export CTS_BUF_DISTANCE = 600
    export SKIP_GATE_CLONING = 1

    # export CORE_UTILIZATION=0.1  # Reduce this value to allow more whitespace for routing.
<img width="1418" height="738" alt="unnamed" src="https://github.com/user-attachments/assets/cae0c175-a617-4bc6-92db-51e39ea6a4ad" />
<img width="1418" height="447" alt="unnamed" src="https://github.com/user-attachments/assets/def2d4c2-a70d-47ed-9604-3cea5cb70964" />

Now run the following commands:
           cd OpenROAD-flow-scripts
           source env.sh
           cd flow

Command for Synthesis

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
<img width="804" height="240" alt="unnamed (3)" src="https://github.com/user-attachments/assets/83c40609-da6e-4f39-bcb7-c0e37058dddd" />

This creates Synthesis Netlist

- Synthesis log
<img width="1920" height="923" alt="unnamed" src="https://github.com/user-attachments/assets/f0de241a-f725-4824-8744-2b2aea2aee55" />

- Synthesis Check
<img width="1130" height="153" alt="unnamed" src="https://github.com/user-attachments/assets/b5b5a8a8-598b-4df1-8ff3-492dd7283eb8" />

- Synthesis Stat
<img width="552" height="830" alt="unnamed" src="https://github.com/user-attachments/assets/fc368099-71f8-41e6-89d4-ed2918dcdbf7" />
<img width="613" height="454" alt="unnamed" src="https://github.com/user-attachments/assets/b309ec4a-c7b9-4cf7-b644-e85ef41480ca" />

1. BabySoC Floorplanning
Floorplanning is the process of dividing the chip area and deciding the relative locations of major functional blocks (like ALU, memory, I/O, etc.) on the chip. It involves the chip planning where the die size, macros and standard cells rows are consider without actually allocating the area. Its objective is to minimize the interconnect length and to avoid congestion.

In floorplanning the macros and the IO and power planning takes place. It also Specifies the die area and core area

Command for Floorplan:

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
<img width="832" height="606" alt="unnamed (2)" src="https://github.com/user-attachments/assets/4d1ff25b-2f32-4e0a-88ce-f1ac32495985" />
<img width="1851" height="837" alt="unnamed (1)" src="https://github.com/user-attachments/assets/f4a58035-59a5-488b-b699-4898b0bdb69a" />

2. Placement
Placement is the process of assigning exact physical locations to standard cells (logic gates, flip-flops, etc.) within the floorplanned blocks.

Command for Placement:

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
<img width="1851" height="783" alt="unnamed (5)" src="https://github.com/user-attachments/assets/2258d8a1-93d6-4a17-9e88-f90cad695259" />


make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
<img width="1841" height="806" alt="unnamed" src="https://github.com/user-attachments/assets/64039fd0-50ad-4f44-9753-80e17a945d05" />
<img width="1446" height="734" alt="unnamed" src="https://github.com/user-attachments/assets/1984b48a-509f-4628-bdfb-d2a3ac2d7a04" />

Heatmap
<img width="1456" height="806" alt="unnamed" src="https://github.com/user-attachments/assets/1bab94fa-7919-4f48-89b5-eb7d8d92739f" />
<img width="1456" height="806" alt="unnamed" src="https://github.com/user-attachments/assets/68045c81-4b41-4e9f-b50f-cc371e844300" />

3. Clock Tree Synthesis
CTS is used to create the clock distribution network. Its goal is to deliver the clock to all sequential elements with the minimum skew. Ideally the skew is 0. The tree is usually in the shape of H or X. 

Command for CTS:

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
<img width="1342" height="511" alt="unnamed (7)" src="https://github.com/user-attachments/assets/a656994d-9ef9-4314-95ec-7b2fc247a3a2" />


make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
<img width="1291" height="593" alt="unnamed" src="https://github.com/user-attachments/assets/241d1c78-3d6c-4835-82a6-fde51dd04659" />

CTS Final Report:
<img width="1123" height="831" alt="unnamed" src="https://github.com/user-attachments/assets/d3af3c8b-9913-47dd-8d02-adcf10da7beb" />
<img width="1054" height="723" alt="unnamed" src="https://github.com/user-attachments/assets/43f4c1cf-3e27-43c6-a7a5-df6e5ff9338c" />
<img width="845" height="816" alt="unnamed" src="https://github.com/user-attachments/assets/a2335c7b-c5f8-4a6a-8519-8141fe30adda" />
<img width="845" height="816" alt="unnamed" src="https://github.com/user-attachments/assets/7dcb2d34-ff29-48ce-a07b-e504709c6f10" />
<img width="845" height="816" alt="unnamed" src="https://github.com/user-attachments/assets/afd28103-8341-45d8-8791-fdc173cf8c0e" />

4. Routing
Routing is the process of interconnecting the components and the available metal layers. Metal tracks forms a routing grid. It is based on divide and conquer approach. In global routing the routing grids are generatedwhile in detailed routing the actual wiring is takes place.

Command for Route:

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
<img width="1164" height="204" alt="unnamed (8)" src="https://github.com/user-attachments/assets/da58a867-d0b4-4ab8-b06c-6cfc61ae79b1" />

make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_route
<img width="1463" height="802" alt="unnamed" src="https://github.com/user-attachments/assets/bcc811b2-6f01-4dd6-9601-c1fd5c513855" />
<img width="1463" height="802" alt="unnamed" src="https://github.com/user-attachments/assets/4047c9bb-1334-4320-9a41-6b7bebe2ae0f" />


5. Post-Route SPEF Generation
Post-route SPEF generation is the process of extracting the final parasitic RC values (resistance and capacitance) from a fully routed layout and writing them into a SPEF (Standard Parasitic Exchange Format) file.
This happens after place-and-route is complete, when wire geometry is fixed.
SPEF (IEEE 1481 standard) is a file format used to represent parasitic extraction data of a chip’s interconnects:

R → resistance

C → capacitance (to ground + coupling)

L (optional) → inductance

Net topology

It is done to provide accurate interconnect parasitics for sign-off static timing analysis (STA). Also, Post-route timing uses real RC values instead of rough estimates.

- Extract Parasitics
extract_parasitics
  [-ext_model_file filename]      pointer to the Extraction Rules file

  [-corner_cnt count]             process corner count

  [-max_res ohms]                 combine resistors in series up to
                                  <max_res> value in OHMS

  [-coupling_threshold fF]        coupling below the threshold is grounded

  [-lef_res]                      use per-unit RC defined in LEF

  [-cc_model track]               calculate coupling within
                                  <cc_model> distance

  [-context_depth depth]          calculate upper/lower coupling from
                                  <depth> level away

  [-no_merge_via_res]             separate via resistance

The extract_parasitics command performs parasitic extraction based on the routed design. If there is no routed design information, then no parasitics are returned. Use ext_model_file to specify the Extraction Rules file used for the extraction.

### Acknowledgement
I'm very grateful to VSD team and kunal ghosh for this RISC-V tapeout chip program.
