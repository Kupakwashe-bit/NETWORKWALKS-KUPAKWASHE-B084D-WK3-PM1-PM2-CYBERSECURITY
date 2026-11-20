# Week 3 — Password Cracking Labs (JTR & NetworkWalks Tools)

**Program:** NetworkWalks Cybersecurity & Ethical Hacking Internship (Batch B084)
**Intern:** Kupakwashe Isaac Nyanguru
**Modules Covered:**
- W3-PM1 — Password Cracking with John the Ripper (JTR / Johnny)
- W3-PM2 — Password Cracking with NetworkWalks Online Tools (Hash Calculator + Password Cracker)

---

## 🎯 Objective

Both modules demonstrate the same core concept from two different angles: **recovering a password from a PDF's embedded hash rather than attacking the file directly.**

A locked PDF doesn't store your password in plain text — it stores a cryptographic hash derived from it. To "crack" the file, you first extract that hash, then run it through a tool that hashes candidate passwords (from a wordlist/dictionary) and compares the output until it finds a match. This is the same dictionary-attack principle used by real-world tools like John the Ripper, Hashcat, and hundreds of credential-stuffing attacks seen in breaches today.

> ⚠️ **Scope & Ethics:** Both target files (`My Locked PDF1.pdf`) were provided by NetworkWalks specifically for this lab. No real-world systems, accounts, or third-party data were involved. This repo is for educational purposes only, per the program's liability disclaimer.

---

## 🧪 Lab 1 — W3-PM1: Password Cracking with JTR (John the Ripper / Johnny)

### Tools Used
- **Online HashCrack — PDF Hash Extractor** (`onlinehashcrack.com/tools-pdf-hash-extractor.php`) — browser-based `pdf2john`/`pdf2hashcat`-compatible hash extraction
- **Johnny** — the GUI front-end for John the Ripper

### Process

**Step 1 — Extract the hash from the locked PDF**
Uploaded `My Locked PDF1.pdf` to the Online HashCrack PDF Hash Extractor. The tool parsed the file locally server-side and returned a crackable hash in `pdf2john` format:

```
$pdf$4*4*128*-1028*1*16*ca7f72f11459cba469f1005a8765ed51*32*f32d8fa1bfbe2648226dffc39f7909ea0021446990b9e4114071a4d9104984c1*32*9322f50c29569712067a775264635e4954ccb1b99e209d664984054ffad30a6a
```
📸 `screenshots/JTR/1_HashCalculator.png`
📸 `screenshots/JTR/2_ExtractHash.png`

**Step 2 — Save the hash to a text file**
Copied the full hash string (starting from `$pdf$`) into Notepad and saved it as `HashFinal.txt`, ready for Johnny to ingest.
📸 `screenshots/JTR/3_SaveNotepad.png`

**Step 3 — Load the hash into Johnny**
Opened Johnny → **Open password file** → selected **Open other file format (*2john)** (since the hash is in `pdf2john` format, not a plain PASSWD file) → browsed to `HashFinal.txt`.
📸 `screenshots/JTR/4_OpenPasswordFile.png`
📸 `screenshots/JTR/5_SelectFile.png`

**Step 4 — Run the attack**
Started the attack from Johnny's toolbar. Johnny ran its default wordlist against the hash and cracked it:

| User | Password | Hash (truncated) | Format |
|---|---|---|---|
| ? | **`good-luck`** | `$pdf$4*4*128*-102...` | PDF |

Status bar confirmed: **100% (1/1 cracked, 0 left) [format=PDF]**
📸 `screenshots/JTR/6_Cracked.png`

**Step 5 — Unlock the PDF**
Opened `My Locked PDF1.pdf`, entered `good-luck` at the password prompt.
📸 `screenshots/JTR/7_Input.png`

**Result:** File opened successfully — flag captured: `nw{cybersecurity_flag_captured_2608}`
📸 `screenshots/JTR/8_Opened.png`

---

## 🧪 Lab 2 — W3-PM2: Password Cracking with NetworkWalks Tools

### Tools Used
- **NetworkWalks Hash Calculator** (`networkwalks.com/hash-calculator`)
- **NetworkWalks Password Cracker — Dictionary Attack Lab** (`networkwalks.com/password-cracker`)

Both tools run entirely client-side in the browser — no file is ever uploaded to a server, which is a meaningful difference from Lab 1's approach.

### Process

