# 💡 Advanced Verilog HDL Learning Guide for Computer Architecture Students

Welcome to the **Advanced Verilog Learning Guide**! 🚀  
This guide helps you learn **digital circuit design** using **Verilog HDL**, covering everything from basic logic gates to advanced sequential systems, including **flip-flops**, **registers**, **counters**, and **FSMs**.

---

## 🧠 What is Verilog?

**Verilog** is a *Hardware Description Language (HDL)* used to describe, simulate, and synthesize digital circuits.  
It’s essential for **FPGA design**, **CPU design**, and **digital system architecture**.

---

## ⚙️ Design Levels in Verilog

| Level | Description | Example |
|-------|--------------|----------|
| Gate-level | Describes actual logic gates | `assign y = a & b;` |
| Dataflow-level | Uses Boolean equations | `assign y = (a & b) | (~a & c);` |
| Behavioral-level | Describes logic using procedural code | `always @(posedge clk)` |

---

## 🧩 1. Gate-Level Modeling

### ✅ AND Gate
```verilog
module and_gate (input a, input b, output y);
    assign y = a & b; // Output is 1 only when both inputs are 1
endmodule
```

### ✅ OR Gate
```verilog
module or_gate (input a, input b, output y);
    assign y = a | b; // Output is 1 when either input is 1
endmodule
```

### ✅ XOR Gate
```verilog
module xor_gate (input a, input b, output y);
    assign y = a ^ b; // Exclusive OR
endmodule
```

---

## 🔢 2. Dataflow Modeling — Adders

### ➕ Half Adder
```verilog
module half_adder (input a, input b, output sum, output carry);
    assign sum = a ^ b;
    assign carry = a & b;
endmodule
```

### ➕ Full Adder
```verilog
module full_adder (input a, input b, input cin, output sum, output cout);
    assign sum  = a ^ b ^ cin;
    assign cout = (a & b) | (b & cin) | (a & cin);
endmodule
```

### ➗ 4-bit Ripple Carry Adder
```verilog
module ripple_adder_4bit (
    input [3:0] a, input [3:0] b, input cin,
    output [3:0] sum, output cout
);
    wire c1, c2, c3;
    full_adder FA0 (a[0], b[0], cin, sum[0], c1);
    full_adder FA1 (a[1], b[1], c1,  sum[1], c2);
    full_adder FA2 (a[2], b[2], c2,  sum[2], c3);
    full_adder FA3 (a[3], b[3], c3,  sum[3], cout);
endmodule
```

---

## ⏱️ 3. Sequential Logic — Flip-Flops and Registers

### 🧩 D Flip-Flop
```verilog
module d_flip_flop (input d, input clk, input reset, output reg q);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

### 🔄 T Flip-Flop
```verilog
module t_flip_flop (input t, input clk, input reset, output reg q);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else if (t)
            q <= ~q;
    end
endmodule
```

### ⚙️ JK Flip-Flop
```verilog
module jk_flip_flop (input j, input k, input clk, input reset, output reg q);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else begin
            case ({j, k})
                2'b00: q <= q;
                2'b01: q <= 1'b0;
                2'b10: q <= 1'b1;
                2'b11: q <= ~q;
            endcase
        end
    end
endmodule
```

### 🧠 SR Flip-Flop
```verilog
module sr_flip_flop (input s, input r, input clk, input reset, output reg q);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 1'b0;
        else begin
            case ({s, r})
                2'b00: q <= q;
                2'b01: q <= 1'b0;
                2'b10: q <= 1'b1;
                2'b11: q <= 1'bx;
            endcase
        end
    end
endmodule
```

### 🧮 4-bit Register
```verilog
module register_4bit (input [3:0] d, input clk, input reset, output reg [3:0] q);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 4'b0000;
        else
            q <= d;
    end
endmodule
```

### 🔁 8-bit Shift Register
```verilog
module shift_reg_8bit (
    input clk, input reset, input load,
    input [7:0] d, input dir,
    output reg [7:0] q
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            q <= 8'b00000000;
        else if (load)
            q <= d;
        else if (dir)
            q <= q >> 1;
        else
            q <= q << 1;
    end
endmodule
```

---

## ⏱️ 4. Counters

### ⬆️ Up Counter
```verilog
module counter_up (input clk, input reset, output reg [3:0] count);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 0;
        else
            count <= count + 1;
    end
endmodule
```

### ⬇️ Down Counter
```verilog
module counter_down (input clk, input reset, output reg [3:0] count);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b1111;
        else
            count <= count - 1;
    end
endmodule
```

### 🔀 Up/Down Counter
```verilog
module counter_updown (input clk, input reset, input up_down, output reg [3:0] count);
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 0;
        else if (up_down)
            count <= count + 1;
        else
            count <= count - 1;
    end
endmodule
```

---

## 🧭 5. FSM Example — Traffic Light Controller 🚦
```verilog
module traffic_light (
    input clk, input reset,
    output reg [1:0] light
);
    always @(posedge clk or posedge reset) begin
        if (reset)
            light <= 2'b00;
        else begin
            case (light)
                2'b00: light <= 2'b10;
                2'b10: light <= 2'b01;
                2'b01: light <= 2'b00;
            endcase
        end
    end
endmodule
```

---

## 🧪 6. Testbench Example (4-bit Counter)
```verilog
module tb_counter_up;
    reg clk, reset;
    wire [3:0] count;

    counter_up uut (.clk(clk), .reset(reset), .count(count));

    initial begin
        $dumpfile("counter_wave.vcd");
        $dumpvars(0, tb_counter_up);
        clk = 0; reset = 1;
        #10 reset = 0;
        repeat (16) begin
            #5 clk = ~clk;
        end
        $finish;
    end
endmodule
```

---

## 🖥️ 7. Simulation & GTKWave

### ▶️ Compile
```bash
iverilog -o sim counter_up.v tb_counter_up.v
```

### ▶️ Run Simulation
```bash
vvp sim
```

### 📈 View Waveform
```bash
gtkwave counter_wave.vcd
```

---

## 🧰 Tools

| Tool | Purpose | Link |
|------|----------|------|
| Icarus Verilog | Simulation | https://bleyer.org/icarus/ |
| GTKWave | Waveform Viewer | http://gtkwave.sourceforge.net/ |
| EDA Playground | Online HDL IDE | https://www.edaplayground.com/ |

---

## 💬 Final Tips
- Always include **reset** in sequential designs.
- Use **non-blocking (`<=`)** for registers.
- Test all designs with **testbenches**.
- Visualize with **GTKWave** for debugging.
- Keep your code **modular, readable, and commented**.

Happy Coding and Simulating! 🚀
