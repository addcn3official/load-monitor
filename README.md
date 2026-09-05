[Uploading README.md…]()
# Load Monitor — Feeder Load & Province Peak

เว็บแดชบอร์ด static (HTML/CSS/JS ล้วน ไม่มี build step) สำหรับดูโหลดไฟฟ้ารายฟีดเดอร์/สถานี
และค่า peak รายจังหวัด มี 2 หน้า:

- **LOAD01** — แนวโน้มโหลดรายฟีดเดอร์ พร้อม filter (voltage level / substation / feeder / month)
- **PROVINCE PEAK** — แนวโน้ม peak รวม, สรุปการ์ดสถิติ, แผนที่จังหวัด (แบบ SVG จริง ไม่ใช่รูปภาพ),
  กราฟรายจังหวัด และตารางรายละเอียดรายเดือน

## โครงสร้างไฟล์

- `index.html` — ตัวแดชบอร์ดทั้งหมด (ไม่มี dependency ภายนอก, ไม่มี build step)
- `data/province_data.json`, `data/sum_data.json` — ข้อมูลที่หน้าเว็บ fetch ตอน runtime
- `convert_excel.py` — สคริปต์แปลง `ZZZZ.xlsx` (ชีต `SUM` และ `PROVINCE`) ให้เป็นไฟล์ 2 ตัวข้างบน
- `ZZZZ.xlsx` — ไฟล์ Excel ต้นฉบับ (เก็บไว้สำหรับอัปเดตข้อมูลในอนาคต)

เปิด `index.html` ผ่านเว็บเซิร์ฟเวอร์เท่านั้น (เช่น GitHub Pages หรือ
`python3 -m http.server` ตอน dev) เพราะหน้าเว็บใช้ `fetch()` โหลดไฟล์ json — เปิดโดย
double-click ไฟล์ตรงๆ จะติด CORS ของเบราว์เซอร์แล้วขึ้น error "Could not reach the data files"

## วิธีอัปเดตข้อมูลเดือนใหม่

1. อัปเดตข้อมูลในไฟล์ `ZZZZ.xlsx` (ชีต `SUM` และ `PROVINCE` ตามโครงสร้างเดิม — ดูรายละเอียด
   คอลัมน์ที่ต้องมีในคอมเมนต์ต้นไฟล์ `convert_excel.py`)
2. รัน:

   ```bash
   pip install pandas openpyxl   # ครั้งแรกครั้งเดียว
   python3 convert_excel.py ZZZZ.xlsx
   ```

   จะได้ `data/sum_data.json` และ `data/province_data.json` เวอร์ชันใหม่ — **ไม่ต้องแก้ `index.html`**
3. commit + push (ดูขั้นตอนด้านล่าง) — GitHub Pages deploy เวอร์ชันใหม่ให้เองภายใน 1-2 นาที

## วิธีเอาโปรเจกต์นี้ขึ้น GitHub แล้วเปิดเป็นเว็บ (GitHub Pages)

### ขั้นตอนที่ 1 — สร้าง repository บน GitHub

1. ไปที่ [github.com/new](https://github.com/new)
2. ตั้งชื่อ repo เช่น `load-monitor-dashboard`
3. เลือก Public (หรือ Private ถ้ามี plan ที่รองรับ Pages แบบ private)
4. **ไม่ต้อง** ติ๊ก "Add a README file"
5. กด Create repository

### ขั้นตอนที่ 2 — push โค้ดขึ้น GitHub

เปิด terminal ในโฟลเดอร์นี้ แล้วรัน (แทนที่ `<username>` และ `<repo-name>` ด้วยของจริง):

```bash
git init
git add .
git commit -m "Initial commit: load monitor dashboard"
git branch -M main
git remote add origin https://github.com/<username>/<repo-name>.git
git push -u origin main
```

### ขั้นตอนที่ 3 — เปิดใช้งาน GitHub Pages

1. ไปที่หน้า repo บน GitHub → **Settings** → **Pages**
2. **Build and deployment → Source** เลือก **Deploy from a branch**
3. **Branch** เลือก `main`, โฟลเดอร์เลือก `/ (root)` → **Save**
4. รอ 1-2 นาที แล้วรีเฟรชหน้า Settings → Pages จะมีลิงก์
   `https://<username>.github.io/<repo-name>/`

### อัปเดตเว็บภายหลัง

```bash
git add .
git commit -m "Update data for <เดือน>"
git push
```

GitHub Pages จะ deploy เวอร์ชันล่าสุดให้อัตโนมัติทุกครั้งที่ push เข้า `main`