**Step 1 — Extract the hash**
Opened the Hash Calculator, switched to the **PDF** tab, and selected `My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`. The tool parsed the file locally in-browser and returned:

```
$pdf$4*4*128*-1060*1*16*55d1a5c14175da449753199e44971d32*32*777fd021a7f3c5ae598c0c6495c7f76e00000000000000000000000000000000*32*ceecdac74b19b5a62688d3b3524e1374c955cbb9cc3c45316494d9446ef81af1
```
Metadata confirmed: Revision R4, Version V4, Key length 128-bit.
📸 `screenshots/NWT/1_HashCalculator.png`
📸 `screenshots/NWT/2_SelectPDF.png`
📸 `screenshots/NWT/3_CopyHash.png`

**Step 2 — Save the hash**
Pasted the hash into Notepad for safekeeping before running the crack.
📸 `screenshots/NWT/4_PasteAndSave.png`

**Step 3 — Run the dictionary attack**
Opened the NetworkWalks Password Cracker, pasted the hash, and launched the attack. The tool worked through a 100-word dictionary at ~9 passwords/second, trying common passwords (`1q2w3e4r`, `baseball`, `michael`, `jordan`...) until it hit a match at attempt 91/100:

**Password cracked: `password1`**
📸 `screenshots/NWT/5_PasteAndCrack.png`

**Step 4 — Unlock the PDF**
Entered `password1` at the PDF's password prompt.
📸 `screenshots/NWT/6_EnterPassword.png`

**Result:** File opened successfully — flag captured: `nw{networkwalks_flag1_jtr_270521_1}`
📸 `screenshots/NWT/7_CrackedAndOpened.png`

---

## 🔍 Comparing the Two Methods

| | JTR / Johnny | NetworkWalks Tools |
|---|---|---|
| Hash extraction | Third-party online extractor (server-side) | NetworkWalks Hash Calculator (fully client-side) |
| Cracking engine | John the Ripper (local, industry-standard) | Browser-based dictionary attack |
| Processing location | Hash cracked locally on my machine | Hash cracked locally in-browser (JS) |
| Speed/transparency | Showed cracked status + format in a results table | Showed live attempt log + attempts/success rate |
| Cracked password | `good-luck` | `password1` |

**Key takeaway:** Despite different tools and interfaces, both attacks follow the identical underlying logic — hash extraction, then a dictionary attack against that hash. JTR is the real-world, industry-standard tool professionals actually use in authorized penetration tests; the NetworkWalks tools are a simplified, visual way to understand the same mechanism without installing anything.

---

## 💡 Why This Matters (Security Takeaway)

Both passwords cracked here — `good-luck` and `password1` — are weak by design, to demonstrate the lesson quickly. In the real world:
- `password1`-style passwords are among the [most commonly breached passwords globally](https://www.security.org), often cracked in under a second offline.
- A short, dictionary-word-based password (even with a number appended) falls to this exact attack method in seconds to minutes.
- A long, random, 12+ character password with mixed case/numbers/symbols would resist this same dictionary attack for years, because it isn't in any wordlist and brute-forcing the keyspace becomes computationally infeasible.

This is the practical argument for passphrases over passwords, and for password managers over memorable (and therefore guessable) patterns.

---

## 📁 Repo Structure

```
├── README.md
├── screenshots/
│   ├── JTR/
│   │   ├── 1_HashCalculator.png
│   │   ├── 2_ExtractHash.png
│   │   ├── 3_SaveNotepad.png
│   │   ├── 4_OpenPasswordFile.png
│   │   ├── 5_SelectFile.png
│   │   ├── 6_Cracked.png
│   │   ├── 7_Input.png
│   │   └── 8_Opened.png
│   └── NWT/
│       ├── 1_HashCalculator.png
│       ├── 2_SelectPDF.png
│       ├── 3_CopyHash.png
│       ├── 4_PasteAndSave.png
│       ├── 5_PasteAndCrack.png
│       ├── 6_EnterPassword.png
│       └── 7_CrackedAndOpened.png
```

---

## 📜 Disclaimer

These labs were performed exclusively on files provided by NetworkWalks Academy for authorized educational purposes, as part of the NetworkWalks Cybersecurity & Ethical Hacking Internship Program. No real systems, credentials, or third-party data were accessed. All concepts demonstrated here are intended to improve defensive password security practices.

---

*Submitted by Kupakwashe Isaac Nyanguru — Batch B084*
