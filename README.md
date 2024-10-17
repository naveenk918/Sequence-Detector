# Sequence-Detector
Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

Verilog Code for Sequence Detector Using Moore FSM

module moore_sequence_detector(
    input clk,
    input reset,
    input seq_in,
    output reg output_x
);
    // defining state encoding in parameter
    parameter S0 = 3'b000, 
              S1 = 3'b001, 
              S2 = 3'b010, 
              S3 = 3'b011, 
              S4 = 3'b100;

    reg [2:0] current_state, next_state;  // 3-bit state variables
    
    // State transition logic (state register update)
    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end
    
    // next state and output logic
    always @(*) begin
        output_x = 1'b0;  // default output for moore FSM
        case (current_state)
            S0: begin
                if (seq_in)
                    next_state = S1;
                else
                    next_state = S0;
            end
            S1: begin
                if (seq_in)
                    next_state = S1;
                else
                    next_state = S2;
            end
            S2: begin
                if (seq_in)
                    next_state = S3;
                else
                    next_state = S2;
            end
            S3: begin
                if (seq_in)
                    next_state = S4;
                else
                    next_state = S2;
            end
            S4: begin
                output_x = 1'b1;  // Output high on detecting 1011
                if (seq_in)
                    next_state = S1;
                else
                    next_state = S0;
            end
            default: next_state = S0;
        endcase
    end
endmodule

Verilog Code for Sequence Detector Using Mealy FSM

module mealy_sequence_detector (
    input clk,             
    input reset,
    input seq_in,
    output reg output_x
);
    // define state encoding using parameter
    parameter S0 = 3'b000, 
              S1 = 3'b001, 
              S2 = 3'b010, 
              S3 = 3'b011;

    reg [2:0] current_state, next_state;  // 3-bit state variables

    // state transition logic
    always @(posedge clk or posedge reset) begin
        if (reset)
            current_state <= S0;
        else
            current_state <= next_state;
    end

    // next state and output logic
    always @(*) begin
        output_x = 0;  
        case (current_state)
            S0: begin
                if (seq_in)
                    next_state = S1;
                else
                    next_state = S0;
            end
            S1: begin
                if (seq_in)
                    next_state = S1;
                else
                    next_state = S2;
            end
            S2: begin
                if (seq_in)
                    next_state = S3;
                else
                    next_state = S0;
            end
            S3: begin
                if (seq_in) begin
                    next_state = S1;
                    output_x = 1;  // sequence is detected
                end else
                    next_state = S2;
            end
            default: next_state = S0;
        endcase
    end
endmodule

Testbench for Sequence Detector (Moore and Mealy FSMs)

`timescale 1ns / 1ps  

module testbench;
    reg clk, reset, seq_in;
    wire output_mealy, output_moore;

    // instantiate the Mealy sequence detector
    mealy_sequence_detector u1 (
        .clk(clk),
        .reset(reset),
        .seq_in(seq_in),
        .output_x(output_mealy)
    );

    // instantiate the Moore sequence detector
    moore_sequence_detector u2 (
        .clk(clk),
        .reset(reset),
        .seq_in(seq_in),
        .output_x(output_moore)
    );

    
    always #5 clk = ~clk;  

    initial begin
        
        clk = 0;
        reset = 1;
        seq_in = 0;

        
        #10 reset = 0;

        // Apply input sequence "1011" (in hexadecimal: 0xB = 4'b1011)
        #10 seq_in = 1;  // 1
        #10 seq_in = 0;  // 0
        #10 seq_in = 1;  // 1
        #10 seq_in = 1;  // 1, 1011 sequence detected

        // apply input sequence "110" (in hexadecimal: 0x6 = 3'b110)
        #10 seq_in = 1;  // 1
        #10 seq_in = 1;  // 1
        #10 seq_in = 0;  // 0, sequence ends

        // apply input sequence "1001" (in hexadecimal: 0x9 = 4'b1001)
        #10 seq_in = 1;  // 1
        #10 seq_in = 0;  // 0
        #10 seq_in = 0;  // 0
        #10 seq_in = 1;  // 1

        // apply input sequence "011" (in hexadecimal: 0x3 = 3'b011)
        #10 seq_in = 0;  // 0
        #10 seq_in = 1;  // 1
        #10 seq_in = 1;  // 1, sequence ends

        // finish simulation after some time
        #100 $finish;
    end

    
    initial begin
        $monitor("Time = %0t | seq_in = %b | Mealy output = %b | Moore output = %b", 
                  $time, seq_in, output_mealy, output_moore);
    end
endmodule

Output : ![Screenshot 2024-10-17 121103](https://github.com/user-attachments/assets/39cd63c4-b0a0-44fc-b5ba-5d3d2c6f0a89)

Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
