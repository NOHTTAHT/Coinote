# ใช้ Shortcut อ่านสลิป คู่กับการจดใน Wallet Story

Wallet Story ไม่เปิดให้ Shortcut หรือแอปอื่นเขียนข้อมูลเข้าไป จึงบันทึกเข้า Wallet Story ให้อัตโนมัติไม่ได้ วิธีนี้ทำให้งานจดเหลือแค่ "วางยอดแล้วกดบันทึก"

พอปิดแอปธนาคาร iPhone จะทำ 4 อย่างนี้ให้เอง

1. อ่านสลิปล่าสุดใน Photos (อ่านในเครื่อง)
2. แจ้งเตือนว่าจ่ายเท่าไร ให้ใคร จากบัญชีไหน และหมวดที่แนะนำ
3. คัดลอกยอดเงินไว้ แล้วเปิด Wallet Story ให้ คุณแค่กดวางยอด เลือกหมวด แล้วบันทึก
4. เก็บรายการไว้ใน Coinote ด้วย ใช้เป็นรายการไว้เทียบว่ามีสลิปไหนลืมจดใน Wallet Story

ต้องทำขั้นที่ 1, 2 และ 4.1 ใน `SETUP-TH.md` ก่อน (สร้าง Supabase, ขึ้นเว็บ, ให้แอปธนาคารบันทึกสลิปลง Photos)

---

## ขั้นที่ 1: สร้าง Shortcut "สลิป → Wallet Story"

แอป **Shortcuts (คำสั่งลัด)** → แถบ **Shortcuts** → **+**

1. **Find Photos (ค้นหารูปภาพ)**
   - Add Filter: **Creation Date** · **is in the last** · **5 minutes**
   - Sort by **Creation Date**, **Latest First**, เปิด Limit = **1**
2. **Extract Text from Image (ดึงข้อความจากภาพ)** ใส่ **Photos**
3. **Get Contents of URL (รับเนื้อหาของ URL)** ตั้งค่าเหมือน `SETUP-TH.md` ขั้น 4.2 ข้อ 4 ทุกอย่าง
   - URL จากหน้า ⚙️ ตั้งค่า ใน Coinote
   - Method **POST**
   - Headers `apikey` และ `Content-Type: application/json`
   - Body JSON: `p_token`, `p_text` = **Text**, `p_app` = **Shortcut Input**
4. **Get Dictionary Value**: Get **Value** for key `message` in **Contents of URL**
5. **Show Notification** ใส่ **Dictionary Value**
6. **Get Dictionary Value** อีกอัน: Get **Value** for key `amount` in **Contents of URL**
   (กดที่ตัวแปรแล้วเปลี่ยนชื่อเป็น **ยอดเงิน** จะได้ไม่สับสน)
7. **If (ถ้า)**: **ยอดเงิน** · **has any value (มีค่าใดๆ)**
   - ภายใน If เพิ่ม **Copy to Clipboard (คัดลอกไปยังคลิปบอร์ด)** ใส่ **ยอดเงิน**
   - ต่อด้วย **Open App (เปิดแอป)** เลือก **Wallet Story**
8. ตั้งชื่อ **สลิป → Wallet Story** แล้วกด Done

ข้อ 7 ทำให้ Wallet Story เปิดขึ้นเฉพาะตอนที่อ่านสลิปได้จริง ถ้าเป็นรูปอื่นหรือสลิปซ้ำจะไม่เปิด

> ถ้าไม่อยากให้เก็บรายการไว้ใน Coinote ให้เพิ่มแถว `p_dry` ชนิด **Boolean** = **True** ใน Body ของข้อ 3
> ระบบจะอ่านและแจ้งเตือนอย่างเดียว แต่จะไม่มีรายการไว้เทียบในขั้นที่ 3

## ขั้นที่ 2: ให้รันเองตอนปิดแอปธนาคาร

แอป Shortcuts → **Automation** → **+** → **App**

- เลือก **K PLUS, ttb touch, CIMB THAI, TrueMoney, Shopee**
- ติ๊ก **Is Closed** และเอา Is Opened ออก
- เลือก **Run Immediately** และปิด Notify When Run
- New Blank Automation → **Run Shortcut** → **สลิป → Wallet Story**

ถ้าเคยทำ Automation ของ "Coinote สลิป" ไว้แล้ว ให้แก้ Run Shortcut ในอันเดิมให้ชี้มาที่ Shortcut ใหม่นี้แทน จะได้ไม่อ่านสลิปซ้ำ 2 รอบ
(สำหรับ TrueMoney / Shopee ยังใส่ Input `TrueMoney` / `ShopeePay` ได้เหมือนเดิม)

## ขั้นที่ 3: ใช้งานประจำวัน

1. โอนหรือสแกนจ่ายในแอปธนาคาร แล้วปิดแอป
2. จะเห็นแจ้งเตือน เช่น "บันทึกแล้ว จ่าย ฿65.00 · 7-Eleven · TTB (สำรอง) · ของใช้" และ Wallet Story จะเปิดขึ้นมาเอง
3. ใน Wallet Story กดเพิ่มรายการ → แตะช่องยอดเงินค้างไว้ → **วาง** → เลือกหมวดตามที่แจ้งเตือนบอก → บันทึก

**เช็คว่าลืมจดอะไรไหม:** เปิด Coinote (ไม่ต้องจดอะไรในนั้น) หน้าแรกจะมีทุกสลิปของเดือนแยกตามวัน พร้อมป้าย ⚡ สลิป แล้วเทียบยอดรวมรายวันกับใน Wallet Story ได้เลย

## ถ้ามีปัญหา

| อาการ | ทางแก้ |
|---|---|
| Wallet Story ไม่เปิด | ตรวจข้อ 7 ว่าใช้ key `amount` และ If ตั้งเป็น has any value |
| วางแล้วได้ข้อความยาว ไม่ใช่ตัวเลข | ข้อ 7 ต้องคัดลอก **ยอดเงิน** ไม่ใช่ Dictionary Value ของ message |
| แจ้งเตือนขึ้น 2 ครั้ง | มี Automation 2 อันรันทั้ง "Coinote สลิป" และ Shortcut ใหม่ ให้เหลืออันเดียว |
| ปัญหาอื่น | ดูตารางท้าย `SETUP-TH.md` |
