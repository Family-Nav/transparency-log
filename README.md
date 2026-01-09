# Transparency Log — FamilyNav

This document explains the public transparency log used by FamilyNav from the point of view of a report recipient, attorney, or court official. It describes what the log is, why it exists, what is published, and simple step‑by‑step instructions to independently verify a report's existence and integrity.

Short summary
- FamilyNav cryptographically signs each generated report and registers a small, non‑sensitive record of that report in a public transparency log.
- The log is published as timestamped batch files to a public GitHub repository hourly. This creates an independent, append‑only record that third parties can audit.
- The public log contains only verification metadata (verification codes, cryptographic hashes, signatures, timestamps) — it does not contain personal data, GPS, or report contents.

Contents
- What is the transparency log?
- What is published (and what is not)
- Why this matters (legal / evidentiary value)
- How to verify a report (step-by-step)
- Technical notes & trust model
- Security & privacy considerations
- Contact / next steps

---

## What is the transparency log?

The transparency log is a public, append‑only registry of cryptographically signed reports. When FamilyNav generates and signs a report, a short entry describing that report is added to the registry. Those entries are periodically bundled into batches and published to a public GitHub repository. The commit history in the repository provides external, tamper‑evident evidence that a given entry (and therefore the corresponding signed report) existed at or before the time recorded in the repository.

This allows someone who has the report (for example a parent, attorney, or court) to independently check:
- The report was signed by FamilyNav's signing key.
- The report's reported cryptographic hash matches the log entry.
- The log entry was published to GitHub at a recorded time and commit.

---

## What is published (public) — and what is never published

Published (public):
- Verification code (short human-friendly code printed on the report)
- Cryptographic content hash of the report (for example, SHA-256)
- Signature metadata (signature, signer key id)
- Batch Merkle root and batch commit metadata (commit SHA, timestamp)
- Publication timestamps and counts (when the batch was published, entry counts)

Never published (private / not shared publicly):
- Report contents (the actual text, images, GPS, or case details)
- Any personally identifying information (PII)
- Raw event details that would reveal sensitive locations or personal data

The public entries are intentionally minimal so anyone can verify integrity without exposing private data.

---

## Why this matters (for users/attorneys/courts)

- Tamper evidence: Publishing a hashed record of each signed report to a public Git repository makes it easy to show that a report existed at a specific time and has not been altered since.
- Independent verification: The GitHub commit history is controlled by Git and GitHub; FamilyNav cannot retroactively remove or silently alter past commits without leaving a public trace.
- Privacy preserving: Only hashes and small metadata are published, so sensitive content remains private while still enabling verification.
- Practical for courts: A court or attorney can verify a report without needing access to FamilyNav's internal systems — they only need the signed report (or its verification code) and the public transparency log.

---

## How to verify a report — quick checklist

Assumptions: you have the physical or digital report and a verification code printed on it (for example, ABC12345). You may also have the signed report file (PDF or JSON) produced by FamilyNav.

1. Locate the verification code on the report.
2. Option A — Use the public verification page:
   - Visit the public verify URL: https://<familynav-site>/verify/<CODE>. The application’s Verify page is public and shows verification and transparency status.
   - The page will show whether the report signature is valid and whether the entry is published in the transparency log, including a GitHub commit link if published.

   Option B — Verify manually via the public transparency repository:
   - Go to the public transparency repository that contains published batches. The repository and path are listed on FamilyNav’s Transparency page.
   - Search the recent batch files for the verification code or the data hash.

3. Confirm the entry line:
   - Each published batch is a JSON Lines (newline-delimited JSON) file. An entry will include the verification code and a data hash.

4. Verify the report contents match the data hash:
   - If you have the actual report file, compute its cryptographic hash (for example, SHA-256) using a standard tool:
     - Example command on Linux or macOS: sha256sum report.pdf
     - Or use any GUI hash tool to compute SHA-256.
   - Confirm the computed hash matches the data_hash recorded in the transparency entry.

5. Confirm the entry is anchored in a Git commit:
   - In the batch commit metadata on GitHub, confirm the file containing the entry appears in the listed commit and that the commit timestamp is correct.
   - Optionally review the commit history and provenance on GitHub (view diffs and commit metadata).

6. Verify the signature:
   - Confirm the signed statement and signature on the report are valid using the signer key id and FamilyNav’s published public key.
   - Use the standard cryptographic verification tools appropriate for the signature algorithm specified with the report.

7. If all checks pass:
   - The report you hold matches the published registry entry (data hash), the report signature is valid, and the entry was published to an independently auditable public ledger (GitHub). This provides strong evidence the report existed as claimed and has not been modified.

If any check fails (hash mismatch, missing entry, invalid signature), the report’s authenticity is in question.

---

## Example verification flow (concise)

- From the report: verification code ABC12345.
- On the public Transparency page, search ABC12345 and find the entry with a data_hash H and signature S.
- On GitHub, find the batch JSONL that contains ABC12345; confirm the commit SHA and timestamp.
- Compute SHA-256(report.pdf) and confirm it equals H.
- Verify signature S using the published signer public key and confirm it validates.

---

## Technical & trust model notes

- Batches are published hourly by an automated edge function that:
  - Collects unpublished transparency entries from the database,
  - Writes a JSONL batch containing each entry’s verification code, hash, merkle root component, and signature,
  - Computes a batch Merkle root and commits the file to a public GitHub repo.
- The transparency registry is public and read-only for end-users. All writes are performed by backend/edge services using service credentials.
- Merkle roots allow efficient validation that a particular entry belongs to a published batch (auditability).
- The repository's commit history is the primary anti-tamper mechanism — modifications to published files are visible in Git history.

---

## Security & privacy considerations

- The system is intentionally conservative about what is published publicly — only index-like metadata and cryptographic material.
- No personal data or location traces are published. If you need the actual report, you must get it from the report issuer or the person who provided the report.
- The transparency registry is a public audit trail, not a data access channel.

---

## If you're an attorney or court official — practical guidance

- If a party submits a FamilyNav report in evidence, ask for the verification code printed on the report or the signed file.
- Use the steps above to independently verify the report's hash, signature, and presence in the transparency log.
- If you require a formal attestation from FamilyNav, the organization can provide logs, key information, or signed attestations as needed subject to their policies.

---

## Contact / support

If you have questions about verification steps, need the FamilyNav signer public key, or want help verifying a specific report, please contact the FamilyNav support team (contact info is available in the application or at www.familynav.app) or the custodian/administrator who provided the report.

---

Document version: 1.0  
Last updated: 2026-01-09
