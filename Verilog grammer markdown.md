> http://www.asic-world.com/verilog/index.html
>
> All code and catalogue comes from the URL above. 
>
> This .md document is only for my review and won't contain everything beginner want.

## Introduction

```verilog
// D flip-flop Code
module d_ff ( d, clk, q, q_bar);
input d ,clk;
output q, q_bar;
wire d ,clk;
reg q, q_bar;
  	 
always @ (posedge clk)
begin
  q <= d;
  q_bar <= !d;
end

endmodule

```



## Verilog In One Day

#### Port

Besides two types of ports `in` and `out`, there's another: `inout` which is bi-directional.

**[7:0]** means we're using the little-endian convention - you start with 0 at the rightmost bit to begin the vector, then move to the left. If we had done [0:7], we would be using the big-endian convention and moving from left to right. (Anyway, it's a 8-bit port)



#### Data Type

 A **driver** is a data type which can drive a load. Basically, in a physical circuit, a driver would be anything that electrons can move through/into. And hardware has two types of driver.

One type can store values (such as flip-flop), another cannot store value but connects two points (wire).



#### Control Statements

if else; repeat; while; for; case;

```verilog
// begin and end act like curly braces in C/C++.
if (enable == 1'b1) begin
  data = 10; // Decimal assigned
  address = 16'hDEAD; // Hexadecimal
  wr_enable = 1'b1; // Binary  
end else begin
  data = 32'b0;
  wr_enable = 1'b0;
  address = address + 1;  
end


// case block
case(address)
  0 : $display ("It is 11:40PM");
  1 : $display ("I am feeling sleepy");
  2 : $display  ("Let me skip this tutorial");
  default : $display  ("Need to complete");
endcase


//for loop
 	for (i = 0; i < 16; i = i +1) begin
  	  	$display ("Current value of i is %d", i);
  end


//repeat
repeat (16) begin
  $display ("Current value of i is %d", i);
  i = i + 1;
end
```

Verilog programs often have many statements executing in parallel. All blocks marked with `always` will run simultaneously.

```verilog
// a combination
module counter (clk,rst,enable,count);
input clk, rst, enable;
output [3:0] count;
reg [3:0] count;
  	  	          
always @ (posedge clk or posedge rst)
if (rst) begin
  count <= 0;
end else begin : COUNT
  while (enable) begin
    count <= count + 1;
    disable COUNT;
  end
end

endmodule
```



#### Variable Assignment

initial the block with `initial begin` and `end`

There are two types of sensitive list: level sensitive (for combinational circuits) and edge sensitive (for flip-flops).  <u>One important note about always block: it can not drive wire data type, but can drive reg and integer data types.</u>

```verilog
//combinational logic
always  @ (a or b or sel) 
begin
  y = 0;
  if (sel == 0) begin
    y = a;
  end else begin
    y = b;
  end
end

//sequential logic
always  @ (posedge clk )
if (reset == 0) begin
  y <= 0;
end else if (sel == 0) begin
  y <= a;
end else begin
  y <= b;
end
```

**"=" is blocking assignment and "<=" is nonblocking assignment. "=" executes code sequentially inside a begin / end, whereas nonblocking "<=" executes in parallel.**

Assign statement is only use for combinational logic.



#### Task and Function

```verilog
function parity;
input [31:0] data;
integer i;
begin
  parity = 0;
  for (i= 0; i < 32; i = i + 1) begin
    parity = parity ^ data[i];
  end
end
endfunction
```

Task is similar with function. The difference is: task can have delays but function cannot; task cannot return a value but function can.



#### Test Benches

```verilog
module arbiter (
clock, 
reset, 
req_0,
req_1, 
gnt_0,
gnt_1
);

input clock, reset, req_0, req_1;
output gnt_0, gnt_1;

reg gnt_0, gnt_1;

always @ (posedge clock or posedge reset)
if (reset) begin
 gnt_0 <= 0;
 gnt_1 <= 0;
end else if (req_0) begin
  gnt_0 <= 1;
  gnt_1 <= 0;
end else if (req_1) begin
  gnt_0 <= 0;
  gnt_1 <= 1;
end

endmodule



// Testbench Code Goes here
module arbiter_tb;
  
// Declared all the arbiter inputs as reg and outputs as wire
reg clock, reset, req0,req1;
wire gnt0,gnt1;
  
// Initial all the inputs to known state
initial begin
  $monitor ("req0=%b,req1=%b,gnt0=%b,gnt1=%b", req0,req1,gnt0,gnt1);
  clock = 0;
  reset = 0;
  req0 = 0;
  req1 = 0;
// Begin to assert
  #5 reset = 1;
  #15 reset = 0;
  #10 req0 = 1;
  #10 req0 = 0;
  #10 req1 = 1;
  #10 req1 = 0;
  #10 {req0,req1} = 2'b11;
  #10 {req0,req1} = 2'b00;
  #10 $finish;
end

always begin
 #5 clock = !clock;
end

arbiter U0 (
.clock (clock),
.reset (reset),
.req_0 (req0),
.req_1 (req1),
.gnt_0 (gnt0),
.gnt_1 (gnt1)
);

endmodule
```





