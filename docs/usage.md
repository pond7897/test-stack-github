# Usage

ต่อจาก [Intro](./intro.md)

## ขั้นตอนใช้งาน

```bash
gh stack init docs/intro     # สร้างชั้นแรกจาก trunk
gh stack add docs/usage      # เพิ่มชั้นถัดไปทับบนยอด
gh stack submit              # push + เปิด PR ทั้งกอง
```

## คำสั่งที่ใช้บ่อย

- `gh stack view` — ดูโครงกองปัจจุบัน
- `gh stack up` / `gh stack down` — เดินขึ้นลงระหว่างชั้น
- `gh stack sync` — fetch + rebase + push + sync สถานะ PR
