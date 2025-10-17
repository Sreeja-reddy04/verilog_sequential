# D Flip flop
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
