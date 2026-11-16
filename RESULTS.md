# UI lab results

Tool parts that I chose: Part 3 Robot Framework and Part 4 Cypress

## Part 0
- Step 0.1, Chrome version: 157.0.8103.52
- Step 0.3, ChromeDriver version: 157.0.8103.78
- Step 0.4, locator of Pizza, Please!: button[name="pizza-please"]
- Step 0.4, locator of Excluded tools: select
- Step 0.4, locator of Must be vegetarian: #default-checkbox
- Step 0.4, locator of Custom Pizza Name: input#pizza-name
- Step 0.5, pizza name, tool and calories of the run by hand: Lab Pizza 20261116-123500, tool Scissors, 550 calories per slice

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1    | Heading read "Looking to break out of your pizza routine?" | Pass |
| 2    | Max Calories per Slice, Excluded tools, Must be vegetarian and Custom Pizza Name appeared | Pass |
| 3    | Fields showed 600, Knife, vegetarian ticked and my pizza name | Pass |
| 4    | Answer showed my pizza name, tool Scissors, 550 calories per slice | Pass |
| 5    | Page said "Please log in first." | Pass |
| 6    | Page showed "Latest pizza recommendations" | Pass |
| 7    | Line showed my pizza name with tool Scissors | Pass |

## Part 3: Robot Framework
- robot --version: 7.5.1
- Step 3.2, failure message for the wrong title: Title should have been 'Web Form' but was 'Web form'.
- Step 3.8, run 1, result, elapsed time and pizza name: PASS, elapsed 00:00:12 , Name : Lab Pizza 20261116-114703
- Step 3.8, run 2, result, elapsed time and pizza name: PASS, elapsed 00:00:12 , Name : Lab Pizza 20261116-114729

## Part 4: Cypress
- node --version, npm --version and npx cypress version: node --version v24.15.0 ,  npm --version 11.12.1 , npx cypress version : Cypress package version: 16.2.1 , Cypress binary version: 16.2.1 , Electron version: 41.7.0 ,Bundled Node version: 24.15.0
- Step 4.3, failure message for the wrong title:  Timed out retrying after 10000ms + expected - actual , -'Web form' +'Web Form'
- Step 4.6, tool and calories in the answer of POST /api/pizza: "tool": "Knife" ,  "calories": 425
- Step 4.7, the line of the admin list that shows your pizza: Lab Pizza 20261116-121239 (tool=Pizza cutter, ingredients_number=7)
- Step 4.8, run 1, result, duration and pizza name: passed, 00:09,Lab Pizza 20261116-122017
- Step 4.8, run 2, result, duration and pizza name: passed, 00:17,Lab Pizza 20261116-122242
- Step 4.9 (optional), browser and result: skipped

## Part B
1. บนหน้าเว็บ QuickPizza ช่องกรอกตัวเลขทั้ง 3 ช่องภายใต้เมนู Advanced (`Max Calories per Slice`, `Min Number of Toppings` และ `Max Number of Toppings`) ใช้แอตทริบิวต์ `id="floating_filled"` ซ้ำกันทั้งหมด ทำให้เมื่อค้นหาด้วย `#floating_filled` จะเจอ Element ถึง 3 ตัวแทนที่จะเป็นตัวเดียว เนื่องจากไม่สามารถใช้ `id` แยกแยะช่องกรอกแคลอรีได้ ทั้ง Robot Framework (`xpath://label[contains(., 'Max Calories')]/../input`) และ Cypress (`cy.contains('label', 'Max Calories per Slice').siblings('input')`) จึงต้องอ้างอิงจากข้อความของ `<label>` ก่อนแล้วค่อยขยับไปยังแท็ก `<input>` ที่อยู่ติดกันแทน หากต้องการให้ชุดทดสอบเรียกใช้ `id` ได้โดยตรง ผู้พัฒนาหน้าเว็บควรกำหนด `id` เฉพาะตัวที่สื่อความหมายให้แต่ละช่อง เช่น `id="max-calories"`, `id="min-toppings"` และ `id="max-toppings"` พร้อมผูกแอตทริบิวต์ `for` บน `<label>` ให้ตรงกัน การเปลี่ยนแปลงนี้ช่วยให้ผู้ทดสอบสามารถเขียน Locator แบบ CSS (`#max-calories`) ที่สั้น รวดเร็ว และเสถียร โดยไม่ต้องพึ่งพาโครงสร้างลำดับชั้นของ DOM (`..` หรือ `siblings()`) ซึ่งอาจพังได้ง่ายหากมีการเปลี่ยนข้อความบน Label หรือปรับเลย์เอาต์ของหน้าเว็บ

