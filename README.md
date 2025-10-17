# Flipflops
## D Flip flop
## [RTL]
```bash
module dff(clock,reset,d_in,Q_out,Qb_out);
  input clock,reset,d_in;
	output reg Q_out;
	output Qb_out;
   always@(posedge clock)
      begin
	 if(reset)
	    Q_out <= 1'b0;
	 else
	    Q_out <= d_in;
      end		
	 assign Qb_out=~(Q_out); 
endmodule          
```
## [Test bench]
```bash
module dff_tb();
   reg clk,reset,d;
   wire q,qb;
   parameter CYCLE = 10;//Step1 : Define a parameter with name "CYCLE" which is equal to 10  
   dff DUT(.clock(clk),.reset(reset),.d_in(d),.Q_out(q),.Qb_out(qb));
   always
      begin
	 #(CYCLE/2);
	 clk = 1'b0;
	 #(CYCLE/2);
	 clk = 1'b1;
      end
   //Reset task
   task rst_dut();
      begin
	 @(negedge clk);
	 reset=1'b1;
	 @(negedge clk);
	 reset=1'b0;
      end
   endtask
   //Data task
   task din(input i);
      begin
         @(negedge clk);
         d=i;
      end
   endtask
   initial 
      begin
         rst_dut;
         din(0);
         din(1);
         din(0);
         din(1);
         din(1);
         rst_dut;
         din(0);
         din(1);
         #10; 
      end
		initial begin
      #1000 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,d=%b,q=%b,qb=%b",clk,reset,d,q,qb);
      end
endmodule
```
# T Flipflop
## [RTL]
```bash
module tff(clock,reset,t,q,qb);
input clock,reset,t;
output qb,q;
wire w1;
xor x1(w1,t,q);
d_ff d1(.clock(clock),.reset(reset),.d_in(w1),.Q_out(q),.Qb_out(qb));
//assign qb=~(q); 
endmodule
module d_ff(clock,
	   reset,
	   d_in,
	   Q_out,
	   Qb_out);

   input clock,reset,d_in;
	output reg Q_out;
	output Qb_out;//Step1 : Declare Port Directions

   /*Understand the Behaviour of D flip-flop &
   check the coding style of synchronous reset*/

   always@(posedge clock) //or posedge reset)
      begin
	 if(reset)
	    Q_out <= 1'b0;
	 else
	    Q_out <= d_in;
      end

   //Step2 : Write the logic for Qbar				
	 assign Qb_out=~(Q_out); 
endmodule          
```
## [Test bench]
```bash
module tff_tb;
	// Inputs
	reg clock;
	reg reset;
	reg t;
	// Outputs
	wire q;
	wire qb;
	// Instantiate the Unit Under Test (UUT)
	tff uut (
		.clock(clock), 
		.reset(reset), 
		.t(t), 
		.q(q), 
		.qb(qb)
	);
	always
	begin
	 clock=1'b0;
	 #10;
	 clock=1'b1;
	 #10;
	 end
	 task re_set;
	 begin
	 @(negedge clock);
	  reset=1'b1;
	  #5;
	 @(negedge clock);
	  reset=1'b0;
	  end
	  endtask
	  task in_put(input in);
	  begin
	  @(negedge clock);
	   t=in;
		end
		endtask
		initial begin
		clock = 0;
		reset = 0;
		t = 0;
		#100;
		   re_set;
			in_put(0);
			in_put(1);
			in_put(0);
			in_put(0);
			re_set;
			in_put(0);
			in_put(0);
			in_put(1);
			#10;
	end
    initial begin
      #1000$finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,t=%b,q=%b,qb=%b",clock,reset,t,q,qb);
      end  
endmodule
```
# JK Flipflop
## [RTL]
```bash
module jkff(clk,reset,j,k,q,qb);
input j,k,reset,clk;
output reg q;
output qb;
parameter HOLD = 2'b00,
          RESET = 2'b01,
          SET   = 2'b10,
		  TOGGLE = 2'b11;
always @(posedge clk)
   begin
     if(reset)
	    begin
		   q<=0;
		 end
	  else 
	      begin
			case({j,k})
			 HOLD : q<=q;
			 RESET : q<=0;
			 SET : q<=1;
			 TOGGLE : q<=~q;
			 endcase
			 end
	end
	assign qb=~q;
endmodule
//behavioural model
always @(posedge clk)
   begin
     if(reset)
	    begin
		   q<=0;
		 end
	  else 
	      begin
			case({j,k})
			 2'b00 : q<=q; 2'b01 : q<=0;
			 2'b10 : q<=1;2'b11 : q<=~q;
			 endcase
			 end
	end
	assign qb=~q;
endmodule
```
## [Test bench]
```bash
module jkff_tb;
	// Inputs
	reg clk;
	reg reset;
	reg j;
	reg k;
	// Outputs
	wire q;
	wire qb;
   integer o;
	// Instantiate the Unit Under Test (UUT)
	jkff uut (.clk(clk), .reset(reset), .j(j), .k(k), .q(q), .qb(qb));
	always
	begin
	 clk=1'b0;
	 #10;
	 clk=1'b1;
	 #10;
	 end
   task re_set();
      begin
	 @(negedge clk);
	 reset=1'b1;
	 @(negedge clk);
	 reset=1'b0;
      end
   endtask
   task in(input m, input n);
      begin
         @(negedge clk);
         j=m;
			k=n;
      end
   endtask
	initial begin
		clk = 0;
		reset = 0;
		j = 0;
		k = 0;
		#100;
      re_set;
		in(0,1);in(1,0);
		in(1,1);in(0,1);
		in(1,1);in(0,0);
		in(1,1);in(0,1);
		in(1,1);in(0,0);
		in(1,1);in(1,0);
		in(1,1);in(0,0);
		#10;
		re_set;
      for(o=0;o<4;o=0+1)
		begin 
		 {j,k}=o;
		 #10;
		end
	end
    initial begin
      #500 $finish;
		end
		initial begin
   $monitor("clk=%b,reset=%b,j=%b,k=%b,q=%b,qb=%b",clk,reset,j,k,q,qb);
      end
endmodule
```
