
---

# **Week 8 – Post-Layout STA & Multi-Corner Timing Analysis**

Week 8 marks the **final stage** of the VSDBabySoC physical design flow. After completing placement, CTS, routing, and SPEF extraction, this week focuses on **Post-Layout Static Timing Analysis (STA)** using real routed parasitics. This step brings the timing analysis closest to what will be seen on silicon.

---

## **📌 Objectives**

* Perform **post-route STA** with accurate wire delays
* Annotate **SPEF RC parasitics**
* Run **multi-corner timing analysis** (TT / SS / FF)
* Generate **setup and hold timing reports**
* Visualize **critical timing paths**
* Compare **pre-layout vs post-layout timing**
* Understand how **PVT variations** and parasitics affect timing

---

## **📁 Files Used**

**Input:**

* `vsdbabysoc_postroute.v` – Gate-level netlist after routing
* `vsdbabysoc.spef` – Extracted parasitic file
* `constraints.sdc` – Timing constraints
* `tt.lib`, `ss.lib`, `ff.lib` – Liberty timing models

**Output:**

* `*_setup.rpt` – Setup analysis reports
* `*_hold.rpt` – Hold analysis reports
* Timing graphs for each corner

---

## **🛠 Multi-Corner STA Script (OpenSTA)**

```tcl
read_verilog vsdbabysoc_postroute.v
read_sdc constraints.sdc

read_liberty tt.lib
read_liberty ss.lib
read_liberty ff.lib

read_spef vsdbabysoc.spef

foreach C {tt ss ff} {
    current_corner $C
    update_timing

    report_checks -path_delay max -fields {slew cap nets} -nosplit \
        > reports/${C}_setup.rpt

    report_checks -path_delay min -fields {slew cap nets} -nosplit \
        > reports/${C}_hold.rpt
}
```

This script automates:
✔ SPEF loading
✔ Corner switching
✔ Setup/Hold report generation

---

## **📊 Key Observations**

### **1️⃣ Post-route timing becomes slower**

Due to:

* Wire resistance
* Parasitic capacitance
* Coupling effects
* Slew degradation

### **2️⃣ Setup timing worst at SS corner**

Slow transistors + high temperature → increased delay.

### **3️⃣ Hold timing worst at FF corner**

Fast switching → early arrival → possible hold violations.

### **4️⃣ Timing differs significantly from Week 3**

Pre-route = ideal wires
Post-route = real RC delays
This shows why final sign-off must use routed parasitics.

---

## **📘 Learning Outcome**

By the end of Week 8, you have learned:

* How to run **post-layout STA**
* How to annotate and use **SPEF parasitics**
* How to perform **multi-corner timing**
* How to analyze **setup & hold paths**
* How routing parasitics affect delay
* How to complete a real **timing sign-off flow**

This concludes the physical design cycle for VSDBabySoC.

---
