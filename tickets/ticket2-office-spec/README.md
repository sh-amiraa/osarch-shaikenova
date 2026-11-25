# Ticket 2 — Kitting out the new office

## 1. What was asked

> From: Daniyar K. (Director)
> Subject: computers for the new office
>
> We are opening the second office and need four computers — accountant, sales, web developer, designer. Budget 3,000,000 tenge for everything. Everyone says we should just buy the same four machines, is that right? Make sure they last a few years. Monitors included.

## 2. What was actually needed

**Four identical machines is the wrong answer.** The four people do very different work, so a different part runs out first for each of them:

| Role | What runs out first |
|---|---|
| Accountant | nothing in the PC is the limit; the risk is losing data (power cuts) and eye strain from 8 hours of numbers |
| Sales manager | memory (30–40 browser tabs + video calls) |
| Web developer | memory and CPU cores (Docker, VMs, builds, several browsers) |
| Designer | colour-accurate monitor and memory (big Photoshop files) |

If we buy four identical machines, there are only two options: make all four strong enough for the developer and the designer (then we pay for 32 GB and a 27" colour-accurate monitor on the accountant's desk, where they do nothing), or make all four "average" (then the developer swaps all day and the designer works with wrong colours). Both waste money. In 2026 memory is the most expensive part in Kazakhstan (348,381 KZT for 32 GB), so putting it where it is not needed hurts the budget a lot.

What makes sense to keep **the same** is the platform: same socket (AM5), same board, same DDR5. That gives easy repairs, spare parts that fit everywhere, and one upgrade path. So the answer to Daniyar is: *same platform, different machines.*

**What the request left out:**

- **Operating system.** "Computers" is not only hardware. A Windows 11 Home licence costs 70,657 KZT per PC (budget.csv). The accountant *must* have Windows (1C, NCALayer for the digital signature, bank clients). The web developer can use Ubuntu 24.04 LTS — free and open-source.
- **Keyboards, mice, cables.** Not mentioned, but nobody can work without them.
- **Headset and webcam** for the sales manager, who has ~4 hours of calls a day.
- **UPS** for the accountant: a power cut while 1C writes to the database can damage it.
- **Office software.** Microsoft 365 / Office is a subscription (yearly cost), not a one-time purchase. I did **not** put it in the 3,000,000 because it is a running cost — Daniyar must know it is extra.
- **Backups.** Nobody asked, but the accountant's data is the most valuable thing in this office.
- **Assembly and delivery.** Someone has to build four PCs and install the OS.
- **"A few years" has no number.** It changes the platform choice.

**What I would ask Daniyar, and what I assumed instead:**

| Question to Daniyar | What I assumed |
|---|---|
| Does "everything" include Windows, Office, keyboards/mice, UPS? | Yes for OS, keyboard/mouse, UPS and headset — they are needed on day one. No for Office (it is a yearly subscription, separate budget). |
| How many years is "a few"? | 4–5 years, so I chose AM5 + DDR5 (upgradeable CPU and memory), not the cheaper AM4 + DDR4. |
| Which accounting software? 1C? | 1C:Accounting for Kazakhstan on Windows, plus salyk.kz / ESF and NCALayer. |
| Does the designer do video or 3D, or only 2D (print, social media)? | Mostly 2D, sometimes short videos. That is why I put money in the monitor and RAM, and left the video card as an upgrade. |
| Does the office need Windows Pro features (domain, remote desktop)? | No — four people, no server, so Windows 11 Home is enough. |
| Can the developer work on Linux? Does the team test on Windows? | Linux (Ubuntu) is fine; Windows testing can be done in a VM or on another PC. |
| Is there a server or cloud for backups and shared files? | No server yet; backups are a separate decision (see section 5). |
| Is the electricity stable in the new office? | Not sure, so at least the accountant gets a UPS. |
| Is there an existing IT company or warranty contract? | No, so I compared self-build vs pre-built (section 3). |
| Can there be a small reserve, or must we spend all 3,000,000? | I wanted ~5–7% reserve, but after DDR5 prices went up only 17,760 KZT (0.6%) is left. Parts must be bought soon after the quote. |

## 3. What I did

1. **Roles → loads.** For each person I wrote what they run, how many hours, how many things at once.
2. **Loads → bottleneck.** For each load I found which resource runs out first (memory, CPU, storage, GPU or screen) and why.
3. **Bottleneck → parts.** I put the money into the bottleneck and saved where the person would not notice. Then I checked compatibility like in Lab 3 (socket, DDR type, M.2, power) and on PCPartPicker.
4. **Prices.** All prices are in tenge, from shops that sell in Kazakhstan, dated 2026-11-25 (one CPU price 2026-11-26). The shop is written after each model in budget.csv; some links go to the e-katalog.kz / alfa.kz page that lists the shop, and the screenshot shows the shop and its price. PCPartPicker was used only for compatibility and power, because it does not show tenge.
5. **Re-check.** When I compared the screenshots with my first table, three things were wrong: the 32 GB kit was now 348,381 KZT (not 230,168), the WD SN850X SSD is no longer sold, and the 8600G was the OEM version without a cooler. I fixed all three: BOX version of the CPU, Kingston NV3 everywhere, cheaper monitors for the developer and the designer, and Windows 11 Home instead of Pro.

The four builds with Load · Bottleneck · Trade-off · Total are in **[builds.md](builds.md)**. All parts with prices, shops and dates are in **[budget.csv](budget.csv)**.

| Role | CPU | RAM | SSD | Monitor | OS | Total, KZT |
|---|---|---|---|---|---|---:|
| Accountant | Ryzen 5 8600G | 16 GB | Kingston NV3 1 TB | Dell P2425H 24" | Win 11 Home | 649,370 |
| Sales | Ryzen 5 8600G | 32 GB | Kingston NV3 1 TB | Dell S2425H 24" | Win 11 Home | 782,387 |
| Web developer | Ryzen 7 7700 | 32 GB | Kingston NV3 1 TB | Dell S2425H 24" | Ubuntu 24.04 | 698,981 |
| Designer | Ryzen 5 8600G | 32 GB | Kingston NV3 1 TB | Dell S2725DS 27" 1440p, 99% sRGB | Win 11 Home | 851,502 |
| **Grand total** | | | | | | **2,982,240** |

Grand total is **2,982,240 KZT ≤ 3,000,000 KZT**, including monitors, operating systems, keyboards/mice, UPS, headset and webcam. Remaining budget: **17,760 KZT** (≈0.6%).

### Answer 1 — Which machine will I upgrade first, 18 months from now, and why?

The **designer's PC**, and the part is a **video card** (an RTX 5060-class card, 8 GB). Right now the designer uses the 8600G's built-in Radeon 760M. It is enough for 2D work in Photoshop and Illustrator, but slow for heavy filters, AI features and video export, and it borrows 2–4 GB from system RAM. I left this out on purpose, and the build is ready for it: free PCIe x16 slot, 550 W PSU (the whole PC with a ~150 W card will use ~300 W). In 18 months we will also know if the designer really does video. The second upgrade is the web developer's memory: add another 2×16 GB in the two free slots → 64 GB.

### Answer 2 — Pre-built vs self-built

I priced four ready PCs from a Kazakhstan system builder (PULSER, sold through Kazakhstan shops) with the closest specs. Full table with links and dates: **[prebuilt.csv](prebuilt.csv)**.

| Role | Pre-built model | Specs | Price, KZT (2026-11-25) | My tower, KZT |
|---|---|---|---:|---:|
| Accountant | PULSER Eco 100 | Ryzen 5 5600GT (AM4), 16 GB DDR4, 1 TB | 235,107 | 443,669 |
| Sales | PULSER Compact 15 | Ryzen 5 5600GT (AM4), 16 GB DDR4, 1 TB | 296,441 | 624,576 |
| Web developer | PULSER Advanced 379 | Ryzen 7 5700X (AM4), 32 GB DDR4, 1 TB, RTX 5060 Ti | 695,423 | 635,806 |
| Designer | PULSER Advanced 377 | Ryzen 5 7500F (AM5), 32 GB DDR5, 1 TB, RTX 5060 | 705,083 | 624,576 |
| **Towers total** | | | **1,932,054** | **2,328,627** |

(Monitors, OS, keyboards and mice, UPS, headset and webcam add the same **653,613 KZT** in both cases. The pre-built PCs come without Windows. With these items the pre-built office would cost **2,585,667 KZT**, leaving **414,333 KZT** — before adding 16 GB to the sales PC.)

**What the company gains with pre-built:** the four towers are **396,573 KZT cheaper**. Most of this difference is memory: the pre-built PCs use DDR4 or were priced before the DDR5 price jump. One shop gives the warranty for the whole PC (not separate warranties for each part); the PC is already assembled and tested — no time spent building, and someone else is responsible if it does not start. Two of them also include video cards.

**What the company loses:** three of the four PCs are on **old AM4 + DDR4**. AM4 gets no new CPUs, so in 3–4 years the only upgrade is a new PC — this is against "last a few years". The sales PC has only 16 GB, so it needs more memory right away. The developer pays for a gaming video card he does not need. The PSU and board brands are not listed in some offers, and some PULSER listings are "wholesale only, from 5 pieces", so buying exactly one of each may not be possible.

**My choice:** self-built on AM5 for the sales manager, the developer and the designer — their loads grow, and they need the upgrade path. But I am honest that for the **accountant** a pre-built PC like the PULSER Eco 100 is good enough: 1C and Excel will not need a faster CPU in five years. If Daniyar prefers, buying that one PC pre-built saves ~208,000 KZT and gives back a real reserve (see section 5).

### Answer 3 — Who will complain first, and about what?

The **designer**, within the first month, about **slow exports and laggy heavy effects** — for example exporting a short video for Instagram, or Photoshop's AI/blur filters on a big file. This is exactly my trade-off: no separate video card, and the built-in GPU takes part of the 32 GB. They will not complain about colours or RAM, because that is where I put the money. If the complaint comes, it confirms that Answer 1 (video card) should come first — there is no money for it in this budget, so it goes to the next budget (or to the money saved if the accountant's PC is bought pre-built).

