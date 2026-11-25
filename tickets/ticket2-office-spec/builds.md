# Ticket 2 — Four builds

All four machines use the **same platform**: AMD socket **AM5**, board **MSI PRO B650M-P**, **DDR5** memory.
I picked one platform on purpose:

- AM5 is the current AMD socket. AMD says it will get new CPUs for a few more years, so in 2–3 years we can drop in a faster CPU without changing the board or memory. AM4 + DDR4 is cheaper today, but it is a dead end.
- Same board in all four = one BIOS, one set of drivers, spare parts fit every machine. This is the only part where "same for everyone" makes sense.
- What is **different** is where the money goes: memory, SSD, CPU cores and the monitor. That follows the load of each person.

Prices are in KZT from Kazakhstan shops, taken on 2026-11-25. Every row is in [budget.csv](budget.csv).
Compatibility and power were checked on PCPartPicker (links in [evidence/pcpartpicker-links.md](evidence/pcpartpicker-links.md)).

**Important about prices:** in 2026 DDR5 memory became very expensive in Kazakhstan (a 2×16 GB kit costs about 230,000 KZT, more than the CPU and the board together). This changed the whole plan: memory is now the most expensive part, so I give 32 GB only to people whose load really needs it.

---

### Build 1 — accountant

**Load:** 8 hours a day: 1C:Accounting (1C:Бухгалтерия для Казахстана), Excel with big tables, a browser with the tax portal (cabinet.salyk.kz, ESF / e-invoices), bank client, NCALayer for the digital signature (ЭЦП). 3–5 programs open at once, never heavy.

**Bottleneck:** not the CPU and not the memory. 1C and Excel use 1–2 cores and 4–6 GB of RAM. What really "runs out" for an accountant is: (1) **data safety** — a power cut in the middle of saving the 1C database can damage it, and (2) **the screen and the eyes** — 8 hours of small numbers every day. Also the software decides the OS: 1C, NCALayer and bank clients need **Windows**.

**Trade-off:** I took the cheapest memory that meets the rule (16 GB, 2×8 GB in dual channel) and a budget SSD (Kingston NV3). The ~77,000 KZT saved compared to 32 GB went to a **UPS** (APC 650 VA, gives 5–10 minutes to save and shut down) and a **Dell P2425H** monitor with a height and pivot stand, instead of the cheapest 24" one. Windows 11 **Pro**, not Home, because Pro has BitLocker to encrypt the financial data if the PC is stolen. The alternative I did not take: 32 GB of RAM — the accountant would never use it.

**Parts:** Ryzen 5 8600G BOX · MSI PRO B650M-P · 16 GB DDR5-6000 (2×8) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell P2425H 23.8" · Windows 11 Pro · Logitech MK270 · APC BV650I-GR.

The 8600G has a good built-in GPU (Radeon 760M), so no video card is needed.

**Total:** 610,004 KZT (budget.csv, rows 2–12)

---

### Build 2 — sales manager

**Load:** CRM in the browser, 30–40 tabs open all day, WhatsApp Web and Telegram, Excel price lists, about 4 hours of video calls a day (Zoom / Google Meet), often a call and the CRM at the same time.

**Bottleneck:** **memory**. Each Chrome tab takes 150–300 MB; 35 tabs ≈ 7–9 GB, a video call adds 1–2 GB, Windows itself ~3 GB, messengers ~1 GB. That is about 12–14 GB, so 16 GB is full, and when RAM is full Windows starts to swap to the SSD (Lab 6) — the call freezes while the customer is talking. The CPU is mostly idle in this job.

**Trade-off:** same cheap CPU as the accountant (Ryzen 5 8600G — 6 cores are more than enough for tabs and video), but **32 GB** of memory instead of 16. To pay for it I took the cheaper monitor **Dell S2425H** (no height stand, but it has speakers) — the sales manager looks at the CRM, not at colours. I also added a **headset with a noise-cancelling mic** and a **webcam**, because bad sound on a call loses a customer faster than a slow PC. The alternative I did not take: Ryzen 7 — more cores would not help a browser that is waiting for memory.

**Parts:** Ryzen 5 8600G BOX · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell S2425H 23.8" · Windows 11 Pro · Logitech MK270 · Logitech C270 webcam · Logitech H390 headset.

**Total:** 655,941 KZT (budget.csv, rows 13–24)

---

### Build 3 — web developer

**Load:** VS Code, Docker with 3–5 containers (backend, PostgreSQL, Redis, nginx), Node.js builds, 2–3 browsers (Chrome, Firefox, sometimes a mobile emulator) with DevTools, sometimes one virtual machine. All of this runs **at the same time**, the whole day.

**Bottleneck:** **memory first, then CPU cores, then SSD speed.** Rough count: Docker stack 6–8 GB, a VM 4–8 GB, browsers with DevTools 4–6 GB, VS Code + language servers 2 GB, OS 2–3 GB → 18–27 GB. 16 GB would swap all day. When code is built (`npm run build`, `docker build`) all cores work, so 8 cores help. Docker images and `node_modules` are thousands of small files, so a fast SSD with a DRAM cache helps.

