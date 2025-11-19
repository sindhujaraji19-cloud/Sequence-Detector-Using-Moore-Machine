# Design and Simulation of Sequence Detector Using Moore Machine in Verilog HDL

---

## **Aim**
To design, simulate, and verify a **Sequence Detector** using the **Moore Finite State Machine (FSM)** model in Verilog HDL.

---

## **Apparatus Required**
- System with **Vivado Design Suite**
---

## **Theory**

A **sequence detector** is a type of **finite state machine (FSM)** that detects the occurrence of a specific sequence of bits in a given input stream.

### **Moore Machine Concept**
In a **Moore Machine**, the **output depends only on the present state** and **not on the current input**.  
The output changes only when the state changes.

---

### **Example Sequence: 1011**
The sequence detector should produce an output `Z = 1` whenever the input sequence **“1011”** is detected in the serial input stream.

#### **State Description**
| State | Description | Output (Z) |
|:------:|:-------------|:-----------:|
| S0 | Initial State / No bits matched | 0 |
| S1 | ‘1’ detected | 0 |
| S2 | ‘10’ detected | 0 |
| S3 | ‘101’ detected | 0 |
| S4 | ‘1011’ detected | 1 |

#### **State Transition Diagram**
<img width="403" height="557" alt="image" src="https://github.com/user-attachments/assets/9b24604c-69d2-412f-a302-2c23aed79038" />

#### **State Transition Table**
| Current State | Input (X) | Next State | Output (Z) |
|:---------------|:----------|:------------|:------------|
| S0 | 0 | S0 | 0 |
| S0 | 1 | S1 | 0 |
| S1 | 0 | S2 | 0 |
| S1 | 1 | S1 | 0 |
| S2 | 0 | S0 | 0 |
| S2 | 1 | S3 | 0 |
| S3 | 0 | S2 | 0 |
| S3 | 1 | S4 | 1 |
| S4 | 0 | S2 | 0 |
| S4 | 1 | S1 | 0 |

---

## **Program**

```
`timescale 1ns/1ps

module moore_statemachine (
    input clk,
    input reset,
    input in,
    output reg out
);
    parameter S0 = 3'b000;
    parameter S1 = 3'b001;
    parameter S2 = 3'b010;
    parameter S3 = 3'b011;
    parameter S4 = 3'b100;

    reg [2:0] current_state, next_state;

    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end

    always @(*) begin
        case (current_state)
            S0: next_state = (in) ? S1 : S0;
            S1: next_state = (in) ? S1 : S2;
            S2: next_state = (in) ? S3 : S0;
            S3: next_state = (in) ? S4 : S2;
            S4: next_state = (in) ? S1 : S2;
            default: next_state = S0;
        endcase
    end

    always @(*) begin
        case (current_state)
            S4: out = 1'b1;
            default: out = 1'b0;
        endcase
    end
endmodule

```
### Testbench
```
`timescale 1ns/1ps

module tb_moore_statemachine;
    reg clk, reset, in;
    wire out;

    moore_statemachine dut(clk, reset, in, out);

    always #5 clk = ~clk;

    initial begin
        clk = 0;
        reset = 1;
        in = 0;
        #10 reset = 0;

        in = 1; #10;
        in = 0; #10;
        in = 1; #10;
        in = 1; #10;
        in = 0; #10;
        in = 1; #10;
        in = 0; #10;
        in = 1; #10;
        in = 1; #10;

        #20 $finish;
    end

    initial begin
        $monitor("Time=%0t | In=%b | Out=%b | State=%b", $time, in, out, dut.current_state);
    end
endmodule

```
### Simulation Output
<img width="1917" height="1198" alt="image" src="https://github.com/user-attachments/assets/df1fe448-bce9-4f17-8442-1c9876434ccc" />






### Result

The Sequence Detector using Moore Machine was successfully designed and simulated in Vivado Design Suite using Verilog HDL.
The simulation confirmed that the output Z is asserted high only when the input sequence “1011” is detected.
