# Greed Route — Book Recommendation System

**Data Science for Business Analytics · 2026**

ระบบแนะนำหนังสือ (Book Recommendation System) แบบ end-to-end บนแพลตฟอร์ม **Greed Route** — เว็บแอปพลิเคชันสไตล์ Goodreads รองรับภาษาไทยและภาษาอังกฤษ ครอบคลุมตั้งแต่ database design, synthetic data generation, recommendation algorithms จนถึง web prototype 14 หน้า

Live demo: [github.io](https://khala1391.github.io/DS_RecSys_GoodRead/)
---

## Overview

**Greed Route** เป็น multi-sided platform ที่เชื่อมต่อผู้อ่าน สำนักพิมพ์ และร้านหนังสือ โดยโครงงานนี้พัฒนาระบบแนะนำที่ผสาน 4 อัลกอริทึม:

| Algorithm | CTR | Coverage | จุดเด่น |
| --- | ---: | ---: | --- |
| Collaborative Filtering | 0.24 | 68% | Heavy users (>300 events) |
| Content-Based Filtering | 0.21 | 72% | Cold-start / niche books |
| Popularity-Based | 0.19 | 35% | Fallback เท่านั้น |
| **Hybrid (Best)** | **0.27** | **78%** | ผลลัพธ์ดีที่สุดทุกมิติ |

---

## Dataset

ข้อมูลจำลอง (synthetic) สร้างด้วย Python + Google Gemini API ครอบคลุมช่วง มกราคม 2024 – ธันวาคม 2025

| Entity | ขนาด |
| --- | ---: |
| Users | 1,000 |
| Books | 6,000 |
| Genres (main + subgenre) | 24 + 93 |
| Tags | 57 |
| Publishers | 51 |
| Book Stores | 12 |
| User-Book Events | ~200,000 |
| Recommendation Logs | 50,000 |
| System Events | ~30,000 |

---

## Database Schema

ออกแบบตาม **Star Schema** มี fact table `user_book_event` เป็นศูนย์กลาง ล้อมรอบด้วย dimension tables 17 ตาราง รวม **18 ตาราง**

### Dimension Tables

| Table | แถว | คำอธิบาย |
| --- | ---: | --- |
| `language` | 5 | ภาษา: TH, EN, JA, ZH, KO |
| `device` | 3 | mobile, desktop, tablet |
| `traffic_source` | 9 | แหล่งที่มาของ session |
| `level_of_intent` | 9 | ระดับ engagement (0–8) |
| `discovery_channel` | 8 | ช่องทางค้นพบหนังสือในแพลตฟอร์ม |
| `genre` | 117 | 24 genres + 93 subgenres (hierarchical self-referential FK) |
| `publisher` | 51 | สำนักพิมพ์ไทยและต่างประเทศ |
| `book_store` | 12 | ร้านหนังสือ: SE-ED, Naiin, Kinokuniya, Amazon, Ookbee ฯลฯ |
| `tag` | 57 | bestseller, award-winner, adapted-to-film ฯลฯ |
| `user` | 1,000 | demographic profile ครบถ้วน |
| `book` | 6,000 | title (TH/EN), description, author, price, aggregates |

### Bridge Tables

| Table | คำอธิบาย |
| --- | --- |
| `book_tag` | Many-to-many: book ↔ tag (~24,000 แถว) |
| `user_genre_preference` | Genre preferences จาก onboarding (~2,700 แถว) |
| `publisher_promotion` | โปรโมชันสำนักพิมพ์ (120 แถว) |
| `book_store_promotion` | โปรโมชันร้านหนังสือ (60 แถว) |

### Fact / Event Tables

| Table | แถว | คำอธิบาย |
| --- | ---: | --- |
| `user_book_event` | ~200,000 | Implicit feedback: genre_view → share (9 levels) |
| `recommendation_log` | 50,000 | บันทึกคำแนะนำจาก 4 algorithms |
| `user_system_event` | ~30,000 | Search, filter, session events |

---

## Level of Intent — Engagement Funnel

| Level | Event | ~Count | Drop-off |
| ---: | --- | ---: | ---: |
| 0 | genre_view | 200,000 | — |
| 1 | preview | 140,000 | 30% |
| 2 | view_details | 100,000 | 29% |
| 3 | add_to_wishlist | 52,000 | 48% |
| 4 | read_start | 28,000 | 46% |
| 5 | read_complete | 15,000 | 46% |
| 6 | rate_review | 7,000 | 53% |
| 7 | purchase | 4,500 | 36% |
| 8 | share | 2,800 | 38% |

---

## Web Prototype

Static HTML prototype ที่แสดง recommendation touchpoints ทั่วทั้งแพลตฟอร์ม — 14 หน้า รองรับ bilingual (TH/EN) และ light/dark theme

| หน้า | ไฟล์ | คำอธิบาย |
| --- | --- | --- |
| หน้าแรก | `index.html` | ฟีดข่าว, แนะนำ, สำรวจ, อัปเดต |
| Recommended | `browse-rec.html` | Recommended for You |
| Explore | `browse-explore.html` | Trending / Explore |
| New Releases | `browse-new.html` | หนังสือใหม่ |
| Browse | `browse-list.html` | Browse by genre/list |
| Choice Awards | `browse-choice.html` | โหวตหนังสือยอดเยี่ยม |
| News | `browse-news.html` | ข่าวและบทความ |
| Book Detail | `book-detail.html` | รายละเอียดหนังสือ + แนะนำที่เกี่ยวข้อง |
| My Books | `mybooks.html` | ชั้นหนังสือส่วนตัว |
| Discussions | `community-discuss.html` | กระดานสนทนา |
| Groups | `community-groups.html` | Reading Groups |
| Quotes | `community-quotes.html` | Quotes จากหนังสือ |
| About | `about.html` | เกี่ยวกับแพลตฟอร์ม |
| Contact / FAQ | `contact.html`, `faq.html` | ติดต่อ / คำถามที่พบบ่อย |

---

## Project Structure

```
Y2H203_2603522_RecSys/
├── *.html                     # Web prototype (14 pages)
├── css/style.css              # Global stylesheet
├── js/
│   ├── data.js                # Data loader (genres.json, books.json)
│   └── components.js          # Shared UI components (nav, footer)
├── data/                      # Full synthetic dataset (CSV + JSON, via Git LFS)
├── sample/                    # Dataset samples (smaller CSVs)
├── image/                     # ER diagrams, screenshots, UI assets
├── document/
│   ├── report/                # Project report (markdown chapters)
│   └── IS/                    # Information System documents
├── GreedRoute Report.pdf      # Final compiled report
├── .gitignore
└── .gitattributes
```

---

## Key Findings

- **Hybrid algorithm ให้ผลดีที่สุด** — CTR 0.27, Coverage 78%, Avg Rating 3.8
- **Collaborative เด่นสำหรับ heavy users** (>300 events) — CTR สูงถึง 0.31
- **Content-Based แก้ cold-start** ได้ผลดีสำหรับ new users / new books (Coverage 72%)
- **Popularity-Based ควรใช้เป็น fallback** เท่านั้น — Coverage เพียง 35% เกิด popularity bias รุนแรง
- **Evening peak** 19:00–22:00 — temporal context ที่ควร incorporate ใน production
- **Recommendation feed drive 35% ของ purchases** — ROI สูงสำหรับการลงทุนพัฒนาระบบแนะนำ

---

## Tech Stack

| Layer | Tool |
| --- | --- |
| Database Design | Star Schema · ER diagram (draw.io) |
| Data Generation | Python · Google Gemini API |
| Analysis | Power BI · pandas · numpy |
| Web Prototype | HTML · CSS · JavaScript (static) |
| Version Control | Git · Git LFS (CSV/binary assets) |
| Language | Bilingual: ภาษาไทย / English |

---

## Author

Yuttapong Mahasittiwat — [linkedin.com/in/yuttapong-m](https://www.linkedin.com/in/yuttapong-m/)
