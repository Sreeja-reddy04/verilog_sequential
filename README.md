# Flipflops
 - D Flip flop
 - T Flip flop
 - JK Flip flop
# Memories
 - synchronous dual port 16x8
 - synchronous dual port 8x16
 - Asunchronous dual port 
 - Asunchronous single port
 - FIFO
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
# T Flip flop
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
# JK Flip flop
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
# synchronous dual port 16x8 
## [RTL]
```bash
module ram16_8(clk,din,dout,reset,we,re,wa,ra);
parameter WIDTH=8,
          DEPTH=16,
			 add=4;
input re,we,clk,reset;
input [add-1:0]wa,ra;
input [WIDTH-1:0]din;
output reg [WIDTH-1:0]dout;
reg [WIDTH-1:0]mem[DEPTH-1:0];
integer i;
always@(posedge clk or posedge reset)
begin
if(reset)
	begin
for(i=0;i<DEPTH;i=i+1)
mem[i]<=0;			
end
else
begin
if(we==1)
mem[wa]<=din;
if(re==1)
dout<=mem[ra];
end
end
endmodule
```
## [Test bench]
```bash
module ram16_8_tb;
	reg clk;
	reg [7:0] din;
	reg reset;
	reg we;
	reg re;
	reg [3:0] wa;
	reg [3:0] ra;
	wire [7:0] dout;
	// Instantiate the Unit Under Test (UUT)
	ram16_8 uut (.clk(clk), .din(din), .dout(dout), .reset(reset), .we(we), .re(re), .wa(wa), .ra(ra));
	    always
		  begin
		  clk=0;
		  #5;
		  clk=1;
		  #5;
		  end
		  task re_set();
		  begin
		  @(negedge clk)
		  reset=1'b1;
		  @(negedge clk)
		  reset=1'b0;
		  end
		  endtask
		  task write(input [3:0]a,input m,input [7:0]l);
		  begin
		  @(negedge clk)
		  din=l;
		  wa=a;
		  we=m;
		  end
		  endtask
		  task read(input [3:0]b,input n);
		  begin
		  @(negedge clk)
		  ra=b;
		  re=n;
		  end
		  endtask
	initial begin
		clk = 0;
		din = 0;
		reset = 0;
		we = 0;
		re = 0;
		wa = 0;
		ra = 0;
		#100;
		re_set;
		write(4'b1000,1,8'b11110000);
		#10;
		write(4'b0001,1,8'b00001111);
		#10;
		write(4'b0000,0,8'b11111111);
		#10;
		read(4'b1000,1'b1);
		#10;
		write(4'b1111,1,8'b11111111);
		read(4'b1111,1'b1);
      #10;
		read(4'b0001,1'b1);
      #10;
		read(4'b0000,1'b0);
      #10;
	end
      initial begin
        $monitor("clk=%b reset=%b we=%b re=%b wa=%b ra=%b din=%b dout=%b",clk,reset,we,re,wa,ra,din,dout);
      end
      initial begin
       #1000 $finish;
      end		 
endmodule
```
# synchronous dual port 8x16
### [RTL]
```bash
module ram8_16(clk,din,dout,reset,we,re,wa,ra);
parameter WIDTH=16,
          DEPTH=8,
			 add=3;
input re,we,clk,reset;
input [add-1:0]wa,ra;
input [WIDTH-1:0]din;
output reg [WIDTH-1:0]dout;
reg [WIDTH-1:0]mem[DEPTH-1:0];
always@(posedge clk or posedge reset)
begin
if(reset)
begin
for(i=0;i<DEPTH;i=i+1)
mem[i]<=0;
end
else
begin
if(we==1)
mem[wa]<=din;
if(re==1)
dout<=mem[ra];
end
end
endmodule
```
## [Test bench]
```bash
module ram8_16_tb;

	// Inputs
	reg clk;
	reg [15:0] din;
	reg reset;
	reg we;
	reg re;
	reg [2:0] wa;
	reg [2:0] ra;

	// Outputs
	wire [15:0] dout;

	// Instantiate the Unit Under Test (UUT)
	ram8_16 uut (
		.clk(clk), 
		.din(din), 
		.dout(dout), 
		.reset(reset), 
		.we(we), 
		.re(re), 
		.wa(wa), 
		.ra(ra)
	);
    always
		  begin
		  clk=0;
		  #5;
		  clk=1;
		  #5;
		  end
		  task re_set();
		  begin
		  @(negedge clk)
		  reset=1'b1;
		  @(negedge clk)
		  reset=1'b0;
		  end
		  endtask
		  task write(input[2:0]m,input n,[15:0]p);
		  begin
		  din=p;
		  wa=m;
		  we=n;
		  end 
		  endtask
		  task read([2:0]k,input l);
		  begin
		  ra=k;
		  re=l;
		  end 
		  endtask
	initial begin
		// Initialize Inputs
		clk = 0;
		din = 0;
		reset = 0;
		we = 0;
		re = 0;
		wa = 0;
		ra = 0;
		// Wait 100 ns for global reset to finish
		#100;
       write(001,1,16'hA);
		 #10;
		 write(111,1,16'h8);
		 #10;
		 write(101,0,16'h1);
		 #10;
		 write(011,1,16'h6);
		 #10;
		 read(001,1);
		 #10;
		 read(111,1);
		 #10;
		 read(101,0);
		 #10;
		 read(011,1);
		 #10;
		// Add stimulus here

	end
      initial begin
        $monitor("clk=%b reset=%b we=%b re=%b wa=%b ra=%b din=%h dout=%h",clk,reset,we,re,wa,ra,din,dout);
      end
      initial begin
       #200 $finish;
      end	
endmodule
```
# Asynchronous dual port 
##[RTL]
```bash
module ram16_8(clk,din,dout,reset,we,re,wa,ra);
parameter WIDTH=8,
          DEPTH=16,
			 add=4;
input re,we,clk,reset;
input [add-1:0]wa,ra;
input [WIDTH-1:0]din;
output reg [WIDTH-1:0]dout;
reg [WIDTH-1:0]mem[DEPTH-1:0];
integer i;
always@(posedge clk or posedge reset)
begin
if(reset)
 begin
 for(i=0;i<DEPTH;i=i+1)
 mem[i]<=0;
     end
else
begin
if(we)
mem[wa]<=din; //if single poet dount<=mem[wa]
    end
end
always@(posedge clk or posedge reset) //depends on only read
begin
if(reset)
begin
dout<=0;
end
else
begin
if(re)
dout<=mem[ra];
end
end
endmodule
```
## Asunchronous single port
### [RTL]
```bash
module asy_single_port(input wr_in,
	input rd_in,
	input [3:0]address,
	input [7:0]data_in,output [7:0]data_out);
	 
	 reg [7:0]mem[15:0];
	 always@(wr_in,rd_in,address,data_in) // not depends on clk
     begin
     if(wr_in&&!rd_in)
     mem[address]=data_in;
     end	
	assign data_out=(rd_in&&!wr_in)? mem[address]:8'hzz;
endmodule
```
## [Test bench]
```bash
module asy_single_port_tb;

	// Inputs
	reg wr_in;
	reg rd_in;
	reg [3:0] address;
   reg [7:0] data_in;
	// Bidirs
	wire [7:0] data_out;

	// Instantiate the Unit Under Test (UUT)
	asy_single_port uut (
		.wr_in(wr_in), 
		.rd_in(rd_in), 
		.address(address), 
		.data_in(data_in),
		.data_out(data_out)
	); 
	task write(input [3:0]addr,input [7:0]data);
	begin 
	 wr_in=1;
	 rd_in=0;
	 address=addr;
	 data_in=data;
	 #10;
	 wr_in=0;
	end
	endtask
	
	task read(input[3:0]b);
	begin 
	 rd_in=1;
	 wr_in=0;
	 address=b;
	 #10;
	 rd_in=0;
	end
	endtask

	initial begin
		// Initialize Inputs
		wr_in = 0;
		rd_in = 0;
		address = 0;
		#100;
        write(4'b0001,8'b10101010);
		  write(4'b0010,8'b11110000);
		  write(4'b0011,8'b00001111);
		  #10;
		  read(4'b0001);
		  read(4'b0010);
		  read(4'b0011);
		  //read(0,4'b0010);
		  #10;
		// Add stimulus here

	end
     initial begin
        $monitor(" wr_in=%b, rd_in=%b,address=%b,data_in%b,data_out=%b ",wr_in,rd_in,address,data_in,data_out);
      end
      initial begin
       #1000 $finish;
      end	 
endmodule
```
## FIFO
### [RTL]
```bash
module FIFO(clk,reset,w_en,wr_data,r_en,data_out,full,empty);
input clk,reset,w_en,r_en;
parameter WIDTH=8,
          DEPTH=16;
input [WIDTH-1:0]wr_data;
output full,empty;
output reg [WIDTH-1:0]data_out;
reg [WIDTH-1:0]mem[DEPTH-1:0];
parameter ADDR=4;
reg [ADDR-1:0]wr_ptr;
reg [ADDR-1:0]rd_ptr;
reg [DEPTH-1:0]count;
assign full=(count==DEPTH-1);
assign empty=(count==0);

always@(posedge clk or posedge reset)
begin
if(reset)
    begin
wr_ptr<=0;
rd_ptr<=0;
data_out<=0;
count<=0;
    end
else 
  begin 
if(w_en && !full)
       begin
		 mem[wr_ptr]<=wr_data;
		 wr_ptr<=wr_ptr+1;
		 count<=count+1;
		 end
if(r_en && !empty)
        begin
        data_out<=mem[rd_ptr];
        rd_ptr<=rd_ptr+1;
        count<=count-1;
        end
  end
end
endmodule		  
```
### [Test bench]
```bash
module FIFO_tb;
	// Inputs
	reg clk;
	reg reset;
	reg w_en;
	reg [7:0] wr_data;
	reg r_en;
	// Outputs
	wire [7:0] data_out;
	wire full;
	wire empty;
	// Instantiate the Unit Under Test (UUT)
	FIFO uut (
		.clk(clk), 
		.reset(reset), 
		.w_en(w_en), 
		.wr_data(wr_data), 
		.r_en(r_en), 
		.data_out(data_out), 
		.full(full), 
		.empty(empty)
	);
	always #5 clk=~clk;
	task write_fifo(input [7:0]q);
	begin
	@(posedge clk)
	 begin
    if(!full)
	 begin
	 w_en=1;
	 wr_data=q;
	 end
	 @(posedge clk)
	 w_en=0;
	 end
	 end
	 endtask
	 task read_fifo();
	begin
	@(posedge clk)
	 begin
    if(!empty)
	 begin
	 r_en=1;
	 end
	 @(posedge clk)
	 r_en=0;
	 end
	 end
	endtask
	initial begin
		// Initialize Inputs
		clk = 0;
		reset = 1;
		w_en = 0;
		wr_data = 0;
		r_en = 0;
		#100;
       reset = 0;
		 write_fifo(8'h1);
		 write_fifo(8'h2);
		 write_fifo(8'h3);
		 write_fifo(8'h4);
		 
		 write_fifo(8'h5);
		 write_fifo(8'h6);
		 write_fifo(8'h7);
		 write_fifo(8'h8);
		 
		 write_fifo(8'h9);
		 write_fifo(8'hA);
		 write_fifo(8'hB);
		 write_fifo(8'hC);
		 
		 write_fifo(8'hD);
		 write_fifo(8'hE);
		 write_fifo(8'hF);
		 write_fifo(8'h1);
		 
	
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 read_fifo();
		 #10;
	end
     initial begin
        $monitor("clk=%b ,reset=%b ,w_en=%b ,wr_data=%h ,r_en=%b ,data_out=%h ,full=%b ,empty=%b ",clk,reset,w_en,wr_data,r_en,data_out,full,empty);
      end
      initial begin
       #1000 $finish;
      end  
endmodule
```
