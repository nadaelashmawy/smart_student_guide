# Smart Student Guide

An intelligent academic advising desktop application for the **Faculty of Artificial Intelligence**.
It helps students check their registration eligibility, search university regulations, and look up
courses, staff members and rooms, while an admin dashboard keeps all the data up to date.

نظام إرشاد أكاديمي ذكي للطلاب: يتحقق من أهلية التسجيل في المواد، ويبحث في لائحة الكلية (عربي وإنجليزي)،
ويعرض المواد وأعضاء هيئة التدريس والقاعات، ومعاه لوحة تحكم للأدمن.

---

## Quick start

**Requirements:** Python 3.9 or newer. No extra packages are needed (standard library only: `tkinter`, `json`, `csv`, `re`, `math`).

```bash
cd Smart_Student_Guide_Final
python main.py        # or: py main.py   /   python3 main.py
```

A launcher window opens with two buttons:

| Button | Who is it for | Notes |
|---|---|---|
| **Open Student Portal** | Students | No login |
| **Open Admin Dashboard** | Administrators | Default password: `admin123` |

To change the admin password, set the environment variable `SSG_ADMIN_PASSWORD` before running.

Try it with the sample students: ID `S101` (Omar Hamed) or `S102` (Menna Ali).

---

## Features

### Student Portal
| Page | What it does |
|---|---|
| **Dashboard** | Overview cards, plus a form to register or update an academic profile (name, ID, CGPA, completed hours, passed courses). |
| **Find Courses** | Search the course catalog by name or code. |
| **Regulations** | Smart search over the regulation documents in Arabic or English, with a filter (All / Regulations / Courses / Staff / Rooms). |
| **Registration Check** | Enter your **Student ID or your Name** (one is enough). Check one specific course or list **all** courses you can register for, with the reasons when you cannot. |
| **Staff Members** | Faculty list: name (Arabic/English), department, courses taught, office, office hours, email. |
| **Rooms & Labs** | Rooms list: building, floor, type, capacity, equipment. |
| **FAQs** | Frequently asked questions. |

### Admin Dashboard
| Page | What it does |
|---|---|
| **Dashboard** | Totals for courses, students, FAQs and documents. |
| **Manage Courses** | Add or update courses (code, name, credits, prerequisites, level, semester). Level (1-4) and semester (1 = Fall, 2 = Spring) are required; the registration check uses the semester. |
| **Students** | Top 3 students by CGPA, plus search by name or ID. |
| **Manage FAQs** | Add or edit FAQs. |
| **Documents** | Upload or edit the `.txt` regulation documents used by the search. |
| **Activity Log** | Every search and eligibility check made by students. |

---

## How it works

The project is split into small modules:

| File | Role |
|---|---|
| `main.py` | Entry point. Launcher window and admin login. |
| `student.py` | Student Portal UI (Tkinter). |
| `admin_ui.py` | Admin Dashboard UI (Tkinter). |
| `kb_engine.py` | **Knowledge-base / rule engine.** Prerequisites, maximum credit hours by CGPA, academic level, graduation-project and summer-training conditions, and the "why can't I register?" explanation. |
| `ir_engine.py` | **Information-retrieval engine.** TF-IDF with cosine similarity. Supports Arabic and English: diacritics and alef normalization, light stemming (e.g. `الغياب` = `غياب`, `hours` = `hour`), and a small list of common student phrases mapped to the terms used in the regulations. |
| `storage.py` | All file access (CSV, JSON, TXT) and query logging. |

### Registration rules (from `data/rules.json`)
- Maximum hours per semester by CGPA: 3.0+ → 21, 2.0+ → 18, 1.0+ → 15, below → 12. A new student with no passed courses gets 18.
- A course is available only if all its prerequisites are passed.
- Graduation project courses (`GP411`, `GP421`) require at least 100 completed hours.
- Completed hours are taken from the student record, or calculated from the credits of the passed courses when missing.

---

## Project structure

```
Smart_Student_Guide_Final/
├── main.py              # launcher
├── student.py           # student UI
├── admin_ui.py          # admin UI
├── kb_engine.py         # rules / eligibility
├── ir_engine.py         # search engine
├── storage.py           # file handling + logging
├── data/
│   ├── courses.csv      # course catalog (63 courses)
│   ├── students.json    # student records (keyed by ID, e.g. S101)
│   ├── staff.json       # faculty members (T01 ...)
│   ├── rooms.json       # rooms and labs (R01 ...)
│   ├── faqs.json        # FAQs
│   ├── rules.json       # registration rules
│   └── documents/       # .txt files searched by the IR engine
│       ├── reg_*.txt      # regulations
│       ├── course_*.txt   # course descriptions
│       ├── staff_*.txt    # staff profiles
│       └── room_*.txt     # room details
└── logs/queries.log     # activity log
```

---

## Managing data

- **Courses:** Admin Dashboard → *Manage Courses*, or edit `data/courses.csv`. Separate prerequisites with `;` (example: `BC111;MA112`).
- **Regulations / documents:** Admin Dashboard → *Documents*, or add a `.txt` file to `data/documents/`. Name files with the prefixes `reg_`, `course_`, `staff_` or `room_` so the search filter can classify them. The search index refreshes automatically when files change.
- **Staff and rooms:** edit `data/staff.json` and `data/rooms.json` (keep the `T01` / `R01` ID format). To make them searchable too, add a matching `staff_*.txt` / `room_*.txt` document.
- **Students:** students can register from the Student Dashboard, or edit `data/students.json`.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `python is not recognized` | Use `py main.py`, or install Python from python.org and tick **Add Python to PATH**. |
| `can't open file 'main.py'` | Run the command from inside the `Smart_Student_Guide_Final` folder. |
| `No module named 'tkinter'` | Re-install Python and tick **tcl/tk and IDLE**. |
| Arabic text looks reversed | This happens on some Linux systems. It displays correctly on Windows and macOS. |
