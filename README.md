# Experiment 4: Timing Validation of Flip-Flop Input using Random Data Generator for Setup and Hold Constraints
# 212223060296
# venkatesan s
---

## Aim  
To validate the **timing of a Flip-Flop input** using **random data generation** in **SystemVerilog** and check **setup and hold constraints** using **ModelSim 2020.1**.

---

## Apparatus Required  
- Computer with **Windows OS**  
- **ModelSim 2020.1** (or later)  
- SystemVerilog source code editor  

---

## Description  
Timing validation ensures that a Flip-Flop operates correctly under all input conditions.  
- **Setup time:** Minimum time before the clock edge that the data input must be stable.  
- **Hold time:** Minimum time after the clock edge that the data input must remain stable.  

In this experiment:  
- A **random data generator** is used to drive the Flip-Flop input.  
- **SystemVerilog constructs** like `randomize()` and `class` or `logic` vectors can be used to generate test data.  
- Simulation is used to observe **violations or adherence** to setup and hold constraints.  

---

## Features  
- Flip-Flop timing validation using **random inputs**  
- Checks for **setup and hold constraints**  
- Includes a **testbench** for verification  
- Compatible with **ModelSim 2020.1**  

---

## Procedure  

1. **Open ModelSim 2020.1**  
   - Launch the IDE.  

2. **Create a New Project**  
   - `File → New → Project`  
   - Name it `FlipFlop_Timing_Project`.  

3. **Add SystemVerilog Files**  
   - Create `flipflop.sv` → Flip-Flop design  
   - Create `flipflop_tb.sv` → Testbench with random input generator  

4. **Compile the Design and Testbench**  
   - Select both files, right-click → **Compile Selected**  
   - Resolve any syntax errors  

5. **Start Simulation**  
   - `Simulate → Start Simulation`  
   - Select testbench module (`flipflop_tb`)  

6. **Add Signals to Waveform**  
   - Select clock, data, and output signals  
   - Right-click → **Add to → Wave → Selected Signals**  

7. **Run Simulation**  
   - Type in console:  
     ```
     run 200ns
     ```  
   - Or use **Run button**  

8. **Analyze Waveforms**  
   - Check setup and hold times at the Flip-Flop input  
   - Verify Flip-Flop output stability for all random input patterns  

9. **Save Results**  
   - Save waveform (`.wlf`) for documentation  

---

## SystemVerilog Code Skeleton  

### Flip-Flop Design (`flipflop.sv`)
```systemverilog
module flipflop (
    input  logic D,       
    input  logic CLK,     
    output logic Q        
);

    always_ff @(posedge CLK) begin
        Q <= D;
    end

endmodule

```
### Testbench (`flipflop_tb.sv`)
```systemverilog
module tb;
    logic D;
    logic CLK;
    logic Q;

    flipflop uut (
        .D(D),
        .CLK(CLK),
        .Q(Q)
    );

    initial begin
        CLK = 0;
        forever #5 CLK = ~CLK;
    end

    initial begin
        // Phase 1: Constant input
        $display("\n--- Phase 1: Constant input ---");
        D = 1;
        #50;
        D = 0;
        #50;

        // Phase 2: Slow toggle
        $display("\n--- Phase 2: Slow toggle ---");
        D = 0;
        repeat (4) begin
            @(posedge CLK);
            @(posedge CLK);
            D = ~D;
        end

        // Phase 3: Fast toggle
        $display("\n--- Phase 3: Fast toggle ---");
        D = 0;
        repeat (10) begin
            #2 D = ~D;
        end
        #20;

        // Phase 4: Burst activity
        $display("\n--- Phase 4: Burst activity ---");
        D = 0;
        repeat (3) begin
            repeat (5) begin
                #3 D = $random;
            end
            #30;
        end

        // Phase 5: Edge-aligned changes
        $display("\n--- Phase 5: Edge-aligned changes ---");
        D = 0;
        repeat (6) begin
            @(posedge CLK);
            #0 D = ~D;
        end

        #50;
        $finish;
    end

    initial begin
        $dumpfile("wave.vcd");
        $dumpvars(0, tb);
    end
endmodule
```
---
### Simulation Output

Simulation is carried out using ModelSim 2020.1.
<img width="1920" height="1080" alt="Screenshot 2025-09-29 231640" src="https://github.com/user-attachments/assets/857f7ba8-9828-4a2b-a2a3-450042260b1e" />


---

### Result


The Flip-Flop maintained correct output behavior, and setup and hold times were verified for all random input cases.