**Trade-off:** I gave this person the **only 8-core CPU** (Ryzen 7 7700) and a faster SSD (WD SN850X instead of NV3). The OS is **Ubuntu 24.04 LTS** — it is free and legal (GPL licence), Docker runs natively on Linux without a hidden VM (on Windows, Docker uses WSL2, which eats extra RAM), and production servers are Linux too. This saves ~62,000 KZT for Windows. What I gave up: **64 GB** of memory — another 2×16 GB kit costs ~230,000 KZT, which the budget cannot take now. But the board has **two free memory slots**, so 64 GB is a simple upgrade later. No video card: the 7700 has a small built-in GPU, enough for the monitor. A 27" 1440p monitor, because code + browser side by side need horizontal space.

**Parts:** Ryzen 7 7700 OEM · DeepCool AK400 cooler (OEM CPU comes without one) · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · WD Black SN850X 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell S2725DS 27" 1440p · Ubuntu 24.04 LTS · Logitech MK270.

**Total:** 704,007 KZT (budget.csv, rows 25–35)

---

### Build 4 — designer

**Load:** Photoshop and Illustrator (or Figma) all day, files with many layers 1–3 GB each, sometimes a short video for social media (Premiere / CapCut), a browser with references. Colours must look the same on screen and on print / on the client's phone.

**Bottleneck:** **the monitor first, then memory.** A designer's work is wrong if the colours are wrong — a cheap office monitor shows ~70% sRGB, so the designer picks colours that look different everywhere else. Big Photoshop files and history steps sit in RAM; with 16 GB Photoshop starts using the scratch disk. GPU matters for some effects and for video export, but for 2D design it is not the first limit.

**Trade-off:** the biggest single part of this build is the **ASUS ProArt PA278CV** (27", 1440p, 100% sRGB, factory calibrated, ~213,000 KZT) + 32 GB RAM + the fast SSD (scratch disk). What I gave up: a **separate video card**. The 8600G's Radeon 760M runs Photoshop's GPU features, but it is slower for heavy filters and video export, and it borrows 2–4 GB from system RAM. The alternative I did not take: an RTX 5060 8 GB (~180,000–200,000 KZT) now. I did not take it because (1) we do not know yet if the designer does video at all (question to Daniyar), and (2) memory/SSD prices in Kazakhstan change every month, so I keep a reserve. The PSU (550 W) and the free PCIe x16 slot are ready for that card — it is the first planned upgrade.

**Parts:** Ryzen 5 8600G BOX · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · WD Black SN850X 1 TB · MSI MAG A550BN 550 W · GameMax M60 · ASUS ProArt PA278CV 27" · Windows 11 Pro · Logitech MK270.

**Total:** 816,280 KZT (budget.csv, rows 36–45)

---

## Compatibility check (Lab 3)

| Check | Result |
|---|---|
| CPU socket ↔ board socket | Ryzen 5 8600G and Ryzen 7 7700 are AM5; MSI PRO B650M-P is AM5 ✔ |
| BIOS | Ryzen 8000G needs a 2024+ BIOS. Boards sold now ship with it; I will ask the shop to confirm before buying. |
| Board memory type ↔ memory | Board takes DDR5 only; all kits are DDR5-6000 (EXPO profile; CPU officially 5200, board runs 6000 with EXPO) ✔ |
| Memory slots | 4 slots; we use 2 in every build → 2 free for upgrade ✔ |
| M.2 slot ↔ NVMe SSD | Board has 2× M.2 2280 (PCIe 4.0 x4 on the first); NV3 and SN850X are M.2 2280 PCIe 4.0 NVMe ✔ |
| Video output | 8600G and 7700 have built-in GPUs; board has HDMI + DisplayPort ✔ |
| Case ↔ board | GameMax M60 is mATX; board is mATX ✔ |
| Cooler | 8600G BOX includes Wraith Stealth; 7700 OEM gets DeepCool AK400 (AM5 kit, 155 mm, fits the case) ✔ |
| Total power ↔ PSU | Highest build (web dev) ≈ 150–170 W under full load (PCPartPicker estimate). 550 W PSU = big margin, and enough for a future ~150 W video card in the designer PC ✔ |

## Budget summary

| Role | Total, KZT |
|---|---:|
| Accountant | 610,004 |
| Sales manager | 655,941 |
| Web developer | 704,007 |
| Designer | 816,280 |
| **Grand total** | **2,786,232** |
| Budget | 3,000,000 |
| **Reserve** | **213,768 (≈7%)** |

The reserve is not "free money". It covers: price changes between the quote and the purchase (memory and SSD prices move a lot within a month), delivery, extra cables (HDMI/DP, power strips). If after buying everything the reserve is still there, the first thing to buy is the designer's video card.
