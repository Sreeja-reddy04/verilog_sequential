***Goofy-Gadgets***
# sequential circuits
### - latch
### - Shift Registers
  - siso
  - sipo
  - piso
  - pipo
## SR latch-[RTL]
```bash
module SR_latch(s,r,q,qb);
input s,r;
output q,qb;
nor n1(q,r,qb);
nor n2(qb,s,q);

endmodule
```
## [Test bench]
```bash
module SR_latch_tb;
	reg s;
	reg r;
	wire q;
	wire qb;
	// Instantiate the Unit Under Test (UUT)
	SR_latch uut (.s(s), .r(r), .q(q), .qb(qb));
	initial begin
		// Initialize Inputs
		s = 0;
		r = 0;
		// Wait 100 ns for global reset to finish
		#100;
		  s=0;r=1;#10;
          s=0;r=0;#10;
		  s=1;r=0;#10;
		  s=1;r=1;#10;
		  s=0;r=1;#10;
          s=0; r=0;#10;
		  s=1; r=0;#10;
		  s=1; r=1;#10;
	end
     initial begin
      #1000$finish;
		end
		initial begin
   $monitor("s=%b,r=%b,q=%b,qb=%b",s,r,q,qb);
      end   
endmodule
```
# Siso
## [RTL]
```bash
module sis0(clk,reset,data,out);
input clk,reset;
input data;
output out;
reg [3:0]s_r;
always@(posedge clk)
begin
 if(reset)
	 s_r<=4'b0000;
 else
	 s_r<={s_r[2:0],data};
	 end
assign out=s_r[3];
endmodule
```
## [Test bench]
```bash
module siso_tb;
	reg clk;
	reg reset;
	reg data;
	wire [3:0] s_r;
	wire out;
	sis0 uut (.clk(clk), .reset(reset), .data(data), .out(out));
	 always #5 clk = ~clk;
	initial begin
		clk = 0;
    	reset = 1;
		 data = 0;
		  #10;
          reset=0;
		  data=1;
		  #10;
		  data=0;
		  #10;
		  data=1;
		  #10;
		  data=0;
		  #10;
	end
       initial begin
      #120 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b, data=%b,out=%b",clk,reset,data,out);
      end
endmodule
```
# sipo
## [RTL]
```bash
module sipo(clk,reset,data,prll_out);
input clk,reset;
input data;
output [3:0]prll_out;
reg [3:0]s_r;
always@(posedge clk)
begin
 if(reset)
	 s_r<=4'b0000;
 else
	 s_r<={s_r[2:0],data};
end
assign prll_out=s_r;
endmodule
```
## [Test bench]
```bash
module sipo_tb;
	reg clk;
	reg reset;
	reg data;
	wire [3:0] prll_out;
	sipo uut (
		.clk(clk), 
		.reset(reset), 
		.data(data), 
		.prll_out(prll_out)
	);
     always #5 clk=~clk;
	initial begin
		// Initialize Inputs
		clk = 0;
		reset = 1;
		data = 0;
		#10;
        reset=0;
		  data=1;
		  #10;
		  data=0;
		  #10;
		  data=1;
		  #10;
		  data=1;
		  #10;
	end
       initial begin
      #120 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b, data=%b,prll_out=%b",clk,reset,data,prll_out);
	//Step3 : Use $monitor to display the various inputs and outputs
      end
endmodule
```
# piso
## [RTL]
```bash
module piso(clk,reset,load,serial_out,prll_in);
input clk,reset;
input load;
output [3:0]prll_in;
output serial_out;
reg [3:0]s_r;
always@(posedge clk or posedge reset)
begin
 if(reset)
	 s_r<=4'b0000;
 else if(load)
    s_r<=prll_in;
	 else
	 s_r<={s_r[2:0],1'b0};
end
assign serial_out=s_r[3];
endmodule
```
## [Test bench]
```bash
module piso_tb;
	reg clk;
	reg reset;
	reg load;
	wire serial_out;
	reg [3:0] prll_in;
	piso uut (.clk(clk), .reset(reset), .load(load), .serial_out(serial_out), .prll_in(prll_in));
      always#5 clk=~clk;
	 initial begin
		clk = 0;
		reset = 1;
		load = 0;
      prll_in=4'b0000;
		#100;
		  reset=0;
        load=1;
		  prll_in=4'b0010;
		  #10;
		  load=0;
		  #40;
		  reset=0;
          load=1;
		  prll_in=4'b1010;
		  #10;
		  load=0;
		  #40;
	end
       initial begin
      #500 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b, prll_in=%b,serial_in=%b",clk,reset,serial_out,prll_in);
      end
endmodule
```


