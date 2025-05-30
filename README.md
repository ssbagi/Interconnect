# Interconnect
The Interconnect or Network on Chip used to connect the Multiple Master and Multiple Slave devices in the SoC. The following section gives a brief Overview of Interconnect Fabric which forms the baseline for the Advanced Interconnect i.e., NoC (Network on Chip). 

The content is taken from the ARM Documentation.
Reference Link :
- https://www.arm.com/resources/education/books/fundamentals-soc
- https://www.arm.com/resources/education/books/modern-soc

## Interconnect Basics (Outdated old concept) : Interconnecting Multiple Requestors and Completers

We need to find a way to interconnect Multiple Requestor – Completer pairs together. The Interconnect Fabric is used for connecting Multiple Requestor and Multiple Completer. 

The interconnect fabric solves the requestor–completer connection issue by:
- Associating every requestor with a completer port in the fabric and every completer with a requestor port in the fabric
- Intercepting all of the transactions issued by the requestors and routing them to the appropriate target completer.


The interconnect fabric, which ensures that any transactions are routed to their intended destination.

![image](https://github.com/user-attachments/assets/4cc4b74a-088f-4d8d-8edf-99427b650b6e)

### Importance of Interconnect Fabric
The Interconnect protocol **affects the types of transactions** that can occur between a given requestor completer pair. 

The Interconnect fabric’s implementation affects the **communication patterns** that can occur among different requestor–completer pairs.

The interconnect fabric is, therefore, a **central part of a SoC** and its implementation heavily influences the **system’s performance and flexibility**.


## Address Translation in Multi-completer Systems
- The hypothetical system containing one requestor and four completers, all connected to the interconnect fabric.
- The ADDR bus of each entity is shown below, but each interface has the full set of signals defined by our protocol (ADDR, LENGTH, WR, RD, etc.).
- In the below image Completer 1, Completer 2 has 3 bit so addressing can be upto max 8 registers. The Completer 3 and Completer 4 has 4 bit so addressing can be upto max 16 registers. We can see only few of the registers are being used and few are unmapped.
- When we count the Max Reguisters = 8+8+16+16 = 64. Hence, we set our Requestor Address to be 6 bit. We can even increase this and make it to 7/8/9 based on the requirement of Interconenct or Completor side registers.
- The Completer 1 Address Space : 0x00 : 0x08
- The Completer 2 Address Space : 0x09 : 0x10
- The Completer 3 Address Space : 0x11 : 0x20
- The Completer 4 Address Space : 0x21 : 0x30
- There are many possible address map arrangements for a given system as shown in below figure.
  
![image](https://github.com/user-attachments/assets/1dd6af93-c5d6-4ca1-8a7d-f35761bd1083)

- In below figure the interconnect fabric’s transaction routing functionality through a series of write transactions.
- We assumed the interconnect’s latency as ideal (i.e. with no delay).
- In reality it would involve latency of at least one cycle between the requestor-side signal entering the interconnect fabric, and the translated completer-side signals exiting the fabric.
- From the timing diagram at Time T2 Requestor issues write transaction toward address is 1 so interconnect fabric routes to Completer 1.
- At Time T3 address is A so interconnect fabric routes to Completer 2.
- At Time T4 address is 13 so routed to Completer 3.
- At Time T5 address is 24 so routes to Completer 4.

![image](https://github.com/user-attachments/assets/3a79bf8f-cadf-41e6-a737-26f9cb593795)

### Address Decoding : 
- In the below image the address decoding is shown like for Completer 1 and Completer 2 the 3rd bit of the address decides the given transaction has to be routed to Completer 1 or Completer 2. 
- Similarly for Completer 3 and Completer 4 the 4th and 5th bit decides where the transaction has to be routed. 

- At given time the address 0th bit, 1st bit, 2nd bit are processed parallely. The last three/two bits decide the selection of completer.

![image](https://github.com/user-attachments/assets/b59185a0-fe25-46fe-a3b2-09e8c495980a)

- That without a longest-prefix equality test, it would be possible for multiple downstream completers to match a given address provided by the requestor, and a transaction would therefore be routed simultaneously to more than one completer.

#### Takeway Point
- The Requestor now sees multiple Completer in its address space and must ensure that no transactions it issues will cross the address boundary.
- When we are during Burst transactions the above condition might occur or possibility of crossing the boundary.
- The metadata of the transaction i.e., AxBurst, AxLen and AxSize. These are initialized at the start of transaction. Once, starts the data transfer occurs. 
  - Transaction Size = (AxLen + 1) * (2 ** AxSize).
  - No. of beats = (AxLen + 1)
  - Each beat data transafer = (2 ** AxSize)
-  Once these meta data signals are asserted the completer processes this transfer wiyhout repeated updates.
-  As we know, AHB and AXI are pipelined protocols, where the address and data phases can occur simultaneously. 

### Arbitration in Multi - Requestor System
- SoC systems often have multiple requestors such as CPUs and DMA engines, and it is not uncommon for multiple requestors to wish to communicate with the same set of completers.
- All requestors operate independently of one another and can issue transactions in parallel, including transactions in the same cycle destined for the same completer.
- Each completer has only one interconnect interface port and more than one requestor cannot communicate with it at the same time.
- The interconnect fabric therefore needs to act as an arbiter and sequentialize the requestors’ access to the completer. 


#### Fabric Topology 
- Shared-bus Architectures
  - A Fabric topology in which all requestors and completers share a single set of common interconnect protocol wires (ADDR, LENGTH, WR, RD, etc.) between them.
  - All requestor and completer interconnect interfaces have to be guarded by a tri-state gate because only a single requestor–completer pair can be connected to the bus at any given time.
  - An additional out-of-protocol signal between the requestors and the arbiter for this purpose. The arbiter receives all requestor requests and sequentially grants bus access by enabling the specific requestor and completer’s tri-state gates.
  - The Shared buses don’t support any notion of concurrency.
  - 
  
  ![image](https://github.com/user-attachments/assets/08fbc4bf-439c-4206-964f-31e436d6510f)

- Full Crossbar-switch Architectures
  - The full crossbar interconnects all requestors to all completers by layering a switch in front of all of the completers.
  - SW : Switches.
  - White circle  : Inactive connection.
  - Yellow circle : Active connection.
  - In the below image the left hand side we observe that both the requestor-completer side switch are distinct.
  - In the right hand side we see both the requestor try to communicate with same completer at the same time. We need arbitration logic here.
  - 
  ![image](https://github.com/user-attachments/assets/5132909b-c2ad-4554-afb1-c0f6a511d6f6)

- Full Crossbar-switch Architectures
  













