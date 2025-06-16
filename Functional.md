# Functional Design
ในขั้นตอนของ Functional Design จะมุ่งเน้นไปที่การกำหนดและตรวจสอบพฤติกรรมของวงจรในระดับ RTL (Register Transfer Level) โดยยังไม่ลงลึกถึงการแมปสู่โครงสร้างฮาร์ดแวร์จริง จุดประสงค์หลักคือการตรวจสอบว่า “สิ่งที่ออกแบบมา ทำงานได้ตรงกับสิ่งที่ต้องการหรือไม่”

## Keyword

| Keyword                                 | คำอธิบาย                                                                                                        |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Elaborate**                           | ขั้นตอนที่เครื่องมือรวมไฟล์ RTL ทั้งหมด สร้าง snapshot ของ design พร้อม hierarchy เตรียมพร้อมสำหรับ simulation           |
| **Simulation**                          | การจำลองพฤติกรรมของวงจรตามเวลา เพื่อทดสอบ correctness ตามที่กำหนดไว้ใน testbench                                |
| **RTL (Register Transfer Level)**       | ระดับการอธิบายวงจรดิจิทัล โดยเน้นที่ flow ของข้อมูลระหว่างรีจิสเตอร์ ผ่าน logic ต่าง ๆ                                     |
| **HDL (Hardware Description Language)** | ภาษาเชิงโครงสร้างสำหรับออกแบบวงจร เช่น Verilog, VHDL                                                           |
| **Testbench**                           | โมดูลสำหรับกระตุ้นสัญญาณอินพุตของ design และตรวจสอบผลลัพธ์                                                      |
| **Waveform**                            | กราฟแสดงสัญญาณดิจิทัลในรูปแบบเวลาจริง ใช้ตรวจสอบลำดับสัญญาณ                                                     |
| **Coverage**                            | การวัดว่าการทดสอบสามารถตรวจสอบเงื่อนไขหรือสถานะของโค้ดได้มากน้อยเพียงใด (เช่น line, toggle, FSM)                |
| **Assertion**                           | การกำหนดเงื่อนไขที่ "ต้องเป็นจริง" ในระหว่าง simulation เพื่อช่วยจับ bug logic                                    |
| **Snapshot**                            | ผลลัพธ์ที่ได้หลัง elaboration เป็นภาพรวมของ design ที่พร้อมจำลอง                                               |
| **xrun**                                | คำสั่งหลักของ Xcelium ที่รวมความสามารถในการ compile, elaborate และ simulate                                    |

## Design Specification
Design Specification คือชุดของข้อกำหนด (Requirements) ที่ระบุไว้อย่างชัดเจนสำหรับวัสดุ ผลิตภัณฑ์ หรือบริการ ซึ่งจะต้องถูกปฏิบัติตามระหว่างกระบวนการออกแบบและพัฒนา

### จุดประสงค์หลัก
* เพื่อกำหนดขอบเขต ความต้องการ และข้อจำกัดของระบบ
* เพื่อเป็นจุดเริ่มต้นและแนวทางให้ทีมออกแบบ (Design Engineers) ใช้ในการเขียน RTL และทำการออกแบบในระดับต่าง ๆ
* เพื่อป้องกันการเบี่ยงเบนจากเป้าหมายทางเทคนิคและฟังก์ชันของระบบ

### ลักษณะของ Design Specification
* อาจอยู่ในรูปแบบ ข้อความ (textual), แผนภาพ (graphical) หรือ แบบจำลองซอฟต์แวร์ (model-based representation)
* เป็นเอกสารที่มี รายละเอียดเชิงลึก เกี่ยวกับพฤติกรรมของระบบ การเชื่อมต่อระหว่างโมดูล และข้อจำกัดด้านการออกแบบ
* มักรวมถึง:
   * รายละเอียดอินพุต/เอาต์พุต
   * ความเร็วในการประมวลผล (latency, throughput)
   * ความเข้ากันได้กับมาตรฐานอุตสาหกรรม
   * เป้าหมายด้านพลังงาน พื้นที่ และความสามารถในการตรวจสอบ (testability)

