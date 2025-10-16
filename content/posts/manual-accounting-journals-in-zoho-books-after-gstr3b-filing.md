---
title: "Manual Accounting Journals in Zoho Books after GSTR-3B Filing"
date: 2025-07-19T02:30:00Z
description: "Maintained by: A small business owner"
categories: ["Accounting"]
tags: [gst, gstr-3b, accounting, zoho books, input tax credit, reverse charge, rounding adjustment, reconciliation]
draft: false
showBreadCrumbs: true
showReadingTime: true
author: "Faisal Darbar"
showToc: true
tocopen: true

cover:
  image: "/images/manual-accounting-journals-in-zoho-books.webp"
  alt: "Manual Accounting Journals"
  caption: "Photo by Jakub Żerdzicki on Unsplash"

---

## 🌟 Purpose

This checklist is for use **after filing GSTR-3B** to create manual journals in **Zoho Books**. These journals ensure your books reflect the actual GSTR-3B return filed — especially handling **reverse charge**, **input credit availability**, and **GSTN rounding adjustments**!

---

## 🗓️ WHEN TO USE THIS

> **Right after filing GSTR-3B** each month  
> All entries are created after filing, but many are **backdated to the last day of the return month** (e.g., June 30 for the June return filed on July 20)

---

## ✅ JOURNAL ENTRIES (With Account Flows & Narrations)

### 🔹 1. Output Taxes Paid or Offset
*Auto-created by Zoho Books*
- **From:** Input CGST / Input SGST / Input IGST and/or Cash  
- **To:** Output CGST / Output SGST / Output IGST  
- **Date:** Same as GSTR-3B filing date

---

### 🔹 2. Reverse Charge ITC Claimed  
**Reverse Charge Tax Input but not due Account → Input Tax Account (CGST/SGST/IGST)**
- **From:** Reverse Charge Tax Input but not due Account  
- **To:** Input CGST / Input SGST / Input IGST (as applicable)  
- **Date:** Last day of the previous month  
- **Narration:** "Reverse Charge for Previous Month"

**Note:** Although this journal is usually created for the current month right after filing GSTR-3B for the previous month, it is preferable to defer creating it until the end of the current month because there may be additional Reverse Charge Tax Input but not due transactions during the month. If the entry is created earlier under the assumption that no further such transactions will occur, the reports and balance sheet will still reflect the amount as on the date of the journal and not as on date. Plus, additional effort may be required to edit the journal if any such transactions arise later.

---

### 🔹 3. Input Tax Moved to Not Due  
**Input Tax Account → Input Tax Credits but not due account**
- **From:** Input CGST / Input SGST / Input IGST  
- **To:** Input CGST but not due / Input SGST but not due / Input IGST but not due  
- **Date:** Last day of the previous month  
- **Narration:** "Deferred ITC – Vendor Not Filed or GSTR-2B Missing"

---

### 🔹 4. Not Due Input Moved to Claimable  
**Input Tax Credits but not due account → Input Tax Account**
- **From:** Input CGST but not due / Input SGST but not due / Input IGST but not due  
- **To:** Input CGST / Input SGST / Input IGST  
- **Date:** Last day of the previous month  
- **Narration:** "Deferred ITC Claimed – Now Available in GSTR-2B"

---

### 🔹 5. Round-Off Input CGST/SGST  
**Input CGST / SGST ↔ Round-off Input CGST / SGST**
- **Direction:** May be credit or debit depending on GSTN rounding  
- **Date:** Last day of the previous month  
- **Narration:** "Input tax round-off adjustment based on GSTR-3B payment"  
- *Discussed and confirmed with Zoho Finance Team via email*

---

### 🔹 6. Round-Off Input IGST  
**Input IGST ↔ Round-off Input IGST**
- **Direction:** May be credit or debit depending on GSTN rounding  
- **Date:** Last day of the previous month  
- **Narration:** "Input tax round-off adjustment based on GSTR-3B payment"  
- *Discussed and confirmed with Zoho Finance Team via email*

---

### 🔹 7. Round-Off Output CGST/SGST  
**Round-off Output CGST / SGST ↔ Output CGST / SGST**
- **Direction:** May be credit or debit depending on GSTN rounding  
- **Date:** Last day of the previous month  
- **Narration:** "Output tax round-off adjustment for GSTR-3B"  
- *Discussed and confirmed with Zoho Finance Team via email*

---

### 🔹 8. Round-Off Output IGST  
**Round-off Output IGST ↔ Output IGST**
- **Direction:** May be credit or debit depending on GSTN rounding  
- **Date:** Last day of the previous month  
- **Narration:** "Output tax round-off adjustment for GSTR-3B"  
- *Discussed and confirmed with Zoho Finance Team via email*

---

### 🔹 9. Other Journals  
As per my current scenario, the above are the only required entries.  

Additional journals may be required if new scenarios arise (e.g., credit notes, advances, corrections, etc.)