## Design And Tool Flow

1. Specification
2. Design
3. RTL design
4. Simulation
5. Synthesis
6. Place & Route
7. Post Si Validation





## Verilog HDL Syntax And Semantics

#### Lexical Conventions

Two types of code: `//` for one line and `/* ... */` for multi line comments.

Verilog is case sensitive and all keywords are lower case.

**NOTE :** Never use Verilog keywords as unique names, even if the case is different.

Escape identifiers begin with `\`



#### Numbers in Verilog

When used in a number, `?` is the Verilog alternative for the `z` character. 

Verilog supports <u>binary, decimal, octal and hexadecimal</u>.

The unsized number in Verilog has a default length of 32 bits.

Real numbers in Verilog cannot contain `X` and `Z`.



#### Modules

```verilog
module addbit (
a      , // first input
b      , // Second input
ci     , // Carry input
sum    , // sum output
co       // carry output
);
//Input declaration
input a;
input b;
input ci;
//Ouput declaration
output sum;
output co;
//Port Data types
wire  a;
wire  b;
wire  ci;
wire  sum;
wire  co;
//Code starts here
assign {co,sum} = a + b + ci;

endmodule // End of Module addbit
```









```verilog
module arithmetic_operators();
 
initial begin
  $display (" 5  +  10 = %d", 5  + 10);
  $display (" 5  -  10 = %d", 5  - 10);
  $display (" 10 -  5  = %d", 10 - 5);
  $display (" 10 *  5  = %d", 10 * 5);
  $display (" 10 /  5  = %d", 10 / 5);
  $display (" 10 /  -5 = %d", 10 / -5);
  $display (" 10 %s  3  = %d","%", 10 % 3);
  $display (" +5       = %d", +5);
  $display (" -5       = %d", -5);
  #10 $finish;
end

endmodule
```



#### Equality operators

There are two types of Equality operators. Case Equality and Logical Equality.

```verilog
module equality_operators();

initial begin
  // Case Equality
  $display (" 4'bx001 ===  4'bx001 = %b", (4'bx001 ===  4'bx001));
  $display (" 4'bx0x1 ===  4'bx001 = %b", (4'bx0x1 ===  4'bx001));
  $display (" 4'bz0x1 ===  4'bz0x1 = %b", (4'bz0x1 ===  4'bz0x1));
  $display (" 4'bz0x1 ===  4'bz001 = %b", (4'bz0x1 ===  4'bz001));
  // Case Inequality
  $display (" 4'bx0x1 !==  4'bx001 = %b", (4'bx0x1 !==  4'bx001));
  $display (" 4'bz0x1 !==  4'bz001 = %b", (4'bz0x1 !==  4'bz001));  
  // Logical Equality
  $display (" 5       ==   10      = %b", (5       ==   10));
  $display (" 5       ==   5       = %b", (5       ==   5));
  // Logical Inequality
  $display (" 5       !=   5       = %b", (5       !=   5));
  $display (" 5       !=   6       = %b", (5       !=   6));
  #10 $finish;
end

endmodule
```



#### Bitwise operator

Bitwise operators perform a bit wise operation on two operands. They take each bit in one operand and perform the operation with the corresponding bit in the other operand. If one operand is shorter than the other, it will be extended on the left side with zeroes to match the length of the longer operand.

*Operate bit by bit. And x,z will all be transfered into x.*



#### Reduction operator

They perform a bit-wise operation on a single operand to produce a single bit result.

*Unknown type remains unknown*



#### Replication Operator

Use to replicate a group of bits for particular times.

```verilog
module replication_operator();

initial begin
  // replication
  $display (" {4{4'b1001}}      = %b", {4{4'b1001}});
  // replication and concatenation
  $display (" {4{4'b1001,1'bz}} = %b", {4{4'b1001,1'bz}});
  #10 $finish;
end

endmodule

// {4{4'b1001}       = 1001100110011001
// {4{4'b1001,1'bz}  = 1001z1001z1001z1001z
```