2. การตรวจสอบใน Step 7 ของ QP-01 จัดเป็น Consistency Check (Consistency Oracle) เพราะเป็นการเปรียบเทียบความสอดคล้องของข้อมูลระหว่างสองส่วนที่แตกต่างกันภายในระบบเดียวกัน ได้แก่ หน้าแสดงผลคำแนะนำพิซซ่าของฝั่งผู้ใช้ใน Step 4 และรายการในหน้า `/admin` ใน Step 7 จากการรันทดสอบจริง ทั้ง Robot Framework (`Admin List Should Show The Pizza`) และ Cypress (`cy.contains('li', pizzaName).should('contain', '(tool=' + tool + ',')`) ต่างดึงค่าอุปกรณ์ (Tool) ที่สุ่มได้ใน Step 4 มาตรวจสอบเทียบกับรายการ `<li>` บนหน้า Admin เช่น `Lab Pizza 20261116-121239 (tool=Pizza cutter, ingredients_number=7)` ข้อผิดพลาด (Fault) รูปแบบหนึ่งที่การตรวจสอบนี้สามารถตรวจจับได้คือ บั๊กการบันทึกข้อมูลฝั่ง Backend หรือการแมป API ที่ทำให้ `POST /api/pizza` ส่งค่าอุปกรณ์หนึ่งมาแสดงให้ผู้ใช้เห็นในหน้า `#recommendations` (เช่น `Pizza cutter`) แต่กลับบันทึกค่าเริ่มต้นหรือค่าที่ผิดพลาด (เช่น `Knife` ที่ถูกสั่งยกเว้นไว้) ลงในฐานข้อมูลของฝั่งเซิร์ฟเวอร์ นอกจากนี้ ยังช่วยตรวจจับข้อผิดพลาดในการแสดงผลของหน้า `/admin` หากระบบดึงข้อมูล Metadata ของพิซซ่ารายการอื่นมาแสดงปะปนกับชื่อ `Lab Pizza` ที่เพิ่งสร้างขึ้นใหม่

3. เนื่องจากกล่องแสดงผล `#recommendations` บนหน้าเว็บ QuickPizza แสดงเฉพาะชื่อพิซซ่า แป้ง รายการวัตถุดิบ อุปกรณ์ และแคลอรี โดยไม่มีป้ายระบุสถานะ "Vegetarian" แสดงให้เห็นโดยตรง ชุดทดสอบ QP-01 จึงไม่สามารถตรวจเช็กความเป็นมังสวิรัติจากข้อความบน UI เพียงจุดเดียวได้ สำหรับ Cypress วิธีที่แม่นยำที่สุดคือการตรวจสอบ Payload จาก Network Response ของคำขอ `POST /api/pizza` ที่ดักจับไว้ใน Step 4.6 ซึ่งมีฟิลด์ `"vegetarian": true` อยู่ในข้อมูล JSON โดยเพิ่มคำสั่งยืนยัน `expect(response.body.vegetarian).to.be.true` ส่วนการตรวจสอบผ่านหน้าเว็บโดยตรง (เช่นใน Robot Framework) ชุดทดสอบจำเป็นต้องดึงข้อความรายการวัตถุดิบทั้งหมดใต้หัวข้อ `Ingredients:` ภายใน `#recommendations` มาวนลูปตรวจสอบเทียบกับชุดข้อมูลอ้างอิงภายนอกที่เป็นรายชื่อวัตถุดิบต้องห้ามประเภทเนื้อสัตว์และอาหารทะเล (เช่น Pepperoni, Bacon, Ham, Chicken หรือ Anchovies) การใช้ข้อมูลจาก JSON Response ร่วมกับรายการวัตถุดิบอ้างอิงจะช่วยให้มั่นใจได้ทั้งว่าระบบหลังบ้านรับค่าเงื่อนไข `Must be vegetarian` ไปประมวลผลจริง และไม่มีวัตถุดิบที่ไม่ใช่มังสวิรัติหลุดมาแสดงผลบนหน้าจอผู้ใช้

