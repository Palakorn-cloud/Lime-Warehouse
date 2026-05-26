# 📦 คลังปูนขาว — Pallet Manager v7

ระบบจัดการคลังสินค้าปูนขาวแบบ Cloud-first สำหรับโรงงาน  
รันผ่าน **GitHub Pages** + **Google Sheets** เป็นฐานข้อมูลกลาง

---

## ✨ ฟีเจอร์หลัก

| ฟีเจอร์ | รายละเอียด |
|---|---|
| 📊 Dashboard | สรุปสต็อก, กราฟ, แจ้งเตือน 4 ระดับ |
| 📱 Scan QR | บันทึกรับ/เบิกจากมือถือโดยตรง |
| 🔄 Auto-Sync | ดึงข้อมูลจาก Sheets ทุก 30 วินาทีอัตโนมัติ |
| 👁️ Visibility Sync | sync ทันทีเมื่อกลับมาที่หน้าเว็บ |
| 📋 ประวัติ | แสดง 30 วันย้อนหลัง, วันที่ปฏิทินสากล |
| 🔲 QR Generator | สร้าง QR สำหรับพิมพ์ติดพาเลท |
| ⚠️ Clear Stock | ล้างสต็อกทุกพาเลท (ต้องใส่รหัสผ่าน 1234) |

---

## 🗂️ โครงสร้างโปรเจกต์

```
warehouse/
├── index.html          ← warehouse_v7.html (เปลี่ยนชื่อเมื่อ deploy)
├── README.md
└── Code.gs             ← copy ไปวางใน Google Apps Script
```

---

## 🚀 ขั้นตอนการติดตั้ง

### ขั้นที่ 1 — สร้าง Google Sheets + Apps Script

