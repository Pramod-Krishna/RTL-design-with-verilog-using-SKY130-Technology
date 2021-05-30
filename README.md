# ***_RTL-design-with-verilog-using-SKY130-Technology_***

***
<p align="center">
<img src="https://user-images.githubusercontent.com/54993262/119881189-a1ebc380-bf4a-11eb-9bdf-6cc93bbcf1bd.png" width="600" height="400">
</p>

***

# **_Table of Contents_**

* ## [Introduction to Verilog RTL design and Synthesis](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#introduction-to-verilog-rtl-design-and-synthesis-1)

* ## [Timing libs, hierarchical vs flat synthesis and efficient flop coding styles](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#timing-libs-hierarchical-vs-flat-synthesis-and-efficient-flop-coding-styles-1)

* ## [Combinational and sequential optmizations](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#combinational-and-sequential-optmizations-1)

* ## [GLS, blocking vs non-blocking and Synthesis-Simulation mismatch](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#gls-blocking-vs-non-blocking-and-synthesis-simulation-mismatch-1)

* ## [Optimization in synthesis](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#optimization-in-synthesis-1)
* ## [Acknowledgement](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#acknowledgement)
* ## [Reference](https://github.com/Pramod-Krishna/RTL-design-with-verilog-using-SKY130-Technology#references)


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

***

# _Timing libs, hierarchical vs flat synthesis and efficient flop coding styles_