4. ด้านการรอหน้าเว็บโหลด Robot Framework จำเป็นต้องระบุคีย์เวิร์ดรอของ `SeleniumLibrary` (ตั้งค่า `timeout=10s`) อย่างชัดเจนในแต่ละจุด เช่น `Wait Until Element Is Visible css:h1` และ `Wait Until Page Contains Element id:svelte-announcer` ในขณะที่ Cypress จะคอยลองใหม่ (Auto-retry) ให้ทุกคำสั่งอย่าง `cy.get()`, `cy.contains()` และ `.should()` สูงสุด `10000ms` โดยไม่ต้องเขียนคำสั่งรอเอง อีกทั้งยังดักรอผลลัพธ์จาก `POST /api/pizza` ผ่าน `cy.wait('@pizza')` ได้โดยตรง การเขียนชุดทดสอบ `QP-01` ทำได้เร็วกว่าใน Cypress (ใช้เวลารัน `00:09` และ `00:17` ใน Step 4.8) เพราะรูปแบบคำสั่งต่อเนื่องของ JavaScript ไม่ต้องเขียนโค้ดรอซ้ำซ้อนและรันซ้ำทันทีที่บันทึกไฟล์ ส่วน Robot Framework (ใช้เวลา `00:00:12` ทั้งสองรอบใน Step 3.8) ใช้เวลาจัดรูปแบบมากกว่าเนื่องจากต้องเว้นวรรคให้ตรงคอลัมน์และแยกเขียนคีย์เวิร์ดทีละขั้น ด้านความอ่านง่ายของหลักฐานการรัน ไฟล์ `log.html` และ `report.html` ของ Robot Framework แสดงลำดับการทำงานของแต่ละคีย์เวิร์ด ค่าตัวแปรอย่าง `Lab Pizza 20261116-114703` ข้อความ Error เมื่อเทสต์พัง (`Title should have been 'Web Form' but was 'Web form'.`) และภาพหน้าจอ (`qp01-step3-fields.png` ถึง `qp01-step7-admin-list.png`) ไว้อย่างเป็นระเบียบในหน้าเดียว ขณะเดียวกัน วิดีโอ `pizza-scenario.cy.js.mp4` และภาพหน้าจอของ Cypress ก็ช่วยให้ตรวจสอบย้อนหลังได้ชัดเจนยิ่งขึ้น เพราะแสดงแถบ Command Log คู่กับหน้าจอเบราว์เซอร์จริงในทุกจังหวะ พร้อมแสดงผลต่างระหว่างค่าที่คาดหวังกับค่าจริง (`-'Web form' +'Web Form'`) เมื่อเกิด Timeout ครบ `10000ms`

5. สำหรับการทดสอบ UI อัตโนมัติใน Project 2 ผมเลือกใช้ Cypress เป็นเครื่องมือหลัก เหตุผลแรกคือ Cypress ช่วยตัดปัญหาการจัดการเวอร์ชันของ `ChromeDriver` ด้วยตนเอง (เช่น การต้องจับคู่ Chrome `157.0.8103.52` กับ ChromeDriver `157.0.8103.78` ใน Part 0) และลดความซับซ้อนในการเขียนคำสั่งรอด้วยระบบ Auto-retry ภายในตัวสูงสุด `10000ms` รวมถึงมีคำสั่ง `cy.intercept()` ที่ช่วยตรวจสอบความถูกต้องของข้อมูล API (`"tool": "Knife"`, `"calories": 425`) ควบคู่ไปกับหน้า UI ได้ทันที เหตุผลที่สองคือ การเปิดใช้งาน `video: true` ใน `cypress.config.js` สามารถบันทึกวิดีโอการรันทั้งหมด (`pizza-scenario.cy.js.mp4`) ที่แสดงทั้ง Command Log และหน้าจอการทำงานจริงได้โดยอัตโนมัติ ซึ่งตอบโจทย์ข้อกำหนดเรื่องหลักฐานการรันของ Project 2 และช่วยให้ไล่หาสาเหตุของบั๊กได้รวดเร็ว เมื่อรวมกับความสามารถในการสร้าง Custom Command อย่าง `cy.adminLogin` ใน `cypress/support/commands.js` ทำให้ Cypress เขียนได้กระชับและดูแลรักษาโค้ดทดสอบที่มีหลายขั้นตอนได้ง่ายกว่า

