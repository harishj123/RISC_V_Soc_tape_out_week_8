
---

# 👋✨ **Welcome to Week 8 – Post-Layout STA & Multi-Corner Timing Analysis for VSDBabySoC**

Welcome to the **final and most exciting week** of the **VSDBabySoC Physical Design Internship Series!**
In **Week 8**, we perform **Post-Layout Static Timing Analysis (STA)** using real routed parasitics — getting as close as possible to **actual silicon timing**.

This README is a **complete, polished documentation** of the entire workflow, including multi-corner STA, SPEF annotation, timing graphs, and a comparison with the post-synthesis timing (Week 3).

---

# 🌟 **Week 8 — Post-Layout STA & Multi-Corner Timing Sign-off**

This repository captures the full procedure of performing **post-route STA** on the **VSDBabySoC** design.
The central aim of this week is to understand how:

* 🧮 **Routing parasitics**
* 🌡️ **PVT corners**
* ⚡ **Device variations**

affect timing and influence the final tape-out decision.

This week acts as the **final verification checkpoint** in the ASIC implementation flow.

---

# 🚀 **1. Introduction**

After completing floorplanning, placement, CTS, routing, and SPEF extraction in Week 7, **Week 8 focuses on timing sign-off** through:

### ✅ **Post-Layout STA**

### ✅ **SPEF (RC parasitics) annotation**

### ✅ **Multi-Corner STA (TT / SS / FF)**

### ✅ **Critical path timing reports & timing graphs**

### ✅ **Comparison: Week 3 (pre-layout) vs Week 8 (post-layout)**

### ✅ **Understanding physical delay effects and timing closure**

By the end of Week 8, you complete a real, industry-style **ASIC timing sign-off** flow.

---

# 🎯 **2. Objectives of Week 8**

### 🔹 **Perform Post-Layout Static Timing Analysis**

With real routed parasitics.

### 🔹 **Run Multi-Corner Timing (PVT Variations)**

* **TT** – Typical
* **SS** – Worst case (Setup critical)
* **FF** – Best case (Hold critical)

### 🔹 **Generate Critical Path Timing Graphs**

Arrival, delay, slew, capacitance, and slack.

### 🔹 **Compare Week 3 vs Week 8 Timing**

Observe how physical design changes everything.

### 🔹 **Understand Parasitic Effects**

RC delay, slew degradation, coupling, loading impacts.

---

# 🧱 **3. Files Used in Post-Layout STA**

### 📥 **Input Files**

| File                         | Purpose                           |
| ---------------------------- | --------------------------------- |
| `vsdbabysoc_postroute.v`     | Post-route gate-level netlist     |
| `vsdbabysoc.spef`            | Extracted routing parasitics (RC) |
| `constraints.sdc`            | Timing constraints                |
| `tt.lib`, `ss.lib`, `ff.lib` | Liberty models for PVT analysis   |

### 📤 **Output Files**

| File              | Purpose                     |
| ----------------- | --------------------------- |
| `*_setup.rpt`     | Setup timing reports        |
| `*_hold.rpt`      | Hold timing reports         |
| Timing graphs     | Critical path visualization |
| Comparison tables | Week 3 vs Week 8 results    |

---

# ⚙️ **4. Multi-Corner STA Script (OpenSTA)**

```tcl
# Load post-route gate-level netlist
read_verilog vsdbabysoc_postroute.v

# Load constraints
read_sdc constraints.sdc

# Load liberty libraries
read_liberty tt.lib
read_liberty ss.lib
read_liberty ff.lib

# Load parasitics from SPEF
read_spef vsdbabysoc.spef

# List of corners
set corners {tt ss ff}

foreach C $corners {
    puts "\nRunning STA for Corner: $C\n"
    current_corner $C
    update_timing

    # Setup analysis
    report_checks -path_delay max -fields {slew cap nets} -digits 4 -nosplit \
        > reports/${C}_setup.rpt

    # Hold analysis
    report_checks -path_delay min -fields {slew cap nets} -digits 4 -nosplit \
        > reports/${C}_hold.rpt
}
```

### ✔️ Performs

* SPEF annotation
* Multi-corner switching
* Setup & Hold report dumping
* Clean folder organization

---

# 📊 **5. Timing Graph Generation**

Critical path timing graphs include:

* 🕒 Gate delays
* ⚡ Net delays
* 📉 Slew propagation
* 🧩 Capacitance loading
* 🧮 Arrival & required times
* 🟢 Slack (Setup/Hold)

Generated graphs are stored in:

```
/graphs/tt/
/graphs/ss/
/graphs/ff/
```

**Post Synthesis**

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/post%20synthesis.png?raw=true)

**Post CTS**

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/Post-CTS.png?raw=true)

**Post-Placement(Pre-CTS)**

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/Post-Placement%20(Pre-CTS).png?raw=true)

**Post-Routing**

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/Post-Routing.png?raw=true)

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/TNS.png?raw=true)

---

# 🔍 **6. Key Observations — Post-Layout Timing Insights**

### 📉 **1. WNS decreases after routing**

Because real RC parasitics slow down signals.

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/WNS.png?raw=true)

### ⚡ **2. Hold slack becomes critical in FF**

Fast devices → early arrival → potential hold violations.

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/worst%20hold%20slack.png?raw=true)

### 🐢 **3. Setup timing worst at SS**

Slow devices + low voltage + high temperature.

![image alt](https://github.com/harishj123/RISC_V_Soc_tape_out_week_8/blob/main/Week_8/worst%20setup%20slack.png?raw=true)


### 🔌 **4. Parasitics impact**

* Increased wire delay
* Coupling capacitance slow-down
* Slew degradation increases cell delay
* Load-dependent delay variations

These effects make post-route timing more realistic and conservative.

---

# 🧬 **7. Physical Interpretation**

### ✔️ **Why Post-Route Timing ≠ Pre-Route Timing?**

Because wires are no longer “ideal”.
Actual silicon has:

* Resistance
* Capacitance
* Coupling
* Non-linear gate behavior

All affecting **delay, slew, and slack**.

---

### 🕘 **Setup Timing (MAX Delay)**

Worst in **SS Corner**
✔ Slow transistors
✔ High temp
✔ Low voltage
➡ Increased delay → setup violations

### 🕒 **Hold Timing (MIN Delay)**

Worst in **FF Corner**
✔ Fast switching
✔ Low RC
➡ Early arrival → hold violations

---

# 🏆 **8. Learning Outcomes**

By completing Week 8:

### 🎓 I learned:

✔ How to perform **post-layout STA**
✔ How to annotate **SPEF parasitics**
✔ How to run **multi-corner STA**
✔ How to generate **critical timing graphs**
✔ How to compare **pre vs post layout timing**
✔ How physical parasitics affect timing closure
✔ How to complete a full **ASIC sign-off flow**

This week marks the **final step before tape-out**.

---

# 🏁 **Conclusion**

Week 8 completes the VSDBabySoC physical design journey.
You performed:

* Real parasitic extraction
* Multi-corner timing analysis
* Setup/Hold verification
* Critical path graphing
* Timing sign-off
* Pre vs post-layout comparison

This repository stands as a **professional-grade documentation** of a complete ASIC timing sign-off process — from RTL to routed design.

---
