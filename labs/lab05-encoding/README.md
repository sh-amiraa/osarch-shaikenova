# Lab 5 — Encoding forensics
 
Name: Dinmukhamed Muratbek + Amira · Date: 2026-11-14 · Tools: Arch Linux (fish shell; `file`, `xxd`, `iconv`, `cat`), LibreOffice Calc, Microsoft Excel (Windows)
 
## What I received
 
- **Excel** (double-click): Russian text is readable, but Kazakh letters are replaced with look-alikes — "Әбдірахманова" shows as "Јбдірахманова", "Нұрлан" as "Нўрлан", "Өскемен" as "Ґскемен".
- **LibreOffice Calc**: the import dialog chose **Cyrillic (Windows-1251)** and the separator `;` by default — the same damage as in Excel.
- **Terminal** (`cat`): Cyrillic shows as `�` and garbage, because the terminal expects UTF-8 and the file has one byte per letter. Latin, digits and `;` are fine. A few odd symbols (`͢`, `Ƣ`, `ÿ`, `̴`, `ؼ`) appear exactly where a capital letter is followed by a Kazakh letter: "Нұ" is `CD A2`, which happens to be valid UTF-8 for U+0362.
## Diagnosis (made before the fix)
 
- `file` said: `Non-ISO extended-ASCII text, with CRLF line terminators` (evidence/file-before.txt) — one byte per letter, a legacy code page; `file` cannot tell which one.
- First 16 bytes: `49 44 3b d4 e0 ec e8 eb e8 ff 3b c8 ec ff 3b ce` (evidence/hexdump-before.txt)
- **Encoding: KZ-1048** (called `RK1048` in iconv) — because:
  - "Фамилия" is `d4 e0 ec e8 eb e8 ff`: 7 letters, 7 bytes, so one byte per letter — a Windows-1251-family code page, not UTF-8 (`D0 xx`) and not UTF-16 (`xx 04`);
  - the first name, "Әбдірахманова", starts at offset `0x2A` with `a3 e1 e4 b3 f0 …`: **Ә = A3** and **і = B3**, which are the KZ-1048 positions;
  - `iconv -f WINDOWS-1251` gives "Јбдірахманова", "Ќасымов", "Нўрлан" (in Windows-1251, A3 is "Ј"); `iconv -f RK1048` gives every Russian and Kazakh name correctly.
- **Line ending: CRLF** — because line 1 ends with `0d 0a` at offset `0x24`, and `cat -A` shows `^M$` at the end of every line.
- **BOM: no** — because the file starts with `49 44` ("ID"), not `EF BB BF`, `FF FE` or `FE FF`.
- **Separator:** `;` (byte `3b`).
The same diagnosis is saved in evidence/diagnosis.txt, written before the fix.
 
### Decisions for the fixed file
 
- **BOM: added** (`EF BB BF`). The brief requires the file to open correctly in Excel by double-click. Without a BOM, Excel on Windows reads a CSV in the system code page, and UTF-8 Cyrillic becomes mojibake. LibreOffice and `cat` are not affected by the BOM.
- **Line endings: CRLF kept.** The file is meant for Excel users on Windows, who expect CRLF; LibreOffice and `cat` handle CRLF fine. Keeping it also means the encoding is the only thing I change.
- **Separator: `;` kept.** Excel with Russian/Kazakh regional settings expects `;`. I did **not** add a `sep=;` line, because with it Excel ignores the BOM.
## Fix
 
`scripts/fix.sh`, run from `labs/lab05-encoding/` with `sh scripts/fix.sh`:
 
```sh
#!/bin/sh
# Lab 5: convert staff-broken.csv from KZ-1048 (iconv name RK1048) to UTF-8
in=evidence/staff-broken.csv
out=evidence/staff-fixed.csv
iconv -f RK1048 -t UTF-8 "$in" > "$out"                       # 1. encoding
# 2. line endings: CRLF kept on purpose (Windows/Excel), no change
printf '\357\273\277' | cat - "$out" > tmp && mv tmp "$out"   # 3. add UTF-8 BOM for Excel
```
 
- Step 1 converts KZ-1048 to UTF-8. CR and LF are ASCII bytes (`0D 0A`), the same in both encodings, so CRLF survives unchanged.
- Step 3 puts the three BOM bytes (`\357\273\277` is octal for `EF BB BF`) in front of the converted text. It goes through a temporary file because `cat - "$out" > "$out"` would empty the file before reading it.
- Running the script again is safe: `iconv` recreates the file without a BOM each time, so the BOM is never doubled.
- The original `evidence/staff-broken.csv` is only read, never written; it is also protected with `chmod a-w`. `.gitattributes` contains `*.csv -text`, so Git never rewrites the CRLF bytes of either CSV.
## Verification
 
- `file` now says: `Unicode text, UTF-8 (with BOM) text, with CRLF line terminators` (evidence/file-after.txt).
- The hex dump starts with `ef bb bf 49 44 3b d0 a4 d0 b0 d0 bc …`: the BOM, then "ID;", then "Фам…" as two-byte UTF-8 pairs (`D0 xx` / `D1 xx`).
- evidence/opens-correctly.png shows the fixed file in three applications at once:
  - **LibreOffice Calc**: character set UTF-8, separator `;`;
  - **Excel**: opened by double-click, no import settings;
  - **Terminal**: `cat evidence/staff-fixed.csv`.
  In all three, every name is correct, including the Kazakh letters (Ә, Қ, Ұ, Ү, Ө, Ғ, І) — e.g. "Әбдірахманова", "Нұрлан", "Өмірзақова", "Қарағанды", "Өскемен" — and each field is in its own column.
## What did not work the first time
 
1. **Windows-1251 looked right, but was wrong.** The bytes matched Windows-1251 for Russian letters (а = E0, я = FF), and `iconv -f WINDOWS-1251` ran without a single error. Russian words were perfect, but Kazakh names came out as "Јбдірахманова", "Ќасымов", "Нўрлан". Byte `A3` is "Ј" in Windows-1251 and "Ә" in KZ-1048. A conversion with no error message does not prove the encoding is right; I had to check a Kazakh letter.
2. **Screenshot of the wrong file.** For my first verification screenshot, LibreOffice had `staff-broken.csv` open (with its default Windows-1251), not `staff-fixed.csv`. At first glance it looked fine, because most of the text is Russian. But "Өмірзақова" and "Өскемен" showed as "Ґмірзақова" and "Ґскемен", and the window title showed the wrong file name. I re-took the screenshot with `staff-fixed.csv` opened as UTF-8.
3. **fish is not bash.** My shell is fish, where the guide's `{ …; } > file` syntax does not work. I used `begin; …; end > file` to save the commands together with their output.