1. เปิด [Google Sheets](https://sheets.google.com) → สร้าง Spreadsheet ใหม่
2. คัดลอกชื่อ Spreadsheet ไว้ (ไม่ต้องสร้าง Sheet ล่วงหน้า — Code.gs จะสร้างให้อัตโนมัติ)
3. เปิดเมนู **Extensions → Apps Script**
4. ลบโค้ดเดิมทิ้งทั้งหมด แล้ววางโค้ดจากส่วน `Code.gs` ในหน้า Settings ของเว็บ
5. กด **Save** (💾)

### ขั้นที่ 2 — Deploy Apps Script เป็น Web App

> ⚠️ **ต้อง Deploy ใหม่ทุกครั้งที่แก้โค้ด** (เลือก New Version)

1. กด **Deploy → New Deployment**
2. ตั้งค่าดังนี้:

| ฟิลด์ | ค่าที่ต้องเลือก |
|---|---|
| Select type | **Web app** |
| Description | (ใส่อะไรก็ได้ เช่น v1) |
| **Execute as** | **Me** (สำคัญมาก) |
| **Who has access** | **Anyone** (สำคัญมาก) |

3. กด **Deploy** → อนุญาต Permission ทุกข้อที่ขอ
4. **คัดลอก Deployment URL** (หน้าตาคล้าย `https://script.google.com/macros/s/AKfycby.../exec`)

### ขั้นที่ 3 — ใส่ URL ในเว็บ

1. เปิดเว็บ → กดแท็บ **⚙️ ตั้งค่า**
2. วาง Deployment URL ในช่อง **Apps Script Deployment URL**
3. กด **💾 บันทึก URL**
4. กด **🔌 ทดสอบ** — ต้องเห็น ✅ เชื่อมต่อสำเร็จ

### ขั้นที่ 4 — Deploy เว็บบน GitHub Pages

1. สร้าง Repository ใหม่บน GitHub
2. เปลี่ยนชื่อไฟล์ `warehouse_v7.html` เป็น `index.html`
3. Push ขึ้น GitHub
4. ไปที่ **Settings → Pages → Source: Deploy from branch → main / root**
5. รอ 1–2 นาที → เว็บจะรันที่ `https://YOUR_USERNAME.github.io/REPO_NAME/`

---

## 📱 วิธีสร้างและใช้ QR Code

1. เปิดเว็บบน **คอมพิวเตอร์** → ไปที่แท็บ **🔲 QR Code**
2. กด **🖨️ พิมพ์** ที่พาเลทที่ต้องการ
3. พิมพ์และติดที่พาเลทจริง
4. พนักงานใช้มือถือ **สแกน QR** → หน้าเว็บจะเปิดตรงไปที่พาเลทนั้นอัตโนมัติ

---

## 🔄 กลไก Auto-Sync (v7)

```
อุปกรณ์ A บันทึกข้อมูล
    │
    ▼
POST → Google Apps Script → เขียนลง Google Sheets
    │
    ├─ อุปกรณ์ A: โหลดข้อมูลกลับจาก Cloud ทันที ✅
    │
    └─ อุปกรณ์ B (ทุกเครื่อง):
           ├─ Auto-Poll ทุก 30 วินาที ← ดึง getStock จาก Sheets
           ├─ Visibility Sync ← เมื่อกลับมาที่แท็บ/เปิดจากแอปอื่น
           └─ Smart Diff ← render ใหม่เฉพาะเมื่อข้อมูลเปลี่ยนจริง
```

**ผลลัพธ์:** Dashboard ของทุกอุปกรณ์จะอัปเดตตรงกันภายใน **≤ 30 วินาที** โดยอัตโนมัติ

---

## 📊 โครงสร้าง Google Sheets

หลังจากมีการบันทึกครั้งแรก Sheets จะมี 2 แผ่น (Sheet) อัตโนมัติ:

### Sheet: `Pallets` (สต็อกปัจจุบัน)
| palletId | qty | updatedAt |
|---|---|---|
| PAL-001 | 35 | 2025-06-01T08:30:00Z |
| PAL-002 | 12 | 2025-06-01T09:15:00Z |

### Sheet: `Transactions` (ประวัติทั้งหมด)
| timestamp | palletId | type | qty | newQty | employee | txId | note |
|---|---|---|---|---|---|---|---|
| 2025-06-01T08:30:00Z | PAL-001 | OUT | 5 | 35 | คุณนะ | TX1234 | |

---

## ⚙️ ค่าที่ปรับแต่งได้ (ในโค้ด)

```javascript
const MAX_BAGS       = 60;   // ความจุสูงสุดต่อพาเลท
const FULL_THRESHOLD = 40;   // ≥ 40 ถุง = สถานะ "เต็ม"
const LOW_THRESHOLD  = 10;   // ≤ 10 ถุง = สถานะ "ใกล้หมด"
const POLL_INTERVAL_MS = 30_000;  // poll ทุก 30 วินาที (ปรับได้)
```

---

## 🔐 รหัสผ่านระบบ

| ฟังก์ชัน | รหัสผ่าน |
|---|---|
| เคลียร์สต็อกทุกพาเลทเป็น 0 | `1234` |

---

## 🛠️ Troubleshooting

| ปัญหา | วิธีแก้ |
|---|---|
| Sync badge แสดง ✗ | ตรวจสอบ URL ใน Settings → กด ทดสอบ |
| ข้อมูลไม่อัปเดต | กด ⟳ รีเฟรชจาก Cloud บน Dashboard |
| QR scan แล้วไม่เลือกพาเลท | รอ 2 วินาทีแล้วลองใหม่ |
| มือถือ cache หน้าเก่า | Chrome → ⋮ → Reload / Clear site data |
| Apps Script Error 403 | Re-deploy ด้วย "New version" และตรวจ Access: Anyone |
| ข้อมูลหาย หลัง Clear Cache | ข้อมูลอยู่ใน Google Sheets ครบ — กด รีเฟรช จะโหลดกลับมา |

---

## 📋 พนักงาน (เริ่มต้น)

คุณนะ, คุณรุ่ง, คุณออดี้, คุณตู่, คุณปุ๊, คุณเมฆ, คุณตี๋, คุณยุทธ, คุณคม, คุณจอย, คุณต้าร์

*(แก้ไขได้ในไฟล์ HTML ที่ `<select id="emp-select">`)*

---

## 🏗️ Tech Stack

- **Frontend**: HTML5 + Vanilla JS + Chart.js + QRCode.js
- **Backend**: Google Apps Script (Web App)
- **Database**: Google Sheets
- **Hosting**: GitHub Pages (static)
- **Offline**: localStorage cache (fallback)