## 4. Evidence

| File | What it proves |
|---|---|
| [budget.csv](budget.csv) | Every part with price in KZT, the shop link and the date; totals per role and grand total ≤ 3,000,000 |
| [prebuilt.csv](prebuilt.csv) | Prices of four pre-built PCs with links and dates (for Answer 2) |
| [evidence/pcpartpicker-links.md](evidence/pcpartpicker-links.md) | One PCPartPicker list per build: parts are compatible and power is enough (PCPartPicker does not show tenge, it is only for compatibility and wattage) |
| [evidence/vendor-quotes/](evidence/vendor-quotes/) | A screenshot of every price, with the shop's site and the date visible |

## 5. What I would do differently

- **Ask Daniyar before choosing.** The biggest unknown in my plan is the designer: whether they do video decides if the video card is needed now. One short call would remove that guess.
- **Get one quote for all parts from one shop.** I took each part where it was cheapest. That means 5–6 deliveries and 5–6 warranties. Next time I would ask one Kazakhstan shop (for example Shop.kz or White Wind) for a quote for the full list — maybe a bit more expensive, but one invoice, one warranty contact, and they can assemble the PCs.
- **Plan backups in the budget.** I named backups as a gap but did not price them. A 2 TB external drive for the accountant's 1C database (~40,000–60,000 KZT) is needed, but now there is no reserve for it.
- **Consider a mixed plan.** With 2026 memory prices, the accountant's PC could be bought pre-built (PULSER Eco 100, AM4 + DDR4). It saves ~208,000 KZT, which would pay for backups and part of the designer's video card. I kept one platform for all four, but this is a real option for Daniyar.
- **Check the screenshots against the table at once.** My first table had an old memory price and a discontinued SSD; I found it only when I compared the screenshots with budget.csv.
- **Take prices faster.** Memory and SSD prices moved while I was working; I should take all prices and screenshots on the same day at the start, not at the end.
