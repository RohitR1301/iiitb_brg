# iiitb_brg- Baud Rate Generator
### 1) Introduction
The Baud rate generator is nothing but a frequency divider. It helps two devices in communicating with each other. When data is sent from transmitter to receiver then the data at the receiver side is sampled at a particular baud rate. If there is a high mismatch between the baud rate of transmitter and receiver then the sampling will not happen at the centre of the bit period and there will be an offset. Due to the offset, there will be information that will be missed and communication will not occur efficiently. For example, if transmitter transmit at a particular rate and the receiving device only expects half of that data rate then half of the information will be lost.

<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/182206317-3a1935dd-f6a0-4392-a50a-39bada827bf4.png" width="500"/>
</p>
<p align="center">
  Fig 1. Baud Rate Generator Block Diagram
</p>

### Working 
The block diagram of the baud rate generator is given in the fig 1. The input to the system is Clock, Baud rate select line and reset. The output is the clock with the frequency corresponding to the baud rate. 
The system works on positive edge triggered clock and uses select line to decide the clock frequency for the corresponding selected Baud Rate to be give as output. A counter value is calculated based on the system clock and 115200bps baud rate which is DIV1. For 115200 bps, when the counter reaches DIV1 the clock pulse is negated. For 38400bps, the clock pulse is negated when the counter reaches DIV1 twice. For 19200bps, the clock pusle is negated when the counter reaches DIV1 5 times. For 9600bps, the clock pulse is negated when the counter reaches DIV1 11 times.<br>
The clock output is set to 0 when the reset is set to high.
### 2) Functional Simulation 
### About Iverilog and GTKWave
**Icarus Verilog** is a Verilog simulation and synthesis tool. It operates as a compiler, compiling source code written in Verilog (IEEE-1364) into some target format.<br>
**GTKWave** is a VCD waveform viewer based on the GTK library. This viewer support VCD and LXT formats for signal dumps. 
### Installing Software 
```
sudo apt-get install git 
sudo apt-get install iverilog 
sudo apt-get install gtkwave 
```
### Executing the project
```
git clone https://github.com/RohitR1301/iiitb_BRG
cd iiitb_brg
iverilog iiitb_brg.v iiitb_brg_tb.v 
gtkwave iiitb_brg_out.vcd
```
When the project is run, we get the following output which is shown with the help of GTKwave.  
<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/182586115-bab95563-73cf-4203-94e1-53e1104832bc.jpeg" width="1000"/><br>
  Fig 2. Simulation result for different Baud Rate
</p>
<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/182586640-c925798d-e41b-4271-b4c9-bf2551328e04.jpeg" width="1000"/><br>
  Fig 3. Simulation result when reset becomes 1
</p>

### 3) Synthesis 
The software used to run gate level simulation is yosys.
### Yosys
**Yosys** is a framework for RTL synthesis tools. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains.
Yosys can be adapted to perform any synthesis job by combining the existing passes (algorithms) using synthesis scripts and adding additional passes as needed by extending the yosys C++ code base.
<br> <br><b>Installing Yosys</b>
```
git clone https://github.com/YosysHQ/yosys.git
make
sudo make install make test
```
Make a file named ```yosys_run.sh``` and copy the below commands to run synthesis is yosys.
```
read_verilog iiitb_brg.v
synth -top iiitb_brg
dfflibmap -liberty /home/rohitr/iverilog/BaudRateGen.v/iiitb_brg/Lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty /home/rohitr/iverilog/BaudRateGen.v/iiitb_brg/Lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime,{D};strash;dch,-f;map,-M,1,{D}
clean
flatten
write_verilog -noattr iiitb_brg_synth.v
stat
show
```
Then open the terminal in iiitb_brg and run the following command
```
yosys -s yosys_run.sh
```
On running the above command we get the netlist and the also the statistics about number of gates that are used.
<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/185080537-819af9d7-f095-4f2a-a873-ffe07e1e2e7c.png" width="300"/><br>
  Fig 4. Statistics 
</p>

### 4) Gate Level Simulation (GLS)
GLS stands for Gate level Simulation. when we make an RTL design we test it with the help of test bench, which applies some stimulus. The output of that stimulus is checked for the desired functionality. After synthesis we need to again check if the functionality is maintained, for that we perform the GLS. We put the netlist under test and use the same test bench that we did for RTL design to check the functionality. GLS also ensures the timing of the design. 
<br>We run the below command for GLS
```
iverilog -DFUNCTIONAL -DUNIT_DELAY=#1 ../iiitb_brg/Verilog_Model/primitives.v ../iiitb_brg/Verilog_Model/sky130_fd_sc_hd.v ../iiitb_brg/iiitb_brg_net.v ../iiitb_brg/iiitb_brg_tb.v
./a.out
gtkwave iiitb_brg_out.vcd
```
After running the above command we get the following output
<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/185083660-f6e7b8b8-947c-4edc-b061-a2a7db144081.png" width="1000"/><br>
  Fig 5. GLS Waveform
</p>

### 5) Layout
<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/187413490-05ae8b94-ad97-4898-81a1-901231036450.png"width="1000"/><br>
  Fig 6. Layout
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/110080106/187411328-1a056207-6ef7-4333-a7f1-2a6a5100c4f1.png"width="1000"/><br>
  Fig 7. Inverter added to layout
</p>



### Author
- Rohit Raj
### Acknowledgement 
Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.
### Contact Information
- Rohit Raj, M.Tech VLSI, Batch 2022-2024, International Institute Of Information Technology, Bangalore(IIIT B), rohitraj116@gmail.com
