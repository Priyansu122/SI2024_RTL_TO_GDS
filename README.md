# SI2024_RTL_TO_GDS
This page is for installation of tools needed for RTL to GDS flow and execution of the flow.
## Table of content
- [Installation](#Installation_of_tools)
- [RTL to GDS flow](#Flow)
- [GDS file of DEMO design](#RESULTS_OF_ASIC_FLOW)
## Installation_of_tools
Tools needed to be installed for executing the flow are </br>
1. Iverilog --> For Verification
2. Gtkwave  --> For Analysis
3. Yosys    --> For Synthesis
4. OpenSTA  --> For Static time analysis
5. OpenRoad --> For Physical design

### Iverilog
Step1 : Install the dependancies by following the commands below
```bash
sudo apt-get update
sudo apt-get install gperf
sudo apt-get install autoconf
sudo apt-get install gcc g++
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install make
```
Step2 : Install Iverilog using following commands

```bash
git clone https://github.com/steveicarus/iverilog.git
ls
cd iverilog
sh autoconf.sh
./configure
make
sudo make install
```
Step3 : Check if installed or not by invoking the tool using ```iverilog```

### Gtkwave   
-  GTKWave is an analysis tool used to perform debugging on Verilog or VHDL simulation
   models.
-  Here, we will be using one of the dump file formats, Value Change Dump (VCD).
-  To install gtkwave, execute the following commands

 ```bash
 cd
 sudo apt install gtkwave
```

### Yosys  
- Install the prerequisites
```bash
 sudo apt−get install build−essential clang bison flex \
 l i breadline −dev gawk tcl−dev libffi −dev git \
 graphviz xdot pkg−config python3 libboost−system−dev \
 l ibboost−python−dev libboost−filesystem−dev zlib1g−dev
```
- Then follow the steps below one by one,
```bash
git clone https://github .com/YosysHQ/yosys . git
ls
cd yosys
make
sudo make install
```
- This is the end of installation process.
- Check the tool is installed or not by invoking it -->  ./yosys
- If tool lunches the command prompt changes to --> yosys>
  
### OpenRoad

OpenROAD is a comprehensive tool for chip physical design that transforms a synthesized Verilog netlist into a routed layout.

The OpenROAD Project has two releases:

- Application [(github)](https://github.com/The-OpenROAD-Project/OpenROAD) [(docs)](https://openroad.readthedocs.io/en/latest/main/README.html): The application is a standalone binary for digital place and route that can be used by any other RTL-GDSII flow controller.
  
- Flow [(github)](https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts) [(docs)](https://openroad-flow-scripts.readthedocs.io/en/latest/): This is the native OpenROAD flow that consists of a set of integrated scripts for an autonomous RTL-GDSII flow using OpenROAD and other open-source tools.
  
- We will be following the application github page becuase it allows us to divide the whole script to subscripts like script for floorplanning, script for detailed routing hence that will give us a better idea on flow and will help us to visualize.
  
- You can explore flow github release for Automated RTL to GDS flow

- Follow the following steps that is execute following commands one by one in the WSL terminal

```bash
  git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD.git
  cd OpenROAD
  sudo ./etc/DependencyInstaller.sh
  mkdir build
  cd build
  cmake
  make
  sudo make install
```
- Verify the installation by invoking the tool using the following command
  
```bash
  openroad
```
## Flow  
Follow the steps given below for understanding the flow of RTL to GDS, 

- Clone the this page using git clone command.      --> ```https://github.com/Priyansu122/SI2024_RTL_TO_GDS.git```
- Enter inside the repository.                      --> ```cd SI2024_RTL_TO_GDS/```
- Unzip the file then cd into the counter folder.   --> ```unzip ASIC_counter.zip```
- Enter inside the counter folder                   --> ```cd counter/```
- Then next we will be starting with the flow

### Functional Verification and Analysis using Iverilog and Gtkwave  
- Verify the given design and testbench --> ```iverilog -o counter counter.v tb_counter.v```
- Analysis using Gtkwave --> </br>
```bash
vvp counter
gtkwave test.vcd
```
### Synthesis using Yosys and Verification after synthesis

- Synthesis required basically 2 files i.e design(.v file), Technology library(.lib file).
- Technology library is basically the resources provided by the fabrication house for the designers.
- Synthesis of a design consist of several commands that is there in synthesis.tcl
    
- Launch the yosys tool  --> ```yosys```
- You can individually run the commands listed above. I am using a tcl file.</br>
```bash
 yosys> script synthesis.tcl
 yosys> show
```
- synth.v is the netlist created by yosys tool.
- You can observe that the modules used in synth.v is from the technology library.
- show command will show the circuit of the verilog code you have written like how D flipflop is used instead of your always block in HDL you can observe that part here.
- Again you can check the functionality of the synthesized code using iverilog using following command.
  
```bash
iverilog -o counter synth.v tb_counter.v formal_pdk.v
vvp counter
gtkwave test.vcd
```
- formal_pdk.v is the file which describes the functionality of the modules present in the technology library.</br>
- You can find this in the location "/sky130hd/work_around_yosys"

### Static time Analysis  

- You can use this to findout setup slack and hold slack if both the slacks comes out to be positive in the report then the STA test is passed.
- You need the following files for the sta analysis
  
   - synthesized nestlist -> synth.v
   - constrain file -> top.sdc
- In constrain file you can metion the design constrain like clock speed, input delay, output delay etc.
- You can creat a tcl script and use that to run set of commands the commands for the static time analysis. I have named that script as sta.tcl.
  
- Lunch the sta tool --> ```sta```
- Run the script to execute static time analysis --> ```source test.tcl```

### Physical design using OpenRoad
- OpenRoad consist of set of scripts integrated together to execute the physical design which has multiple steps like
  
  - Floorplan
  - Power distribution network
  - global placement
  - detailed placement
  - clock tree synthesis
  - Routing
  
- You can invoke the tool and run the script using command ```openroad -gui physicalDesign.tcl```
- Here physicalDesign.tcl is the script that is used for physical design.
- In the physicalDesign.tcl there are some subscripts included one of them is flow.tcl but i have also divided the script into various subscripts for floorplanning then power distribution etc.
- You can comment flow.tcl and uncomment those files one by one to visualize each steps as mentioned in the first point.
- Now you can see the layout file in the user interface of OpenRoad.

## RESULTS_OF_ASIC_FLOW
- This is the Layout file of the counter.
- Fundamentally this will be sent to fabrication house and the chip will comw back.
- With this ASIC flow is completed. 
![image](https://github.com/user-attachments/assets/181d27f4-3244-4b1a-9c5c-b01d716410d5)

  
  
