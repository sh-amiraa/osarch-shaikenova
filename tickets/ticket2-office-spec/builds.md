# Ticket 2 — Four builds

All four machines use the **same platform**: AMD socket **AM5**, board **MSI PRO B650M-P**, **DDR5** memory.
I picked one platform on purpose:

- AM5 is the current AMD socket. AMD says it will get new CPUs for a few more years, so in 2–3 years we can drop in a faster CPU without changing the board or memory. AM4 + DDR4 is cheaper today, but it is a dead end.
- Same board in all four = one BIOS, one set of drivers, spare parts fit every machine. This is the only part where "same for everyone" makes sense.
- What is **different** is where the money goes: memory, CPU cores and the monitor. That follows the load of each person.

Prices are in KZT from Kazakhstan shops, taken on 2026-11-25 (the Ryzen 5 8600G on 2026-11-26). Every row is in [budget.csv](budget.csv); the shop is written after the model name, and every price has a screenshot in [evidence/vendor-quotes/](evidence/vendor-quotes/).
Compatibility and power were checked on PCPartPicker (links in [evidence/pcpartpicker-links.md](evidence/pcpartpicker-links.md)).

**Important about prices:** in 2026 DDR5 memory became very expensive in Kazakhstan. A 2×16 GB kit costs **348,381 KZT** — more than the CPU, the board and the SSD together. A 2×8 GB kit costs 167,474 KZT. This changed the whole plan: memory is now the most expensive part of every PC, so I give 32 GB only to people whose load really needs it, and I saved on monitors, SSDs and the OS instead.

---

### Build 1 — accountant

**Load:** 8 hours a day: 1C:Accounting (1C:Бухгалтерия для Казахстана), Excel with big tables, a browser with the tax portal (cabinet.salyk.kz, ESF / e-invoices), bank client, NCALayer for the digital signature (ЭЦП). 3–5 programs open at once, never heavy.

**Bottleneck:** not the CPU and not the memory. 1C and Excel use 1–2 cores and 4–6 GB of RAM. What really "runs out" for an accountant is: (1) **data safety** — a power cut in the middle of saving the 1C database can damage it, and (2) **the screen and the eyes** — 8 hours of small numbers every day. Also the software decides the OS: 1C, NCALayer and bank clients need **Windows**.

**Trade-off:** I took the cheapest memory that meets the rule (16 GB, 2×8 GB in dual channel) and a budget SSD (Kingston NV3). The **180,907 KZT saved** compared to a 32 GB kit paid for a **UPS** (APC 650 VA, gives 5–10 minutes to save and shut down) and a **Dell P2425H** monitor with a height and pivot stand. The OS is **Windows 11 Home**, not Pro: in an office of four people there is no domain server and no remote desktop, so Pro features are not needed; on most new PCs Home can still turn on device encryption to protect the data if the PC is stolen. The alternative I did not take: 32 GB of RAM — the accountant would not use it.

**Parts:** Ryzen 5 8600G BOX (cooler in the box) · MSI PRO B650M-P · 16 GB DDR5-6000 (2×8) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell P2425H 23.8" · Windows 11 Home · Logitech MK270 · APC BV650I-GR.

The 8600G has a good built-in GPU (Radeon 760M), so no video card is needed.

**Total:** 649,370 KZT (budget.csv, rows 2–12)

---

### Build 2 — sales manager

**Load:** CRM in the browser, 30–40 tabs open all day, WhatsApp Web and Telegram, Excel price lists, about 4 hours of video calls a day (Zoom / Google Meet), often a call and the CRM at the same time.

**Bottleneck:** **memory**. Each Chrome tab takes 150–300 MB; 35 tabs ≈ 7–9 GB, a video call adds 1–2 GB, Windows itself ~3 GB, messengers ~1 GB. That is about 12–14 GB, so 16 GB is full, and when RAM is full Windows starts to swap to the SSD (Lab 6) — the call freezes while the customer is talking. The CPU is mostly idle in this job.

**Trade-off:** same cheap CPU as the accountant (Ryzen 5 8600G — 6 cores are more than enough for tabs and video), but **32 GB** of memory instead of 16. To pay for it I took the cheaper monitor **Dell S2425H** (no height stand, but it has speakers) — the sales manager looks at the CRM, not at colours. I also added a **headset with a noise-cancelling mic** and a **webcam**, because bad sound on a call loses a customer faster than a slow PC. The alternative I did not take: Ryzen 7 — more cores would not help a browser that is waiting for memory.

**Parts:** Ryzen 5 8600G BOX · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell S2425H 23.8" · Windows 11 Home · Logitech MK270 · Logitech C270 webcam · Logitech H390 headset.

**Total:** 782,387 KZT (budget.csv, rows 13–24)

---

### Build 3 — web developer

**Load:** VS Code, Docker with 3–5 containers (backend, PostgreSQL, Redis, nginx), Node.js builds, 2–3 browsers (Chrome, Firefox, sometimes a mobile emulator) with DevTools, sometimes one virtual machine. All of this runs **at the same time**, the whole day.

**Bottleneck:** **memory first, then CPU cores.** Rough count: Docker stack 6–8 GB, a VM 4–8 GB, browsers with DevTools 4–6 GB, VS Code + language servers 2 GB, OS 2–3 GB → 18–27 GB. 16 GB would swap all day. When code is built (`npm run build`, `docker build`) all cores work, so 8 cores help.

