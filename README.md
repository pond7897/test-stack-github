# test-stack-github

Repo สำหรับทดลองฟีเจอร์ **Stacked Pull Requests** ของ GitHub (public preview ตั้งแต่ 2026-07-30)

Stacked PR = ชุด PR ที่ต่อกันเป็นชั้น ๆ แต่ละ PR ตั้ง base เป็น branch ของ PR ชั้นล่าง ทำให้แตกงานก้อนใหญ่เป็นชิ้นเล็กที่รีวิวแยกกันได้ โดยไม่ต้อง rebase เองทุกครั้ง

---

## Prerequisites

| ของที่ต้องมี | เวอร์ชัน |
|---|---|
| `gh` (GitHub CLI) | v2.0+ (repo นี้ทดสอบด้วย 2.93.0) |
| `gh-stack` extension | ล่าสุด |

```bash
gh extension install github/gh-stack
gh stack --help
```

> ฟีเจอร์ทยอยเปิดให้ทีละกลุ่ม (gradual rollout) ถ้าคำสั่งใช้ได้แต่ UI บน github.com ยังไม่โชว์ stack map แปลว่า account/org ยังไม่ถึงคิว

---

## Quick start

สร้าง stack 3 ชั้นจาก `main`:

```bash
# ชั้นล่างสุด
git checkout main
gh stack init
gh stack add feat/layer-1
# ...แก้ไฟล์, commit...

# ชั้นที่ 2 ต่อบน layer-1
gh stack add feat/layer-2
# ...แก้ไฟล์, commit...

# ชั้นที่ 3 ต่อบน layer-2
gh stack add feat/layer-3
# ...แก้ไฟล์, commit...

# push ทุก branch + เปิด/อัปเดต PR ทั้ง stack
gh stack submit

# ดูผล
gh stack view
```

---

## Command cheatsheet

### จัดการ stack
| คำสั่ง | ทำอะไร |
|---|---|
| `gh stack init` | เริ่ม stack ใหม่ใน repo ปัจจุบัน |
| `gh stack add <branch>` | เพิ่ม branch ใหม่ทับบนยอด stack |
| `gh stack checkout <n\|PR\|URL\|branch>` | เช็คเอาต์ stack จากเลข stack, เลข PR, URL หรือชื่อ branch |
| `gh stack modify` | ปรับโครงสร้าง stack แบบ interactive (สลับ/ลบชั้น) |
| `gh stack unstack` | เลิกติดตาม stack ทั้ง local และบน GitHub |

### sync กับ remote
| คำสั่ง | ทำอะไร |
|---|---|
| `gh stack rebase` | pull จาก remote แล้ว rebase ไล่ต่อกันทั้ง stack |
| `gh stack push` | push branch ที่ active ใน stack |
| `gh stack submit` | push ทุก branch + สร้าง/อัปเดต PR และ stack บน GitHub |
| `gh stack sync` | fetch + rebase + push + sync สถานะ PR ในคำสั่งเดียว |
| `gh stack link` | ผูก PR ที่มีอยู่แล้วเป็น stack บน GitHub โดยไม่ต้อง track ใน local |

### เดินในกอง stack
| คำสั่ง | ทำอะไร |
|---|---|
| `gh stack up [n]` / `gh stack down [n]` | ขึ้น/ลง n ชั้น (default 1) |
| `gh stack top` / `gh stack bottom` | กระโดดไปชั้นบนสุด/ล่างสุด |
| `gh stack trunk` | กลับไป trunk branch |
| `gh stack switch` | เลือก branch แบบ interactive |
| `gh stack view` | ดู stack ปัจจุบัน |

### merge
| คำสั่ง | ทำอะไร |
|---|---|
| `gh stack merge` | merge PR ในกอง ทีละตัวหรือหลายตัวพร้อมกัน |

---

## Test scenarios

ไอเดียเคสที่ควรลองใน repo นี้:

- [ ] **สร้าง stack พื้นฐาน** — 3 ชั้น, `gh stack submit`, เช็คว่า base branch ของแต่ละ PR ชี้ถูกชั้น
- [ ] **Merge จากยอด** — merge PR ชั้นบนสุด ควรได้ชั้นล่างที่ยังไม่ merge ติดไปด้วยทั้งหมด
- [ ] **Merge จากล่าง** — merge เฉพาะชั้นล่าง ดูว่า PR ชั้นบน rebase + retarget ให้อัตโนมัติจริงไหม
- [ ] **แก้ชั้นกลาง** — commit เพิ่มที่ชั้นกลาง แล้ว `gh stack sync` ดู cascading rebase
- [ ] **Conflict** — จงใจให้ 2 ชั้นแก้บรรทัดเดียวกัน ดูว่า rebase รายงาน conflict ยังไง
- [ ] **`gh stack modify`** — สลับลำดับชั้น / ลบชั้นกลาง
- [ ] **Branch protection** — เปิด required check บน `main` แล้วดูว่ากันการ merge ทั้ง stack ได้ไหม
- [ ] **Web UI** — เปิด PR บน github.com ดู stack map และปุ่ม merge
- [ ] **`gh stack link`** — เปิด PR ธรรมดา 2 ตัวก่อน แล้วค่อยผูกเป็น stack ย้อนหลัง
- [ ] **Merge queue** — support ยัง rolling out อยู่ ลองแล้วบันทึกผลไว้

---

## บันทึกผลทดสอบ

| วันที่ | เคส | ผล | หมายเหตุ |
|---|---|---|---|
| | | | |

---

## ข้อควรรู้ / ข้อจำกัด

- ยังเป็น **public preview** — พฤติกรรมเปลี่ยนได้
- Rollout ทีละกลุ่ม ทั้งฝั่ง UI และ CLI
- **Merge queue** สำหรับ stacked PR ยังทยอยเปิด อาจยังใช้ไม่ได้
- Branch protection และ required checks เดิมยังบังคับใช้ตามปกติ
- รองรับบน GitHub CLI, github.com, GitHub mobile และ coding agent (ผ่าน gh-stack skill)

---

## อ้างอิง

- [Changelog: Stacked pull requests are now in public preview](https://github.blog/changelog/2026-07-30-stacked-pull-requests-are-now-in-public-preview/)
- [github/gh-stack](https://github.com/github/gh-stack)
