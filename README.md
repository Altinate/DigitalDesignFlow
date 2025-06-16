# DigitalDesignFlow

```
Functional (e.g., Xcelium, Modelsim, ..)
                    +----------------------+
                    | Design Specification |
                    +----------------------+
                                |
                                v
                    +----------------------+
                    | Architectural Design |
                    +----------------------+
                                |
---------------------------------------------------------------------
Structural (e.g., Genus)        |
                                v
                          +-----------+
                          | Synthesis |
                          +-----------+
                                |
---------------------------------------------------------------------
Physical (e.g., Innovus)        |
                                v
                     +---------------------+
                     | Placement & Routing |
                     +---------------------+
```

## Functional Design
ออกแบบฟังก์ชันการทำงานของระบบ (System Functionality) โดยระบุความสามารถของวงจรดิจิทัลที่ต้องการ เช่น การคำนวณ, การควบคุม, หรือการเชื่อมต่อกับอุปกรณ์ภายนอก โดยไม่ลงรายละเอียดของฮาร์ดแวร์จริง ยังอยู่ในระดับ abstract หรือ behavioral level เช่น การใช้ภาษา HDL อย่าง Verilog/VHDL เพื่อเขียนพฤติกรรมของวงจร
### Design Specification
เป็นเอกสารหรือโมเดลที่กำหนดรายละเอียดเชิงฟังก์ชันทั้งหมดของระบบ เช่น อินพุต, เอาต์พุต, timing requirements, throughput, latency และข้อกำหนดเฉพาะทางอื่น ๆ เช่น พลังงาน, พื้นที่, ความเข้ากันได้กับมาตรฐาน
### Architectural Design
เป็นการกำหนดสถาปัตยกรรมของระบบ โดยแยกการทำงานเป็นบล็อกย่อย เช่น datapath, control path, memory interface, bus architecture เป็นต้น รวมถึงการออกแบบ communication protocol ระหว่างบล็อก และการจัดการ concurrency, pipelining, และ resource sharing


## Structural Design
เป็นการแปลง architectural model ให้เป็นการอธิบายระดับล่างลงในรูปแบบ register-transfer level (RTL) โดยใช้ HDL (เช่น Verilog/VHDL) ระบุโครงสร้างของโมดูล, อินเตอร์เฟซ และการเชื่อมต่อกันระหว่างส่วนต่าง ๆ อย่างเป็นระบบ
### Synthesis
คือขั้นตอนที่นำ RTL code (ที่ผ่านการตรวจสอบและ simulation) มาผ่านกระบวนการ Mapping ไปยัง Standard Cell Library ของเทคโนโลยีการผลิตจริง เช่น 7nm, 14nm หรือ FPGA fabric โดยเครื่องมือ Synthesis (เช่น Design Compiler, Vivado, etc.) จะแปลง logic description ให้กลายเป็น netlist ที่ใช้ standard cells
* มีการปรับ Timing, Area, และ Power ให้เหมาะสมกับ Constraint
* ตรวจสอบ DFT (Design for Test) และ Scan Insertion
* เตรียม Netlist สำหรับ Physical Design


## Physical Design
ขั้นตอนที่นำ Netlist ที่ได้จาก Synthesis มาวางผังจริงของชิป บนพื้นผิวซิลิคอน โดยมุ่งเน้นไปที่ Layout, Timing Closure, Congestion และ Power Distribution
### Floorplan
การวางตำแหน่งเบื้องต้นของโมดูลต่าง ๆ บนชิป กำหนด boundary, I/O pads, macros, power grid และพื้นที่เฉพาะสำหรับโมดูลใหญ่ (hard macros, memory blocks)
* กำหนด aspect ratio ของ die
* วาง IO pin ตาม requirement
* จัดพื้นที่สำหรับ Power/Ground ring
### Placement & Routing
#### Placement
คือการจัดวางตำแหน่งของ standard cells ให้อยู่ภายใน floorplan ที่กำหนด โดยเน้น minimizing wire length และ meeting timing constraints
#### Routing
คือการเชื่อมโยงเส้นทางของสัญญาณไฟฟ้า (nets) ระหว่าง cells ที่ได้จาก placement โดยใช้ metal layers ตาม design rules
* Signal Routing
* Power Routing
* Clock Tree Synthesis (CTS)
* Timing Optimization (ECOs)