6. หาก QuickPizza เปลี่ยน Locator ของช่องกรอก `Max Calories per Slice` ผมจะแก้ไขเพียงจุดเดียวที่ตัวแปร `${MAX CALORIES}` (`xpath://label[contains(., 'Max Calories')]/../input`) ภายในส่วน `*** Variables ***` ของไฟล์ `robot/resources/quickpizza.resource` ภายในไฟล์ `quickpizza.resource` ทั้งคีย์เวิร์ด `Turn On Advanced` (ที่ตรวจเช็กการแสดงผลของช่อง) และคีย์เวิร์ด `Fill In The Pizza Request` (ที่กรอกและตรวจสอบค่า `600`) ต่างเรียกใช้ตัวแปร `${MAX CALORIES}` ร่วมกัน การแก้ค่าที่ตัวแปรนี้จึงอัปเดตการทำงานของทุกขั้นตอนที่เกี่ยวข้องทันที เนื่องจากไฟล์ `pizza_scenario.robot` เรียกใช้งาน `resources/quickpizza.resource` และเรียกใช้เฉพาะคีย์เวิร์ดระดับสูงอย่าง `Turn On Advanced` กับ `Fill In The Pizza Request` โดยไม่มี XPath หรือ CSS Selector ปะปนอยู่ในไฟล์เทสต์เลย จึงไม่จำเป็นต้องแก้ไขโค้ดใน `pizza_scenario.robot` แม้แต่บรรทัดเดียว การแยก Locator และรายละเอียดการโต้ตอบกับหน้าเว็บมาไว้ใน Resource File เช่นนี้ ช่วยจำกัดขอบเขตการแก้ไขเมื่อ UI เปลี่ยนแปลงให้อยู่เพียงจุดเดียวแทนที่จะต้องตามแก้ในทุกไฟล์ทดสอบ

7. ใน Cypress ชุดทดสอบ `QP-01` จำเป็นต้องเขียนรวมอยู่ในบล็อก `it(...)` เดียวกัน เพราะระบบ QuickPizza ทำงานกระจายอยู่บนเซิร์ฟเวอร์หลังบ้านหลายเครื่อง และใช้คุกกี้ (Cookie) ในเบราว์เซอร์เพื่อผูก Session ให้เชื่อมต่อกับเซิร์ฟเวอร์เครื่องเดิมที่บันทึกพิซซ่าไว้ เนื่องจาก Cypress จะล้างคุกกี้ทั้งหมดในเบราว์เซอร์ (รวมถึงตัวแปร Alias ของเทสต์อย่าง `@tool`) โดยอัตโนมัติก่อนเริ่มรันบล็อก `it(...)` ใหม่ทุกครั้ง การแยก Step 6 และ 7 ออกไปเป็นเทสต์ที่สองจะทำให้คุกกี้ระบุเซิร์ฟเวอร์และค่า `pizzaName` กับ `@tool` หายไป ผลที่ตามมาคือเมื่อเทสต์ที่สองเปิดหน้า `/admin` ระบบอาจส่งคำขอไปยังเซิร์ฟเวอร์เครื่องอื่นที่ไม่มีรายการพิซซ่าซึ่งเพิ่งสร้างไว้ใน Step 1–5 อยู่ในหน้า `Latest pizza recommendations` ส่งผลให้คำสั่ง `cy.contains('li', pizzaName)` ใน Step 7 ค้นหารายการพิซซ่าไม่พบและเกิดข้อผิดพลาด Timeout หลังครบ `10000ms` ทั้งที่พิซซ่าถูกสร้างสำเร็จไปแล้วในช่วงแรกของเทสต์