To see what the .lib file contains:
``` 
      vim ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
```
This is the snapshot of the library.
![library](https://user-images.githubusercontent.com/54993262/120071322-60732980-c0ac-11eb-9e2f-a13b70f633f3.JPG)
The first line indicates the name of the library. **_tt_** represents the speed, the libraries can be slow, typical and fast. **__025C__** indicates the temperature and **__1v80__** the volatge.

We can also observe the area and power consumption of each cell in these libraries. 
Below are the three types of __2 input and gates__.
![and2_0](https://user-images.githubusercontent.com/54993262/120076152-c74f0d80-c0c1-11eb-90f2-efc5ef27f98e.JPG)
![and2_2](https://user-images.githubusercontent.com/54993262/120076154-c9b16780-c0c1-11eb-9e8d-576ad7a30a43.JPG)
![and2_4](https://user-images.githubusercontent.com/54993262/120076157-cc13c180-c0c1-11eb-9382-ad1cf6328864.JPG)

As seen, __and2_4__ has the least delay (fastest) but consumes more area, where as __and2_0__ has highest delay but takes less area.

*** 
## Hierarchical vs Flat Synthesis
This can be demonstrated by considering __multiple_modules.v__
![mult_mod](https://user-images.githubusercontent.com/54993262/120076437-26615200-c0c3-11eb-80ac-0f00c91193be.JPG)

Hierarchical sysnthesis is basically synthesizing using Yosys as shown previously. This type of sub_module level synthesis is known as Hierarchical Synthesis as the sub_modules are preserved in its hierarchy.
![mult_mod_op](https://user-images.githubusercontent.com/54993262/120076610-09794e80-c0c4-11eb-86a1-e74aa302da61.JPG)

```  
     write_verilog -noattr multiple_modules_hier.v
     !vim multiple_modules_hier.v
```
![mult_mod_hier](https://user-images.githubusercontent.com/54993262/120076955-a38dc680-c0c5-11eb-80f6-7fc831bbc987.JPG)
Here __OR__ gate is not implemented standardly as __NOR__ and inverter as stacking PMOS is bad. As PMOS has poor mobility, and to improve this we should design a wide cell. Thus it is implemented as __NAND__ gate with both inputs inverted. 

Inorder to obtain a gate based synthesized netlist file without preserving any of the sub_module hierarchy, we implement the Flat Synthesis technique using the flatten command. It can be written after right after the abc command.

```
     flatten
```
The output is seen as:
![m1](https://user-images.githubusercontent.com/54993262/120077296-6aeeec80-c0c7-11eb-840a-8d557124a0e3.JPG)

## Reasons for submodule level synthesis approach 
* When a design consists of multiple instances of the same module, we can use this and replicate the same for all the other instances of the same module and stitch it together to obtain the complete netlist file. This would save us some time and it is optimized.
* In case of massive designs, it can be split into submodules and independenlty synthezied. This is called divide and conquer approach.

***
## _Efficient Flip-flop coding styles and Optimizations_
Flipflops are memory elements. One important use is that they avoid glitch errors due to propagation delays between logic gates which cause instability in output. Let us look into D-flipflop implemented with async-reset, sync-reset and async set etc. Their verilog code can be viewed as follows:
![image](https://user-images.githubusercontent.com/54993262/120078360-cbccf380-c0cc-11eb-9eba-068f8c69586f.png)

The waveforms for _Asynchronous Reset_ are:
![image](https://user-images.githubusercontent.com/54993262/120079217-8dd1ce80-c0d0-11eb-9d52-32aa8b1e2860.png)

The netlist of _Asynchronous Reset_ synthesized by _Yosys_ is seen as:
![image](https://user-images.githubusercontent.com/54993262/120078464-4e55b300-c0cd-11eb-8323-8d0865ef005d.png)

Similarly output of _Asynchronous Set_ is:
![image](https://user-images.githubusercontent.com/54993262/120078919-4e56b280-c0cf-11eb-89bd-b423420b374f.png)

Output for _Synchronous Set_ is:
![image](https://user-images.githubusercontent.com/54993262/120078939-66c6cd00-c0cf-11eb-9995-28b5f4015c36.png)

***
# **Combinational and sequential optmizations**

## Combinational Logic Optimization
Combinational logic can be optimized using methods like Constant Propagation and Boolean Logic Optimization. Constant propagation is by _Direct Optimization_ and Boolean Optimization includes _K-Map_ and _Quine Mccluskey method_. 

_Optimization can be done while synthesis using the following line of code_
```
   opt_clean -purge
```

Let's take an example of a verilog module, _opt_check.v_
![image](https://user-images.githubusercontent.com/54993262/120081237-47816d00-c0da-11eb-8cbf-77d4be906b93.png)

The output is:
![image](https://user-images.githubusercontent.com/54993262/120081207-05f0c200-c0da-11eb-9d9f-ae66e4008a92.png)

## Sequential Optmizations
Sequential Optmizations can be optimized using some advanced methods like 
* Sequential Constant Propagation
* State Optimization --> Optimization of unused state
* Retiming --> Can make the design faster by transferring slack
* Sequential Logic Cloning (Floor Plan Aware Synthesis) --> Timing constraints are met
Let's look at the verilog module _dff_const.v_
![image](https://user-images.githubusercontent.com/54993262/120079492-b3aba300-c0d1-11eb-9d4c-b7c00163d86e.png)

The output _q_ is changed only at the _posedge_ of next clock pulse. Thus we would need a _flop_
![image](https://user-images.githubusercontent.com/54993262/120079870-9677d400-c0d3-11eb-9f91-8d4c9c5d09dc.png)

Before synthesizing one more command should be executed.
```
   dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![image](https://user-images.githubusercontent.com/54993262/120080035-a0e69d80-c0d4-11eb-9559-2c38b3b7445a.png)

Let us observe _dff_const2_.
![image](https://user-images.githubusercontent.com/54993262/120082132-d8f2de00-c0de-11eb-8eae-94cfea8990c0.png)

On the contrary, _q_ is always 1
![image](https://user-images.githubusercontent.com/54993262/120082098-b19c1100-c0de-11eb-9496-50ac403fcca6.png)
![image](https://user-images.githubusercontent.com/54993262/120082230-65050580-c0df-11eb-8885-11990a8a5cda.png)

***

# GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

* **_GLSstands_** stands for Gate Level Simulation.
* Running the Test Bench With Netlist as design under Test.
* Netlist is logically same as the RTL Code.
* Same testbench will align with the design.

Why GLS ? 
* Verify the LOgical Correctness of the design after synthesis.
* Ensuring the Timing of design is met (For this GLS needs to be run with delay annotation)

**_GLS using iverilog_**
We give the Netlist,Gate Level Verilog Models and Testbench to the iverilog. Netlist has all the standard cell instantiated and the meaning of standard cell is conveyed to the iverilog by Gate Level Verilog Models. Upon which it has the same flow giving the vcd file using which we can generate Waveform using GTKwave.

Reasons for Synthesis Simulation Mismatch 
* Missing Sensitivity List
* Blocking Vs Non-Blocking Assignments
* Non Standard verilog Coding

Let us look at an example, a verilog file ie. ternary_operator_mux.v
![Ternary_operator](https://user-images.githubusercontent.com/54993262/120113097-8ffe6080-c196-11eb-8d66-6eb4cd2fca61.JPG)
The waveforms with the _gtkwave_ command
![Ternary_operator_output](https://user-images.githubusercontent.com/54993262/120113110-9987c880-c196-11eb-81da-b28a84c0fa99.JPG)

The below snippet is the _yosys_ output
![Ternary_operator_output_yosys](https://user-images.githubusercontent.com/54993262/120113118-a1e00380-c196-11eb-936f-3f23ebeef806.JPG)

The _GLS_ output can be seen after synthesizing using _yosys_. 
![GLS_op_ternary_mux](https://user-images.githubusercontent.com/54993262/120113113-9e4c7c80-c196-11eb-9bf4-278eaa36c02d.JPG)

 


***

# Optimization in synthesis
Let'c consider the example of _If_ statements first. 
Verilog module: _incomp_if_. Since the _else_ condition is mentioned, it behaves like a _D_latch_.   
![image](https://user-images.githubusercontent.com/54993262/120099877-7ab81080-c15b-11eb-8231-4694c667ff41.png)
The simulated output is as below. Whenever select line _i0_ goes to 0, output _y_ is latching. If _i0_ is high, output follows _i1_.
![image](https://user-images.githubusercontent.com/54993262/120100012-19dd0800-c15c-11eb-8318-c083e7fadd77.png)

We can see during synthesis, it clearly infers a _D_latch_ 
![image](https://user-images.githubusercontent.com/54993262/120100159-d767fb00-c15c-11eb-947f-949c8f7dc8cc.png)
![image](https://user-images.githubusercontent.com/54993262/120100192-f8305080-c15c-11eb-8a97-7cbda6d463b1.png)

Verilog Module : _incomp_if2.v_
![image](https://user-images.githubusercontent.com/54993262/120100216-2150e100-c15d-11eb-9988-2b8425b75cb6.png)
Synthesis output: When _i0_ is high, output exactly follows _i1_. when both _i0_ and _i2_ are low, output is constant. 
![image](https://user-images.githubusercontent.com/54993262/120100260-78ef4c80-c15d-11eb-8af2-456546e4c02f.png)
Simulation output:
![image](https://user-images.githubusercontent.com/54993262/120100307-be137e80-c15d-11eb-8cd0-6106c8efcfc2.png)

_Case Statements_
![image](https://user-images.githubusercontent.com/54993262/120100942-1009d380-c161-11eb-8169-34bd4d6b0607.png)

Synthesis output: Whenever _select[0]_ is 0 it follows _i0_, if 1 it follows _i1_. 
![image](https://user-images.githubusercontent.com/54993262/120100979-54956f00-c161-11eb-8cda-3734690177bc.png)

Simulator output : The enable condition is negation of _sel[1]_. 
![image](https://user-images.githubusercontent.com/54993262/120101177-5ad81b00-c162-11eb-971e-268350e017ea.png)

![image](https://user-images.githubusercontent.com/54993262/120101147-37ad6b80-c162-11eb-9c30-1de766054600.png)


Using _defualt_ statements can partially solve the problems of latching. We can observe by the following example.
![image](https://user-images.githubusercontent.com/54993262/120101800-84df0c80-c165-11eb-9551-8a6b5307007d.png)
![image](https://user-images.githubusercontent.com/54993262/120101878-fcad3700-c165-11eb-82ab-5c88143cfc29.png)
Thus no latches are inferred.

## For Loop and For Generate
Verilog file of a _Mux_ implemented using a _for_ _loop_
![image](https://user-images.githubusercontent.com/54993262/120109953-44918580-c189-11eb-9c60-fdda11ad631a.png)
Simulator output:
![image](https://user-images.githubusercontent.com/54993262/120110153-0f396780-c18a-11eb-96ad-6a614194f408.png)

Output generated by _Yosys_
![image](https://user-images.githubusercontent.com/54993262/120110247-5889b700-c18a-11eb-984c-b565d3884865.png)

Verilog file of a _Demux_ implemented using a _for_ _loop_
![image](https://user-images.githubusercontent.com/54993262/120110312-94248100-c18a-11eb-8c67-2b1128c766ea.png)
Simulator output:
![image](https://user-images.githubusercontent.com/54993262/120110404-e82f6580-c18a-11eb-8e3f-58a6d15b0657.png)
Synthesis Output:
![image](https://user-images.githubusercontent.com/54993262/120110825-d18a0e00-c18c-11eb-8162-e5d4c14b5d9e.png)

__Implementation of Ripple carry adder using Full Adder__

Verilog codes for _ripple carry adder and full adder_
![image](https://user-images.githubusercontent.com/54993262/120112474-a35bfc80-c193-11eb-853c-82cdccb6cf2b.png)
![image](https://user-images.githubusercontent.com/54993262/120112489-af47be80-c193-11eb-9358-18b7bab89888.png)

The submodule file should be mentioned, else would result in an error.
![image](https://user-images.githubusercontent.com/54993262/120112665-5a587800-c194-11eb-8a51-5cbb064db4a9.png)

The output can be verified by looking at _sum_out_ 
![image](https://user-images.githubusercontent.com/54993262/120112781-f1bdcb00-c194-11eb-9918-c916bf9103e6.png)

***

# Acknowledgement
* [Kunal Ghosh](https://github.com/kunalg123/)
* [Shon Taware](https://github.com/ShonTaware)

***

# References
* [Sky130 Technology](https://github.com/google/skywater-pdk)




