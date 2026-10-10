# Project 5: Finding a Website's Weak Spots the Way an Attacker Would

A controlled web application exposure review, carried out entirely in Anyaugo Space-Tech's own practice lab.

## The Business Question

If someone outside an organisation examined its website the way an attacker would, what could they find, how serious would it be, and what should be fixed first?

This exercise uses a deliberately vulnerable practice application (OWASP Juice Shop) running on two lab computers. It is **not** a client assessment and does **not** establish weaknesses in any company's systems. Every result is a lab result.

## Current Position

The review is complete. Working from an attacker machine, we mapped the target's reachable areas, then discovered and confirmed several real weaknesses — the most serious being a full administrator login bypass (no password required) and a public folder handing out backup files and a password vault. Every finding was re-verified as reproducible and rated on a single consistent scale. All work stayed inside the agreed lab boundary.

The portfolio contains the evidence screenshots for each stage, this plain-language guide, and a [unified PDF](Test-08-Final-Report-and-Client-Workflow/) that brings the screenshots together with explanations, limitations, a proposed client workflow, and privacy recommendations.

## How To Read The Evidence

Each stage explains the concern, what was done, what the evidence shows, how it applies to real client work, and — most importantly — what the evidence does **not** establish. The unified PDF shows the screenshots; this README explains the same story in words, without images.

| Test | What it covers |
| --- | --- |
| Test 01: Scope and Authorization | What the exercise covers and the limits of permission |
| Test 02: Lab Setup and Network Validation | Building an isolated two-machine lab and proving it works |
| Test 03: Practice Application Setup | Deploying the target application the professional way (as code) |
| Test 04: Web Traffic and Evidence Capture | How a browser talks to a website, and how it is recorded as evidence |
| Test 05: Attack Surface Discovery | Mapping everything the application exposes before testing it |
| Test 06: Web Application Findings | The real weaknesses found, each documented and explained |
| Test 07: Findings Retest and Severity Review | Re-confirming each finding and rating it consistently |
| Test 08: Final Report and Client Workflow | The unified account plus a proposed workflow for authorised client work |
| Test 09: Privacy and AI Use | How client information is handled when AI supports the work |

---

## The Stages, Explained

### Test 01 — Scope and Authorization
Before any testing, we wrote down exactly what could be examined (our own lab machines and a public practice application) and what must never be touched (any real client system, any account not publicly offered for practice). Fixing this boundary first is what allows everything after it to be trusted as controlled and lawful rather than random poking at systems. **This does not establish permission over any real asset** — for a real client, written, signed authorisation with a clear scope must come first. That principle is absolute: we never test anything, even our own systems, without it.

### Test 02 — Lab Setup and Network Validation
We built two machines — an attacker (Kali) and a victim (Ubuntu) — on a private, isolated network with no path to the outside world. Each was given a fixed address, and we proved they could reach each other and nothing else. We also recorded an honest mistake here: a duplicate network profile was created and then removed, and it is kept in the evidence rather than hidden, because recording a mistake and its fix is good practice. **This proves the lab was configured correctly at the time of capture; it is not a complete isolation audit.**

### Test 03 — Practice Application Setup
We deployed the target application using an automation tool (Ansible) run from the attacker machine, rather than installing it by hand. This is how professionals build repeatable environments — the setup is written as a file that can rebuild the whole thing from scratch, which is itself a recovery capability. In real-world terms, that same automation channel is powerful: used well it configures a whole fleet consistently; abused by an attacker who steals the credentials, it becomes a way to push changes everywhere at once. **This shows the target running and reachable; it is an intentionally vulnerable practice app, not a real business system.**

### Test 04 — Web Traffic and Evidence Capture
We captured exactly what a browser and a website say to each other, saved as plain, copyable text — the evidence style used throughout. Reading that traffic already revealed weaknesses before any "attack": the site omits protective settings it should send to visitors' browsers (defence-in-depth headers), and its login pass (token) carries a scrambled password and the user's role written where anyone holding it can read them. **This demonstrates the capture method and reads the site's posture; the token weakness is confirmed as exploitable later.**

### Test 05 — Attack Surface Discovery
We asked the site for hundreds of common folder names to map what it exposes. At first the site answered "yes" to almost everything — a false-positive flood caused by it returning its home page for anything unrecognised. Recognising and filtering that pattern is the real skill; it turned an impossible "everything exists" list into a short, true map: a data interface, a few named endpoints, and — the key lead — a file directory the site tried to hide. That hidden folder was even advertised in the site's own "please don't look here" note, which only points attackers straight to it. **This maps what is reachable; it does not by itself prove any of it is dangerous — that is Test 06.**

### Test 06 — Web Application Findings
The core of the review. Two headline findings:

- **Administrator login bypass (Critical).** By typing a short piece of database language into the email box instead of an email, we changed the question the site asks its database into one that always says "true," and it handed us a full administrator session with no password. Confirmed by decoding the session pass, which showed the administrator account. For a business this is the worst kind of flaw: total takeover, no password, reachable by anyone. An ethical tester proves it once and stops; a malicious one would quietly loot customer data and stay hidden.
- **Exposed file directory (High).** The folder the site tried to hide was fully open, listing downloadable backup files and what appears to be a password vault. An attacker reads the "hidden paths" note first, downloads everything, and cracks the vault offline with no further contact. Fixable in minutes.

Supporting findings (from Test 04): sensitive data inside the login token, missing security headers, a path-disclosing note, and an over-permissive cross-site setting. **We proved each finding without altering data or escalating beyond what the evidence shows.**

### Test 07 — Findings Retest and Severity Review
We re-ran the two headline findings and confirmed both still reproduce on demand. Because a deliberately vulnerable practice app cannot be patched, this retest verifies reproducibility rather than confirming a fix — in a real engagement it would instead confirm the client's remediation worked. Every finding was then rated on one consistent scale (severity = impact × ease of exploitation), so the client knows what to fix first: Critical and High before the rest.

### Test 08 — Final Report and Client Workflow
The unified account of what was verified, plus a proposed delivery model for a real engagement — the client must approve scope, access and authority in writing before any work begins — and a table stating clearly what the lab evidence does and does not establish. Observed lab results are kept separate from proposed client practice throughout.

### Test 09 — Privacy and AI Use
Six agency recommendations for handling client information when AI supports security work: agree the workspace, limit what is shared, control access, keep a human review over AI-assisted output, check provider terms, and keep an accountable record. These are recommendations for client review and adoption, not controls this exercise implemented.

---

## What This Report Does Not Claim

This is a lab exercise against a practice application. It demonstrates real capability and a professional process, but it is not a client assessment, and it does not establish weaknesses in any company's systems. Prepared pages (the scope record, the severity review, the privacy recommendations) are labelled distinctly from raw screenshots, which are kept unretouched. Company branding identifies the report's publisher, Anyaugo Space-Tech Ltd.; it does not constitute client authorisation.

## Tools Used, and Their Professional Equivalents

Testing was command-line-first, using tools that are themselves the industry standard: **curl** (request/response capture), **ffuf** (content discovery from the ProjectDiscovery/fuzzing family), and **Ansible** (automated deployment). An intercepting proxy (**Burp Suite Community**, industry standard **Burp Suite Professional**; **mitmproxy** as a terminal-native alternative) is used where live request manipulation is required. The only genuine paid gaps in this class of work are Burp Professional and, for later projects, real cloud accounts and enterprise monitoring platforms — most of the stack shown here is exactly what professionals use.
