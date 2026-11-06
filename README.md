# Email Phishing Investigation & Threat Analysis

**A practical SOC L1 email-triage portfolio by Vipin Kumar**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Vipin%20Kumar-blue?logo=linkedin)](https://www.linkedin.com/in/vipin-kumar-530a55283/) [![GitHub](https://img.shields.io/badge/GitHub-vipinsky-black?logo=github)](https://github.com/vipinsky)

This project documents the analysis of **four suspicious email samples** from a SOC Level 1 analyst's perspective. The goal was to practise a structured email investigation: inspect message headers, validate sender authentication, identify social-engineering techniques, examine suspicious links and attachments safely, record indicators of compromise (IOCs), and recommend appropriate response actions.

> **Project report:** Add `Email_Phishing_Investigation_Report_Vipin_Kumar.pdf` to this repository (for example, in a `reports/` folder) and link it here once uploaded.

---

## Why I did this project

Email is a common initial access and social-engineering vector. I created this project to strengthen practical SOC L1 skills beyond simply labelling an email as phishing. I wanted to understand **what makes a message suspicious, which technical evidence supports that assessment, what remains unknown, and what a SOC analyst should do next**.

## Project objectives

- Review raw email headers, including `From`, `Reply-To`, `Return-Path`, `Received`, and message identifiers.
- Interpret SPF, DKIM, and DMARC results in the context of the actual sending domain and the brand being impersonated.
- Identify suspicious language and social-engineering patterns such as unexpected donations, financial refunds, account-verification pressure, and promotional lures.
- Inspect a PDF attachment using static analysis, extract its visible content and embedded URL, and calculate its SHA-256 hash.
- Record useful IOCs and distinguish confirmed observations from assumptions.
- Recommend practical containment, threat-hunting, and incident-response steps.

## Cases investigated

| Case | Email theme | Key observations | Assessment |
|---|---|---|---|
| 01 | **Donation / advance-fee lure** | Unsolicited promise of a USD 10.5 million donation; sender and Reply-To mismatch; SPF softfail, no DKIM, DMARC fail; Microsoft SCL 7. | High-confidence phishing — donation scam pattern |
| 02 | **Banco do Brasil / IRPF refund** | Bank-style display name from a personal Gmail address; PDF impersonating a refund notice; embedded external Azure-hosted URL. SPF, DKIM, and DMARC passed for Gmail, not for the bank. | High-confidence phishing — financial brand impersonation |
| 03 | **Kraken identity verification** | KYC/account-restriction pressure; sender domain and Reply-To do not match Kraken; primary “I agree” action points to an unrelated domain. DKIM lookup timed out. | High-confidence phishing — cryptocurrency brand impersonation |
| 04 | **Wildsino casino promotion** | Unsolicited free-spins offer; SPF fail; DKIM passed for a non-aligned signing domain; DMARC none; inconsistent offer details and external promotional links; Microsoft SCL 8. | High-confidence spam; phishing not confirmed from the supplied evidence |

The classifications reflect the evidence available in the supplied samples. In particular, the Wildsino email is reported as suspicious spam/potential phishing rather than confirmed credential theft because the message alone does not establish credential harvesting or compromise.

## Investigation workflow

The same repeatable workflow was applied across the samples:

1. **Preserve and identify the message** — collect the original email and review its sender, recipient, subject, date, and message ID.
2. **Inspect the headers** — compare `From`, `Reply-To`, and `Return-Path`; review the `Received` chain and identify relevant sending infrastructure.
3. **Validate authentication** — examine SPF, DKIM, and DMARC results, noting which domain they authenticate. A pass result for a sender's domain does not prove that the sender represents the brand shown in the display name.
4. **Review the message content** — look for urgency, unexpected financial rewards, account restrictions, unusual requests, mismatched branding, and suspicious calls to action.
5. **Inspect links and attachments safely** — extract URLs without visiting them; inspect the PDF statically, review its metadata/content, render it for visual inspection, and calculate its SHA-256 hash.
6. **Document IOCs and assess risk** — record relevant sender addresses, domains, URLs, IPs, filenames, hashes, and authentication results. Avoid treating shared mail-provider infrastructure as malicious solely because it appears in a message header.
7. **Recommend response actions** — quarantine the message, search for other recipients, review mail/DNS/proxy/endpoint telemetry, and escalate account-protection actions if user interaction or credential exposure is identified.

## Tools and techniques

- **Email analysis:** raw header review, MIME/email structure inspection, SPF, DKIM, DMARC interpretation
- **Python:** email/MIME parsing and extraction
- **PDF analysis:** PyMuPDF and `pdfinfo` for static inspection and metadata review
- **File analysis:** SHA-256 hashing
- **Link analysis:** URL extraction and safe, defanged IOC documentation
- **SOC investigation concepts:** IOC enrichment, mail-flow review, threat hunting, triage, containment, and incident documentation

## Key findings and lessons learned

- **Authentication is one signal, not a verdict.** SPF, DKIM, and DMARC passing for Gmail did not authenticate the Banco do Brasil branding in the refund email.
- **Compare identity fields.** A mismatch between the visible sender, actual sender domain, and Reply-To address can expose impersonation attempts.
- **Inspect the destination, not just the button text.** A legitimate-looking brand or call-to-action can point to an unrelated domain.
- **Static attachment inspection adds context.** The refund PDF's branding, refund claim, call-to-action button, and embedded external link provided evidence that was not obvious from the email headers alone.
- **Gateway scores can help prioritise triage, but should not replace investigation.** The refund email had a low Microsoft SCL score despite its suspicious content and attachment.
- **Report uncertainty accurately.** A DKIM timeout is inconclusive, and suspicious promotional spam should not be labelled confirmed credential phishing without supporting evidence.

## Outcomes

By completing this project, I practised and documented:

- Email-header and sender-authentication analysis.
- Identification of four different email threat/social-engineering patterns.
- Static PDF inspection, hyperlink extraction, and SHA-256 hashing.
- IOC collection and evidence-based risk classification.
- SOC L1 response recommendations and incident-report writing.
- Clear communication of findings, confidence, and investigation limitations.

## Sample source and attribution

The email samples were sourced from the public **[rf-peixoto/phishing_pot](https://github.com/rf-peixoto/phishing_pot/)** repository.

Sample files reviewed:

- `sample-4.eml`
- `sample-406.eml`
- `sample-20.eml`
- `sample-48.eml`

The samples are used for educational analysis and attribution to their original source is retained here. Refer to the source repository for the original sample files and its licensing/usage terms.

## Scope, limitations, and safe handling

- This is a **retrospective, sample-based educational investigation**, not an analysis of a live enterprise incident.
- Suspicious URLs were extracted and documented in defanged form; they were **not opened** during this analysis.
- No recipient click logs, mailbox audit logs, endpoint telemetry, credential-submission evidence, or sandbox detonation results were available.
- No successful account compromise, malware execution, or financial loss is claimed.
- External reputation results are not claimed unless explicitly recorded in the accompanying report.
- The presence of a shared provider IP (for example, a Google mail relay) is not, by itself, a reason to block that IP.
- Do not open suspicious links or attachments on a normal workstation. Use an approved isolated analysis environment and follow organizational policy.

## Repository structure

A simple layout for publishing the project:

```text
email-phishing-investigation/
├── README.md
└── reports/
    └── Email_Phishing_Investigation_Report_Vipin_Kumar.pdf
```

Only publish sample files or other artifacts when redistribution is permitted. Avoid uploading sensitive recipient information, credentials, or live clickable malicious URLs.

## Resume-ready project description

**Email Phishing Investigation Lab | SOC L1** — Analyzed four suspicious email samples covering donation/advance-fee scams, financial institution impersonation, cryptocurrency KYC lures, and suspicious casino promotions. Reviewed email headers and SPF/DKIM/DMARC results, identified sender and Reply-To mismatches, statically inspected a PDF attachment, extracted an embedded URL, calculated a SHA-256 hash, documented IOCs, and prepared evidence-based incident-response recommendations.

## Author

**Vipin Kumar**  
LinkedIn: [linkedin.com/in/vipin-kumar-530a55283](https://www.linkedin.com/in/vipin-kumar-530a55283/)  
GitHub: [github.com/vipinsky](https://github.com/vipinsky)

---

*This project is intended for cybersecurity learning and portfolio demonstration. All findings are limited to the evidence available in the supplied email samples.*
