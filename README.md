***Goofy-Gadgets***
# sequential circuits
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

	// Inputs
	reg s;
	reg r;
	// Outputs
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
		  s=0;
      r=1;#10;
      s=0;
      r=0;#10;
		  s=1;
      r=0;#10;
		  s=1;
      r=1;#10;
		  s=0;
      r=1;#10;
      s=0;
      r=0;#10;
		  s=1;
      r=0;#10;
		  s=1;
      r=1;#10;
	end
     initial begin
      #1000$finish;
		end
		initial begin
   $monitor("s=%b,r=%b,q=%b,qb=%b",s,r,q,qb);
      end   
endmodule
```
