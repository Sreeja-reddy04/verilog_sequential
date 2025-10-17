***Goofy-Gadgets***
# sequential circuits
### - latch
  - SR latch
### - Shift Registers
  - siso
  - sipo
  - piso
  - pipo
  - bi_directional
### - Counters
  - sync up(4-bit)
  - bidirectional up-down
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
# pipo
## [RTL]
```bash
module pipo(clk,reset,prll_out,prll_in);
input clk,reset;
input[3:0]prll_in;
output reg[3:0]prll_out;
always@(posedge clk or posedge reset)
begin
 if(reset)
	 prll_out<=4'b0000;
 else 
    prll_out<=prll_in;
end
endmodule
```
## [Test bench]
```bash
module pipo_tb;

	// Inputs
	reg clk;
	reg reset;
	reg [3:0] prll_in;

	// Outputs
	wire [3:0] prll_out;

	// Instantiate the Unit Under Test (UUT)
	pipo uut (
		.clk(clk), 
		.reset(reset), 
		.prll_out(prll_out), 
		.prll_in(prll_in)
	);
	always #5 clk=~clk;

	initial begin
		// Initialize Inputs
		clk = 0;
		reset = 1;
		prll_in = 4'b0000;

		// Wait 100 ns for global reset to finish
		#100;
		reset=0;
		prll_in=4'b1010;
		#10; // Add stimulus here
      prll_in=4'b1110;
		#10;
		prll_in=4'b0011;
		#10;
		prll_in=4'b0001;
		#10;
	end
       initial begin
      #200 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b, prll_in=%b,prll_out=%b",clk,reset,prll_in,prll_out);
	//Step3 : Use $monitor to display the various inputs and outputs
      end
endmodule
```
# bi_directional
## [RTL]
```bash
module bidir_shift_reg(clk,reset,load,shift_dir,prl_in,prl_out);
input clk,reset,load,shift_dir;
input [3:0]prl_in;
output reg[3:0]prl_out;
//input shift_left,shift_right;
always@(posedge clk or posedge reset)
begin
  if(reset)
  begin
     prl_out<=4'b0000;
  end
  else if(load)
     begin
    prl_out<=prl_in;
	  end
  else if(shift_dir)
       begin
    prl_out<={prl_out[2:0],1'b0};
	    end
  else
        begin
    prl_out<={1'b0,prl_out[3:1]};
	     end
end
endmodule
```
## [Test bench]
```bash
module bidir_shift_reg_tb;
	reg clk;
	reg reset;
	reg load;
	reg shift_dir;
	reg [3:0] prl_in;
	wire [3:0] prl_out;
	bidir_shift_reg uut (
		.clk(clk), 
		.reset(reset), 
		.load(load), 
		.shift_dir(shift_dir), 
		.prl_in(prl_in), 
		.prl_out(prl_out)
	);   
	  always #5 clk=~clk;
	initial begin
		clk = 0;
		reset = 1;
		load = 0;
		//shift_left=0;
		//shift_right=0;
		shift_dir = 0;
		prl_in = 4'b0000;
		#5 reset = 1;
      #10 reset = 0;
    prl_in = 4'b1010;
    load = 1;
    #10; 
	 load = 0; 
    // Shift left operation
    shift_dir = 1;
	 #10;
	 shift_dir = 0;
	 #10;
	 shift_dir = 1;
	 #40;
	end
     initial begin
      #200 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,load=%b shift_dir=%b prl_in=%b,prl_out=%b",clk,reset,load,shift_dir,prl_in,prl_out);
      end 
endmodule
```
# sync up(4-bit)
## [RTL]
```bash
module four_counter(clk,reset,load,data,count);
input clk,reset,load;
input [3:0]data;
output reg[3:0]count;
always @(posedge clk)
begin
if(reset)
begin
count<=0;
end
else if(load)
begin
count<=data;
end
else
begin
count<=count+1;
end
end
endmodule
```
## [Test bench]
```bash
module four_counter_tb;
	reg clk;
	reg reset;
	reg load;
	reg [3:0] data;
	wire [3:0] count;
   integer i;
	// Instantiate the Unit Under Test (UUT)
	four_counter uut (.clk(clk), .reset(reset), .load(load), .data(data), .count(count));
	always
	  begin
	 clk=1'b0;
	 #10;
	 clk=1'b1;
	 #10;
	 end
	 task re_set;
	 begin
	 @(negedge clk);
	  reset=1'b1;
	 @(negedge clk);
	  reset=1'b0;
	  end
    endtask	 
    task in(input [3:0]k);
     begin
       data=k;
      end
    endtask	
	 task load_(input a);
	 begin
	   load=a;
	 end
	 endtask
	initial begin
		// Initialize Inputs
		clk = 0;
		reset = 0;
		load = 0;
		data = 0;
		#10;
		re_set;
       load_(0);
		in(4'b0001);
		re_set;
		load_(1);
		in(4'b1001);
		re_set;
		load_(1);
		in(4'b0001);
		re_set;
	   load_(0);
		in(4'b1001);
		re_set;
      load_(1);
		in(4'b1101);
		#10;
		re_set;
        load_(1);
		end
	end
	initial begin
      #1000$finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,data=%b,load=%b,count=%b",clk,reset,data,load,count);
	end  
endmodule
```
# bidirectional up-down
## [RTL]
```bash
module up_downcounter(clk,reset,load,data,dir,count);
input clk,reset,dir,load;
input [3:0]data;
output reg[3:0]count;
always @(posedge clk)
begin
if(reset)
begin
count<=0;
end
else if(load)
begin
count<=data;
end
else if(dir)
begin
count<=count+1;
end
else
begin
count<=count-1;
end
end
endmodule
```
## [Test bench]
```bash
module up_downcounter_tb;
	reg clk;
	reg reset;
	reg load;
	reg [3:0] data;
	reg dir;
	wire [3:0] count;
	up_downcounter uut (.clk(clk), .reset(reset), .load(load), .data(data), .dir(dir), .count(count));
	always
	  begin
	 clk=1'b0;
	 #10;
	 clk=1'b1;
	 #10;
	 end
      task re_set;
	 begin
	 @(negedge clk);
	  reset=1'b1;
	 @(negedge clk);
	  reset=1'b0;
	  end
    endtask	 
    task in(input [3:0]k);
     begin
       data=k;
      end
    endtask	
	 task load_(input a);
	 begin
	   load=a;
	 end
	 endtask
	 task dirr(input w);
	 begin
	  dir=w;
	 end
	 endtask
	initial begin
		clk = 0;
		reset = 0;
		load = 0;
		data = 0;
		dir = 0;
		#100;
		dirr(1);
		re_set;
		load_(1);
		in(4'b0001);
		dirr(0);
		re_set;
	   load_(0);
		in(4'b1001);
		in(4'b1101);
		#10;
	end
      initial begin
      #600 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,data=%b,load=%b,dir=%b;count=%b",clk,reset,data,load,dir,count);
      end  
endmodule
```
