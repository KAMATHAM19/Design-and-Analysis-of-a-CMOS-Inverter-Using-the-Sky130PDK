# Design-and-Analysis-of-a-CMOS-Inverter-Using-the-Sky130PDK

   * [CMOS Inverter](#cmos-inverter)
      * [Creating cmos inverter and performed pre-layout using xschem & ngspice](#creating-cmos-inverter-and-performed-pre-layout-using-xschem-&-ngspice)
          * [Pre-layout DC Analysis](#pre-layout-dc-analysis)
          * [Pre-layout Transient Analysis](#pre-layout-transient-analysis)
          * [Generated pre-layout netlist from xschem & ngspice](#generated-pre-layout-netlist-from-xschem-&-ngspice)
      * [Post-layout characterization of an inverter using Magic & SKY130 PDKs](#post-layout-characterization-of-an-inverter-using-magic-&-sky130-pdks)
          * [Generated post-layout netlist from magic & SKY130 PDKs](#generated-post-layout-netlist-from-magic-&-sky130-pdks)
          * [Post-layout DC Analysis](#post-layout-dc-analysis)
          * [Post-layout Transient Analysis](#post-layout-transient-analysis)
       * [Layout vs Schematic report](#layout-vs-schematic-report)
    
<a name="cmos-inverter"></a>
## CMOS INVERTER

  A CMOS inverter is a type of digital circuit that converts a digital input signal from one state (e.g., 0 or 1) to its opposite state (e.g., 1 or 0). It is a fundamental component of digital electronics that is widely used in digital systems such as microprocessors, digital logic circuits, and digital-to-analog converters.

A CMOS inverter is typically made up of two complementary transistors, one p-type (pMOS) and one n-type (nMOS). The pMOS transistor pulls the output high, while the nMOS transistor pulls the output low. When the inverter's input is high (1), the nMOS transistor is turned on and the pMOS transistor is turned off, resulting in a low output. When the inverter's input is low (0), the pMOS transistor is turned on and the nMOS transistor is turned off, resulting in a high output.

One of the primary advantages of CMOS inverters is their low power consumption, as the transistors are only turned on when the input changes state, rather than constantly drawing current. Moreover, CMOS inverters are highly scalable and easily integrated into larger circuits, making them a popular choice for digital system design.    


<a name="creating-cmos-inverter-and-performed-pre-layout-using-xschem-&-ngspice"></a>
## Creating cmos inverter and performed pre-layout using xschem & ngspice
The schematic circuit is created by connecting components from the open_pdk library.

<img width="922" alt="inverter schematic xschem dc" src="https://user-images.githubusercontent.com/64173714/219821665-1c58105d-0491-46b9-84e5-8372fbce9f8d.png">

<a name="pre-layout-dc-analysis"></a>
### Pre-layout DC Analysis
DC analysis would be used to create a Voltage Transfer Characteristics (VTC) curve for the circuit. It will sweep the value of Vin from high to low to determine how the circuit works with respect to different voltage levels in the input. When the simulation is run, the plot shown below is obtained.

<img width="659" alt="dc" src="https://user-images.githubusercontent.com/64173714/218326601-d4310d78-607a-4e88-9e84-dff5820cebea.png">

<a name="pre-layout-transient-analysis"></a>
### Pre-layout Transient Analysis
Transient analysis of a circuit is the simulation of the circuit's electrical behaviour over time, specifically its response to changing input signals.

The schematic circuit is created by connecting components from the open_pdk library and performing transient analysis.

<img width="923" alt="inverter schematic xschem trans" src="https://user-images.githubusercontent.com/64173714/218344215-2a247de5-f210-47c1-99bc-8888a5bd6973.png">
<img width="727" alt="tran" src="https://user-images.githubusercontent.com/64173714/218344227-314b2287-d26d-4236-b5ca-c0029ae695a7.png">

<a name="generated-pre-layout-netlist-from-xschem-&-ngspice"></a>
### Generated pre-layout netlist from xschem & ngspice
```
** sch_path: /home/venkat/pd/Lab1/inverter_schematic.sch
**.subckt inverter_schematic vout vin vin
*.opin vout
*.ipin vin
*.ipin vin

XM2 vout vin Gnd Gnd sky130_fd_pr__nfet_01v8 L=0.15 W=1 nf=1 ad='int((nf+1)/2) * W/nf * 0.29' as='int((nf+2)/2) * W/nf * 0.29'
+ pd='2*int((nf+1)/2) * (W/nf + 0.29)' ps='2*int((nf+2)/2) * (W/nf + 0.29)' nrd='0.29 / W' nrs='0.29 / W'
+ sa=0 sb=0 sd=0 mult=1 m=1

XM11 vout vin Vdd Vdd sky130_fd_pr__pfet_01v8 L=0.15 W=1 nf=1 ad='int((nf+1)/2) * W/nf * 0.29' as='int((nf+2)/2) * W/nf * 0.29'
+ pd='2*int((nf+1)/2) * (W/nf + 0.29)' ps='2*int((nf+2)/2) * (W/nf + 0.29)' nrd='0.29 / W' nrs='0.29 / W'
+ sa=0 sb=0 sd=0 mult=1 m=1

Vdd VDD Gnd 1.8
.save i(vdd)
vin vin Gnd pulse(0 1.8 0 500ps 500ps 4ns 10ns)
.save i(vin)
**** begin user architecture code

** opencircuitdesign pdks install
.lib /usr/local/share/pdk/sky130A/libs.tech/ngspice/sky130.lib.spice tt
.tran 0.01n 50n
.save all
**** end user architecture code
**.ends

.GLOBAL Gnd
.GLOBAL Vdd
.GLOBAL VDD
.end
```
<a name="post-layout-characterization-of-an-inverter-using-magic-&-sky130-pdks"></a>
## Post-layout characterization of an inverter using Magic & SKY130 PDKs

Copy sky130A.tech and .magicrc to your current working directory and launch the magic using command
```
magic -T sky130A.tech
```
Import your inverter's spice netlist into the Layout window
<img width="722" alt="magic 1" src="https://user-images.githubusercontent.com/64173714/218344231-fcd77e6c-8670-4c2f-a8ae-a870de63f9e4.png">

Rearrange the nfet and pfet by pressing `i` for select and `m` for moving it to the desired location and add labels/ports. Route the metal1 layer `In tkcon window tpye paint m1` in such a way that the layout has no DRC

<img width="923" alt="magic 2" src="https://user-images.githubusercontent.com/64173714/219140660-02598962-f936-4bba-8afc-f307b721b9db.png">

To extract the spice netlist from the layout, use commands

```
extract do local
extract all
ext2spice lvs
ext2spice cthresh 0 rthresh 0
ext2spice
```
<img width="922" alt="magic 3" src="https://user-images.githubusercontent.com/64173714/219159239-36a32a10-2527-44e5-a1ce-72da45aa52ac.png">

<a name="generated-post-layout-netlist-from-magic-&-sky130-pdks"></a>
### Generated post-layout netlist from magic & SKY130 PDKs
```
* NGSPICE file created from inverter_schematic.ext - technology: sky130A

.subckt sky130_fd_pr__nfet_01v8_648S5X a_n73_n100# a_n33_n188# a_15_n100# a_n175_n274#
X0 a_15_n100# a_n33_n188# a_n73_n100# a_n175_n274# sky130_fd_pr__nfet_01v8 ad=2.9e+11p pd=2.58e+06u as=2.9e+11p ps=2.58e+06u w=1e+06u l=150000u
C0 a_n73_n100# a_15_n100# 0.16fF
C1 a_n33_n188# a_n73_n100# 0.03fF
C2 a_n33_n188# a_15_n100# 0.03fF
C3 a_15_n100# a_n175_n274# 0.08fF
C4 a_n73_n100# a_n175_n274# 0.11fF
C5 a_n33_n188# a_n175_n274# 0.30fF
.ends

.subckt sky130_fd_pr__pfet_01v8_XGS3BL a_n73_n100# a_15_n100# w_n211_n319# a_n33_n197#
+ VSUBS
X0 a_15_n100# a_n33_n197# a_n73_n100# w_n211_n319# sky130_fd_pr__pfet_01v8 ad=2.9e+11p pd=2.58e+06u as=2.9e+11p ps=2.58e+06u w=1e+06u l=150000u
C0 w_n211_n319# a_15_n100# 0.06fF
C1 a_n33_n197# a_n73_n100# 0.03fF
C2 a_n33_n197# a_15_n100# 0.03fF
C3 a_n73_n100# a_15_n100# 0.16fF
C4 w_n211_n319# a_n33_n197# 0.26fF
C5 w_n211_n319# a_n73_n100# 0.09fF
C6 a_15_n100# VSUBS 0.02fF
C7 a_n73_n100# VSUBS 0.02fF
C8 a_n33_n197# VSUBS 0.05fF
C9 w_n211_n319# VSUBS 1.07fF
.ends

.subckt inverter_schematic
XXM2 vout vin vout VSUBS sky130_fd_pr__nfet_01v8_648S5X
XXM11 vout vout XM11/w_n211_n319# vin VSUBS sky130_fd_pr__pfet_01v8_XGS3BL
C0 vout XM11/w_n211_n319# 0.24fF
C1 vout vin 0.68fF
C2 vin XM11/w_n211_n319# 0.08fF
C3 vin VSUBS 0.61fF
C4 vout VSUBS 1.41fF
C5 XM11/w_n211_n319# VSUBS 1.10fF
.ends
```
open the post-layout netlist in ngspice
```
    ngspice inverter_schematic.spice
```
<a name="post-layout-dc-analysis"></a>
### Post-layout DC analysis

<img width="921" alt="post dc" src="https://user-images.githubusercontent.com/64173714/219821893-41e1916d-dff7-4e75-8fba-788a920227f9.png">

<a name="post-layout-transient-analysis"></a>
### Post-layout Transient analysis
<img width="925" alt="post trans" src="https://user-images.githubusercontent.com/64173714/219818907-cb784f41-22e5-44f3-b15b-8270d3d326af.png">

From the transient response, we will now characterise the cell's slew rate and propagation delay
1. Rise transiton - time taken by output waveform to transit from 20%(0.36v) to 80%(1.44v) of VDD(1.8v max value) 

   4.83095e-09 - 4.78239e-09 = 0.05ns = 50ps

2. Fall transition - time taken by output waveform to transit from 80% (1.44v) to 20% (0.36v) of VDD

   1.02815 - 1.02459 = 0.00356ns = 3.56ps
   
3. Rise cell delay - The difference between the time when output as well as input is at 50% (0.9v) i.e falling at 50% of output is rising

   4.80833 - 4.75208 = 0.05625ns = 56.25ps

4. Fall cell delay - The difference between the time when output as well as input is at 50% (0.9v) i.e falling at 50% of input is rising

   1.02651 - 1.025 = 0.00151ns = 1.51ps

<a name="layout-vs-schematic-report"></a>
## Layout vs Schematic report

Perform an LVS check on the pre-layout and post-layout netlists
```
 netgen -batch lvs /home/venkat/pd/Lab1/inverter_schematic.spice /home/venkat/pd/Lab1/mag/inverter_schematic.spice

```
<img width="633" alt="netgen" src="https://user-images.githubusercontent.com/64173714/219203441-98bfa8e7-152f-4436-890c-21d92800c69b.png">

<img width="920" alt="comp" src="https://user-images.githubusercontent.com/64173714/219820941-b8471cac-5ef9-41bb-8241-1cc64973c34b.png">

    
