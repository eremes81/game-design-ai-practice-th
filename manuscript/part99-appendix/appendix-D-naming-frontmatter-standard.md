# ภาคผนวก D. มาตรฐานการตั้งชื่อและ Frontmatter ของเอกสาร R&D

> เวอร์ชันทั่วไปของมาตรฐานการตั้งชื่อและ frontmatter ของเอกสาร R&D ในโปรเจกต์ A ของบริษัท (`_NAMING_FRONTMATTER_STANDARD`)

---

## D.1 มาตรฐานการตั้งชื่อ

### D.1.1 atom

```
<category>_<topic>_<subtopic>.md

ตัวอย่าง:
combat_global_cooldown_constant.md
narrative_voice_profile_K_007.md
ui_button_primary_style.md
```

snake_case พร้อม prefix ของหมวดหมู่

### D.1.2 การ์ดการตัดสินใจ (decision card)

```
D<YEAR>_Q<QUARTER>_<NUMBER>.md

ตัวอย่าง:
D2026_Q2_017.md
```

ปี · ไตรมาส · หมายเลข

### D.1.3 บันทึกการประชุม

```
<category>_<YYYY-MM-DD>[_<seq>].md

ตัวอย่าง:
95_BattleTF_2026-05-18.md
art_review_2026-05-18_1.md
art_review_2026-05-18_2.md
```

### D.1.4 เอกสารข้อกำหนด (spec)

```
spec_<topic>.md

ตัวอย่าง:
spec_combat_global_cooldown.md
spec_guild_attendance.md
```

### D.1.5 รายงาน

```
report_<period>_<type>.md

ตัวอย่าง:
report_W21_alpha_gap.md
report_Q2_user_voice.md
```

---

## D.2 มาตรฐาน Frontmatter

### D.2.1 atom

```yaml
---
name: combat_global_cooldown_constant
description: นิยามค่ามาตรฐานของ global cooldown ในระบบการต่อสู้
type: atom
category: combat
status: active
priority: P0
related_atoms:
  - combat_skill_cooldown_rule
  - combat_healing_skill_cooldown_exception
created: 2026-05-18
last_modified: 2026-05-18
related:
  derives_from: [combat_design_principle]
  affects: [combat_skill_cooldown_rule, ui_skill_cooldown_indicator]
---
```

### D.2.2 การ์ดการตัดสินใจ

```yaml
---
decision_id: D2026_Q2_017
title: รวม global cooldown ของการต่อสู้ให้เป็น 0.5 วินาที
type: system_change
status: active
created: 2026-05-18
created_by: สมาชิกทีม A
approved_by: อี มินซู
scope:
  - combat_system
affected_atoms: [...]
implementation:
  target_build: 2026-05-18
verification:
  layer_1: passed
  layer_2: passed
  layer_3: pending
---
```

### D.2.3 บันทึกการประชุม

```yaml
---
type: meeting_note
category: battle
date: 2026-05-18
attendees: [สมาชิกทีม A, สมาชิกทีม B, อี มินซู]
related_atoms: [...]
---
```

### D.2.4 เอกสารข้อกำหนด

```yaml
---
title: เอกสารข้อกำหนดฟีเจอร์การเช็กชื่อกิลด์
type: spec
priority: P1
target_milestone: MS2
---
```

---

## D.3 ฟิลด์บังคับ vs ฟิลด์ทางเลือก

### D.3.1 ฟิลด์บังคับ

| ชนิดเอกสาร | บังคับ |
|---|---|
| atom | name, description, type, category, status |
| การ์ดการตัดสินใจ | decision_id, title, type, status, created, scope |
| บันทึกการประชุม | type, category, date, attendees |
| เอกสารข้อกำหนด | title, type, priority |

### D.3.2 ฟิลด์ทางเลือก (มีก็ดี)

| ชนิดเอกสาร | ทางเลือก |
|---|---|
| atom | related, last_modified, priority |
| การ์ดการตัดสินใจ | rationale, related_decisions, verification |
| บันทึกการประชุม | related_atoms, sub_topic |
| เอกสารข้อกำหนด | target_milestone, related_atoms |

---

## D.4 การตรวจสอบอัตโนมัติด้วย Lint

```bash
# frontmatter_lint.py

for file in glob("**/*.md"):
    fm = parse_frontmatter(file)
    if not fm:
        warn(f"{file}: ไม่มี frontmatter")
    
    doc_type = infer_type_from_filename(file)
    required = REQUIRED_FIELDS[doc_type]
    
    for field in required:
        if field not in fm:
            warn(f"{file}: ขาดฟิลด์บังคับ {field}")
```

รันอัตโนมัติตอนบิลด์ การละเมิดจะแจ้งเตือน (alert)

---

## D.5 การป้องกันชื่อชนกัน

| ขอบเขต | การป้องกัน |
|---|---|
| atom name | unique ระดับ global |
| decision ID | unique ภายในไตรมาส |
| meeting ID | วันที่ + seq |
| ชื่อไฟล์ | unique ภายในโฟลเดอร์ |

เมื่อชื่อชนกันจะถูกบล็อกอัตโนมัติ

---

## D.6 ขั้นตอนการเปลี่ยนแปลง

### D.6.1 การเปลี่ยนชื่อ atom

```
1. สร้าง atom ด้วยชื่อใหม่
2. อัปเดต wikilink ทั้งหมดของ atom เดิมไปยังชื่อใหม่ (อัตโนมัติ)
3. ทำเครื่องหมาย atom เดิมเป็น deprecated + redirect
4. ย้ายไปยัง _archive หลังผ่านไป 1 เดือน
```

การเปลี่ยนชื่อแบบรีบร้อนเสี่ยงทำให้ข้อมูลเสียหาย

### D.6.2 การเปลี่ยนมาตรฐาน frontmatter

```
1. เสนอเหตุผลของการเปลี่ยนแปลง (ตามขั้นตอน decision)
2. สคริปต์ migration สำหรับเอกสารเดิมทั้งหมด
3. อัปเดต lint ตอนบิลด์
4. แจ้งทีม
```

---

## D.7 ข้อควรทราบสำหรับผู้อ่าน

มาตรฐานนี้เป็นสภาพแวดล้อมของผู้เขียน ผู้อ่านต้องปรับให้เข้ากับสภาพแวดล้อมของตนเอง สาระสำคัญคือ:

| สาระสำคัญ | เหตุผล |
|---|---|
| ความสม่ำเสมอของการตั้งชื่อ | การค้นหา · การทำงานอัตโนมัติ |
| มาตรฐาน Frontmatter | เป็นมิตรกับเครื่องมือ |
| การแยกบังคับ · ทางเลือก | ลดภาระในการเขียน |
| Lint อัตโนมัติ | บังคับใช้มาตรฐาน |
| ขั้นตอนการเปลี่ยนแปลง | ปกป้องข้อมูล |
