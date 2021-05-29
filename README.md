# ***_RTL-design-with-verilog-using-SKY130-Technology_***

***
<p align="center">
<img src="https://user-images.githubusercontent.com/54993262/119881189-a1ebc380-bf4a-11eb-9bdf-6cc93bbcf1bd.png" width="600" height="400">
</p>

***

# **_Table of Contents_**

* ## Introduction to Verilog RTL design and Synthesis

* ## Timing libs, hierarchical vs flat synthesis and efficient flop coding styles

* ## Combinational and sequential optmizations

* ## GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

* ## Optimization in synthesis


***

# **_Introduction to Verilog RTL design and synthesis_**
Here **_iverilog_** is the simulator used. Simulator basically looks for changes in input, upon which output is evaluated. If there are no changes in the input, output won't be evaluated. **_iverilog_** takes **_design_** and **_testbench_** as inputs. **_Design_** is the actual verilog or set of verilog codes which has the intended funtionality to meet with the required specifications. **_Testbench_** is a setup that one uses to apply a set of stimuli to check the functional working of the design file. There are no primary inputs or outputs to testbench. 

These changes in input and corresponding output values are dumped in a special format file called value change dump(.vcd) file. The _waveforms_ can be viewed by using the **_gtkwave_** command to the _vcd file_.  

The _SkyWater Open Source PDK_ is a collaboration between Google and SkyWater Technology Foundry to provide a fully open source Process Design Kit and related resources, which can be used to create manufacturable designs at SkyWater's facility.

 

## _Implementing a Mux 2x1_
First thing is to clone https://github.com/kunalg123/vsdflow.git and the following codes are to be typed.

```
    iverilog good_mux.v tb_good_mux.v
    ./a.out
    gtkwave tb_good_mux.vcd
```
Verilog Code for Mux 2x1
![good_muxv](https://user-images.githubusercontent.com/54993262/120067755-03bb4300-c09b-11eb-9509-60ef05055bd5.JPG)
Test Bench for Mux 2x1
![tb_good_mux](https://user-images.githubusercontent.com/54993262/120067768-0fa70500-c09b-11eb-9472-50a334237857.JPG)
Output Waveforms
![gtk_good_mux](https://user-images.githubusercontent.com/54993262/120067779-1b92c700-c09b-11eb-81f8-c964f51609e8.JPG)


## _Sythesis using Yosys_
Yosys is a framework for Verilog RTL synthesis. It is a currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains. Yosys can be invoked by simply typing yosys on the command prompt.
![yosys](https://user-images.githubusercontent.com/54993262/120067982-00748700-c09c-11eb-867c-1271acfc1af5.JPG)

```
      read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
      read_verilog good_mux.v
      synth -top good_mux
      abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
      show
```
The output can be seen below. The final sysnthesized netlist shows the _2:1 multiplexer_ RTL is translated to a gate level representation.
![good_mux_op_yosys](https://user-images.githubusercontent.com/54993262/120068133-c5bf1e80-c09c-11eb-8390-40ed07650a5c.JPG)

# Timing libs, hierarchical vs flat synthesis and efficient flop coding styles


