**Trade-off:** I gave this person the **only 8-core CPU** (Ryzen 7 7700) and 32 GB. The OS is **Ubuntu 24.04 LTS** — it is free and open-source, Docker runs natively on Linux without a hidden VM (on Windows, Docker uses WSL2, which uses extra RAM), and production servers usually run Linux. This saves **70,657 KZT** for a Windows licence. What I gave up:
- **64 GB** of memory — another 2×16 GB kit costs 348,381 KZT, which the budget cannot take now. The board has **two free memory slots**, so 64 GB is a simple upgrade later.
- **A faster SSD.** I planned a WD SN850X (DRAM cache, faster with thousands of small files in `node_modules`), but it is no longer sold in Kazakhstan, so the developer gets the same Kingston NV3 as the others.
- **A 27" 1440p monitor.** After memory prices went up, I moved the 27" monitor to the designer and gave the developer the 24" Dell S2425H. Code + browser side by side still fit on 1920×1080; a second or bigger monitor is a cheap upgrade later.

No video card: the 7700 has a small built-in GPU, enough for the monitor. The 7700 is sold as OEM (no cooler), so it gets a DeepCool AK400.

**Parts:** Ryzen 7 7700 OEM · DeepCool AK400 · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell S2425H 23.8" · Ubuntu 24.04 LTS · Logitech MK270.

**Total:** 698,981 KZT (budget.csv, rows 25–35)

---

### Build 4 — designer

**Load:** Photoshop and Illustrator (or Figma) all day, files with many layers 1–3 GB each, sometimes a short video for social media (Premiere / CapCut), a browser with references. Colours must look the same on screen and on print / on the client's phone.

**Bottleneck:** **the monitor first, then memory.** A designer's work is wrong if the colours are wrong — a cheap office monitor covers only ~70% of sRGB, so the designer picks colours that look different everywhere else. Big Photoshop files and history steps sit in RAM; with 16 GB Photoshop starts using the scratch disk. GPU matters for some effects and for video export, but for 2D design it is not the first limit.

**Trade-off:** the money goes to **32 GB RAM** and a **27" 1440p IPS monitor with 99% sRGB** (Dell S2725DS). My first choice was the ASUS ProArt PA278CV (100% sRGB, factory calibrated, 240,520 KZT), but with the new memory prices it did not fit the budget, and it was out of stock. The Dell covers almost the same sRGB space for ~98,000 KZT less; what we lose is the factory calibration report, so colours should be checked with a calibrator later. What I also gave up: a **separate video card**. The 8600G's Radeon 760M runs Photoshop's GPU features, but it is slower for heavy filters and video export, and it borrows 2–4 GB from system RAM. The PSU (550 W) and the free PCIe x16 slot are ready for a card — it is the first planned upgrade.

**Parts:** Ryzen 5 8600G BOX · MSI PRO B650M-P · 32 GB DDR5-6000 (2×16) · Kingston NV3 1 TB · MSI MAG A550BN 550 W · GameMax M60 · Dell S2725DS 27" 1440p · Windows 11 Home · Logitech MK270.

**Total:** 851,502 KZT (budget.csv, rows 36–45)

---

## Compatibility check (Lab 3)

| Check | Result |
|---|---|
| CPU socket ↔ board socket | Ryzen 5 8600G and Ryzen 7 7700 are AM5; MSI PRO B650M-P is AM5 ✔ |
| BIOS | PCPartPicker warns: the board needs BIOS 7E27v16 or newer for the Ryzen 8000G. Boards sold now usually ship with it; I will ask the shop to confirm before buying. |
| Board memory type ↔ memory | Board takes DDR5 only; all kits are DDR5-6000 (EXPO profile; CPU officially 5200, board runs 6000 with EXPO) ✔ |
| Memory slots | 4 slots; we use 2 in every build → 2 free for upgrade ✔ |
| M.2 slot ↔ NVMe SSD | Board has 2× M.2 2280 (PCIe 4.0 x4 on the first); Kingston NV3 is M.2 2280 PCIe 4.0 NVMe ✔ |
| Video output | 8600G and 7700 have built-in GPUs; board has HDMI + DisplayPort ✔ |
| Case ↔ board | GameMax M60 is mATX; board is mATX ✔ |
| Cooler | 8600G **BOX** includes the Wraith Stealth cooler; 7700 **OEM** has no cooler, so it gets a DeepCool AK400 ✔ |
| Total power ↔ PSU | PCPartPicker estimate: 149–174 W. 550 W PSU = big margin, and enough for a future ~150 W video card in the designer PC ✔ |

## Budget summary

| Role | Total, KZT |
|---|---:|
| Accountant | 649,370 |
| Sales manager | 782,387 |
| Web developer | 698,981 |
| Designer | 851,502 |
| **Grand total** | **2,982,240** |
| Budget | 3,000,000 |
| **Remaining** | **17,760 (≈0.6%)** |

The plan fits the budget, but the reserve is very small. Memory prices in Kazakhstan change every month, so the parts should be bought soon after this quote. If prices go up before the purchase, the first thing I would change is the accountant's PC (see README, section 5).
