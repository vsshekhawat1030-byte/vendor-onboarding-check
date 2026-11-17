# Vendor Onboarding Desk (Zamp AI Solutions Analyst Case Study — PS-2)

**Live Runnable Demo:** https://vsshekhawat1030-byte.github.io/vendor-onboarding-check/

An end-to-end, live-runnable vendor onboarding and compliance automation engine built for **Problem Statement 2 (Operations: Vendor Onboarding — From Submission to Approval)**.

---

## Overview

Manual vendor onboarding creates operational bottlenecks and exposes procurement teams to payment fraud, compliance violations, and Business Email Compromise (BEC) bank-account hijacks. **Vendor Onboarding Desk** ingests vendor form submissions, JSON bundles, or compliance PDFs, runs them through **7 sequential deterministic and heuristic verification gates**, and produces a clear, reasoned decision (`APPROVED`, `PENDING`, or `REJECTED`) alongside a safe vendor communication draft and a safeguarded Human-in-the-Loop (HITL) review queue.

---

## Core Architecture & 7 Sequential Verification Gates

1. **Completeness & Real Document Text Scanner (`PDF.js`)**
   - Validates all mandatory entity, tax, and banking fields plus required jurisdictional attachments (Certificate of Incorporation, Bank Mandate, GST/VAT Certificate, or IRS Form W-9 for US entities).
   - Extracts raw text from uploaded PDFs via client-side `PDF.js` and scans for compliance document markers vs. wrong-attachment triggers (e.g., catching a vendor who accidentally uploads a Sales Invoice instead of a Tax Certificate) or expired validity dates.
2. **Jurisdiction & OFAC SDN Entity Watchlist Screening**
   - Blocks submissions originating from or banking in sanctioned jurisdictions (`IR`, `KP`, `SY`, `CU`) and screens legal entity and bank holder names against consolidated watchlist entries.
3. **Tax ID Format, State Code & Modulo-36 Check-Digit Validation**
   - Validates country-specific tax formats (`IN` GSTIN, `US` EIN, `GB` VAT, `DE` VAT, `AE` TRN).
   - For Indian GSTINs, verifies the state code (`01`–`38`) against the registered address state, checks the 6th-character PAN entity type (`C` for corporate entities), and computes the official **15th-character Modulo-36 check digit**.
4. **Cross-Document Legal Entity Name Consistency**
   - Normalizes standard corporate suffixes (`Pvt`, `Ltd`, `LLC`, `Inc`, `GmbH`) and enforces strict core-token matching across the submission form, tax certificate, and bank account holder—blocking third-party accounts and unauthorized regional/subsidiary qualifiers.
5. **Banking Checksums & Cross-Border Domicile Check**
   - Runs **ISO 13616 IBAN Modulo-97** checksums (`GB`, `DE`, `AE`), **US Federal Reserve ABA 3-7-1** routing checksums, and **Indian IFSC** validation.
   - Flags cross-border bank accounts (where bank country differs from registration country) for treasury justification.
6. **Corporate Contact & Website Domain Alignment**
   - Rejects free webmail domains (`gmail.com`, `yahoo.com`, etc.) and flags mismatches between the contact email domain and declared company website.
7. **Vendor Master Cross-Check & BEC Bank-Change Guard**
   - **Shared-Bank Fraud Ring Detection:** Immediately rejects submissions whose bank account is already linked to a different vendor in the Vendor Master.
   - **Rejected-Vendor Lock:** Prevents previously rejected vendors from slipping through on resubmission without Compliance Officer intervention.
   - **BEC Bank-Change Protection:** If an existing `APPROVED` vendor submits a new bank account, the active Vendor Master record remains locked to the original verified account until an officer completes out-of-band phone call-back verification.

---

## Safeguarded Human-in-the-Loop (HITL) & Audit Separation

- **External vs. Internal Flag Separation:** Actionable missing items are formatted into a polite vendor follow-up message, while internal fraud and compliance flags (BEC bank-change alerts, domain impersonation notes) are never leaked to the vendor.
- **Mandatory Override Controls:** `PENDING` cases require an audit justification note (and a mandatory out-of-band phone call-back confirmation checkbox for bank-account changes) before updating the Vendor Master.

---

## Repository Files & Test Packets

- **`index.html`** — Self-contained single-file web application (zero external CSS dependencies; clean in-memory session state on load).
- **`Vendor Compliance & Onboarding Packet - Sunrise Packaging.pdf`** — Valid Indian vendor packet (`Sunrise Packaging Pvt Ltd`, GSTIN `27AABCS1234F1ZR`) demonstrating the Happy Path (`APPROVED`)[cite: 17].
- **`Vendor Compliance Packet - Lotus Textiles.pdf`** — Edge-case packet (`Lotus Textiles Pvt Ltd`, GSTIN `24AAACL5678M1Z5`) demonstrating a third-party bank account mismatch (`PENDING`) followed by a corrected resubmission (`APPROVED`, Rev 2)[cite: 17].

### How to Run
Visit the live GitHub Pages deployment at **https://vsshekhawat1030-byte.github.io/vendor-onboarding-check/**[cite: 17] and click **▶ Run 5 Core Demo Scenarios** (or drag and drop the test PDFs into the upload zone) to watch the Happy Path and primary edge cases execute end-to-end.