### ตัวอย่างสิ่งที่ระบุใน Design Specification
| หมวดหมู่             | รายละเอียดตัวอย่าง                                    |
| ------------------- | ----------------------------------------------------- |
| **Functional Spec** | วงจรต้องสามารถทำการคำนวณ FFT 256 จุดภายใน 100 ns      |
| **Interface**       | อินพุต 32-bit, synchronous, ใช้ standard AXI protocol |
| **Performance**     | Latency ไม่เกิน 10 clock cycles                       |
| **Power**           | Active power ไม่เกิน 50 mW                            |
| **Testability**     | รองรับ scan chain 100%, มี BIST สำหรับ memory         |
| **Compliance**      | รองรับมาตรฐาน IEEE 802.3 (สำหรับ Ethernet PHY)        |


## Architectural Design
Architectural Design เป็นขั้นตอนที่กำหนดโครงสร้างระดับสูงของระบบหรือชิป โดยแบ่งออกเป็นบล็อกการทำงานย่อย ๆ (functional blocks) เช่น datapath, control unit, memory interface, และ communication bus พร้อมทั้งกำหนดวิธีการเชื่อมต่อกันของแต่ละบล็อก รวมถึงกลยุทธ์การออกแบบ เช่น pipelining, parallelism และ resource sharing

### จุดสำคัญใน Architectural Design
* กำหนดโมดูลหลักและการแยกหน้าที่ของแต่ละส่วน
* วางโครงสร้าง top-level และ interface ของโมดูล
* ตัดสินใจเรื่อง communication protocols (เช่น AXI, AHB, SPI)
* ออกแบบสถาปัตยกรรมแบบ synchronous/asynchronous
* รองรับการขยายในอนาคต (scalability, modularity)

### เริ่มต้นเขียน Verilog หรือ VHDL
การเขียน RTL code จำเป็นต้องเข้าใจพื้นฐานของภาษา HDL ซึ่งมีอยู่ 2 ภาษาหลักที่ใช้กันอย่างแพร่หลายคือ Verilog และ VHDL










## Xcelium Example Command Usage
Xcelium สามารถเรียกใช้งานได้ทั้งแบบขั้นตอนเดียว (Single-Step) และหลายขั้นตอน (Multi-Step) ขึ้นอยู่กับลักษณะการทำงานและความซับซ้อนของโปรเจกต์

### Single-Step xrun Invocation
```
xrun [options] <source_files>
xrun <source_files> [options] –top [lib.]cell[.view]
```

#### Example: Single-Step xrun Invocation
```
xrun -ieee1364 -v93 -access +r -gui verify.e top.v middle.vhd sub.v
```
* `top.v` และ `sub.v` เป็นไฟล์ Verilog
* `middle.vhd` เป็นไฟล์ VHDL ใช้ตัวเลือก -v93 สำหรับมาตรฐาน VHDL-93
* `verify.e` เป็นไฟล์ e-language สำหรับ verification
* `-access +r` เปิดสิทธิ์ read-only ใน simulation
* `-gui` เรียกใช้โหมดกราฟิก
* เครื่องมือจะทำ **Compile → Elaborate → Simulate** ภายในคำสั่งเดียว

### Multiple-Step xrun Invocation
```
xrun -compile [compilation_options] <source_files>
xrun -elaborate -top [lib.]cell[:view] [elaboration_options]
xrun -R
```

#### Example: Multi-Step xrun Invocation
```
xrun -compile buf.v and2.v -v93 top.vhd
```
* Compile file:
  * Verilog: `buf.v`, `and2.v`
  * VHDL: `top.vhd`

```
xrun -elaborate -elabonly -top top:a
```
* Elaborate:
  * `-elabonly`: ใช้เฉพาะขั้น Elaboration
  * `-top top:a:` ระบุ Top-level cell/view

```
xrun -R
```
* `-R`: เรียก simulation จาก snapshot ล่าสุด

### Example: file list
```
xrun -f filelist.txt -access rwc
```
* ใช้ไฟล์ `filelist.txt` ที่รวมรายชื่อไฟล์ HDL ทั้งหมด

### Getting Help on xrun
```
xrun –helphelp
```
* แสดงรายละเอียด options ทั้งหมดของ `xrun`

