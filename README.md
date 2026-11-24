# Kasauti — The touchstone for skill

> **Proving a tradesperson's skill on their own phone.**

A *kasauti* is the goldsmith's touchstone, the stone that tells real gold from fake. Kasauti does the same for a tradesperson's skill. A worker proves a skill live, on their own phone, and leaves with a signed pass that a contractor can check on the spot.

**wHACKiest 2026 · Online Ideathon (26–27 Nov 2026)**
Department of Computer Science and Engineering, BMS College of Engineering
**Track 4 — UnlockED · Problem Statement 1: Vocational skill verification**

> 🚧 **Status: concept stage, MVP in progress.** Screens are design concepts. Code, demo video and setup instructions will be added as we build.

---

## 📌 Table of contents

- [The problem](#-the-problem)
- [Our solution](#-our-solution)
- [How it works](#-how-it-works)
- [MVP scope](#-mvp-scope-24-hours)
- [Tech stack](#-tech-stack)
- [Architecture](#-architecture)
- [What's new](#-whats-new)
- [Risks and how we handle them](#-risks-and-how-we-handle-them)
- [Pilot targets](#-pilot-targets)
- [Business model](#-business-model)
- [Repository structure](#-repository-structure)
- [Getting started](#-getting-started)
- [Demo](#-demo)
- [Team](#-team)
- [References](#-references)

---

## ❗ The problem

Tradespeople who learned on the job have **no proof a contractor can check on the spot.**

**Meet Ravi (an illustrative example):** 12 years of wiring homes, 0 certificates. He's paid like a helper and hired only by people who already know him.

**Who it affects:** electricians, plumbers and masons; the contractors who hire them; and the households they work in.

| Figure | What it shows |
|---|---|
| **31 crore+** | workers registered on e-Shram, India's unorganised-worker registry ¹ |
| **~4%** | of working-age Indians have formal vocational training ² |
| **94.5%** | of 95.9 lakh PMKVY 2.0/3.0 candidate records had blank or invalid bank-account fields ³ |
| **58** | duplicate profile photos among 694 checked in one employer's 24 audited batches ³ |

**Why today's proof fails the contractor:**

- **Certificate:** hard to check on site
- **Job-app profile:** self-declared
- **Word of mouth:** only works locally

---

## 💡 Our solution

Kasauti brings the test **to the worker**, on their own phone, in four steps:

1. **🗣️ Speak:** a short voice interview about real jobs, in Hindi or English.
2. **🔧 Do:** a live task on camera, with a **one-time code the worker says aloud**, so an old or borrowed video won't pass.
3. **✅ Get graded:** AI grades each step against a rubric and flags safety misses. Unclear cases go to a human assessor.
4. **🎫 Own the pass:** the worker gets a **signed QR pass** they can share. A contractor scans it to verify.

Over time, the pass grows with **customer-confirmed jobs** (verified by OTP), like a contribution graph for real work.

---

## ⚙️ How it works

```
Worker's phone                Cloud checks                    Outcome
──────────────                ────────────                    ───────
Voice interview + selfie ──►  Spoken-code check (Scribe) ──┐
3 task clips (≤60s each) ──►  Face comparison (Rekognition)├──► Pass / Retake / Human review
Records offline, uploads ──►  Step grading (Claude)    ────┘
later                                                          │
                                                               ▼
                                           Signed pass (KMS) ─► QR ─► Contractor scans & verifies
                                           Lesson + retake (TTS) if the worker doesn't pass
```

- Each clip is **up to 60 seconds**. Recording works **offline**, and checks run once the phone is connected.
- Dark or blurry video returns **"can't tell"** and asks for a retake.
- Face comparison flags proxy test-takers. It does **not** prove liveness.
- **Offline verification:** the app checks the pass signature against a cached Kasauti issuer key. Checking whether a pass was revoked needs a connection.

---

## 🎯 MVP scope (24 hours)

**One trade only: domestic electrician.**

- [ ] Voice interview: 3 questions
- [ ] 3 live tasks, one-time code each
- [ ] AI step grading + human review queue
- [ ] Signed QR pass + verification page
- [ ] Customer job confirmation (OTP)

**Rubric:** our proposal, based on the Power Sector Skill Council's *Electrician Domestic Solution* qualification (**PSS/Q6001, NSQF level 3**).

**Not in the MVP:** vouching, other trades.

**Build plan:** core loop by **H6** → first signed pass by **H12** → feature freeze at **H18** → polish + backup demo video.

---

## 🛠️ Tech stack

We use managed services only, so the 24 hours go into the worker's experience instead of training models.

| Layer | Technology | Purpose |
|---|---|---|
| **App** | Next.js progressive web app (PWA) | Installs on low-end Android; records offline, uploads later |
| **Voice** | ElevenLabs Agents · Scribe · TTS | Interview in Hindi or English (Kannada to be tested); transcribes the spoken code |
| **Grading** | Claude on Amazon Bedrock | Reads video frames + rubric; returns step scores |
| **Face check** | Amazon Rekognition (CompareFaces) | Flags a different face between interview and task |
| **Backend** | AWS Lambda · API Gateway · S3 · DynamoDB · SNS | Serverless, pay-per-use |
| **Credential** | W3C Verifiable Credential, signed with an AWS KMS key | Anyone with our published issuer key can verify it |

**Why it fits:**
- **Nothing to train:** every AI step is an API call.
- **Voice-first:** works for workers who may not read or type.
- **Open format:** passes can be checked with standard tools.

---

## 🏗️ Architecture

| Phone | Capture | Cloud checks | Decision + data | Outputs |
|---|---|---|---|---|
| Worker app (offline recording) | Voice interview (ElevenLabs Agent + selfie) | Spoken-code check (ElevenLabs Scribe) | Decision: pass, retake or human | Lesson + retake (ElevenLabs TTS) |
| | Task + one-time code (API Gateway + Lambda) | Face comparison (Amazon Rekognition) | Records (Amazon DynamoDB) | Job confirmation (customer OTP via SNS) |
| | 3 task clips (S3, encrypted) | Step grading (Claude on Bedrock) | Signed pass (AWS KMS key) | Contractor check (scans the QR) |

<!-- TODO: add architecture diagram image -> docs/architecture.png -->

---

## 🚀 What's new

Other tools record what a worker *claims*. Kasauti records what their hands can *do*, and makes that evidence hard to fake.

| | **Kasauti (planned)** | Skyljo | PMKVY certification | Hunar.AI |
|---|---|---|---|---|
| **Where the test happens** | Worker's own phone | At ITIs; AI assists assessors | Training centres; assessor-led | No skill test; AI screens CVs |
| **Anti-fake measures** | Spoken one-time code + face comparison | Not stated publicly | Photo evidence; CAG found reuse | Aadhaar / PAN identity checks |
| **What the worker gets** | Signed, shareable pass | Skill score (stated aim) | Govt. certificate | Job match; no credential |
| **Status** | Concept; MVP planned | Launched Feb 2026 | Running since 2015 | Operating; 20+ clients |

*Competitor cells reflect public descriptions only.*

1. **The test comes to the worker:** on their own phone, any day, voice-first. No visit to a centre.
2. **Replay-resistant evidence:** a one-time code spoken on camera for each task (borrowed from CAPTCHA), plus face comparison.
3. **Proof the worker owns and grows:** a signed pass plus customer-confirmed jobs (borrowed from GitHub's contribution graph).

---

## 🛡️ Risks and how we handle them

| Risk | How we handle it |
|---|---|
| Replayed or borrowed video | One-time code per task, expires in 90 s |
| Proxy test-taker | Face comparison flags mismatches; random re-tests |
| Wrong AI grade | Low confidence → human assessor or "can't tell" |
| Poor video or no network | Record offline, upload later; unclear video means a retake |
| Worker privacy | Consent screen, encrypted storage, 30-day clip deletion; legal review before any pilot |

**Proposed retention:** clips are deleted 30 days after grading unless the worker chooses to keep them.

---

## 📊 Pilot targets

*These are targets we will measure in a pilot, not results.*

| Metric | Target |
|---|---|
| Time to finish a test | ≤ 15 min (median) |
| AI vs certified assessor, per step | ≥ 80% agreement on 50 tasks |
| Replayed clips rejected | 100% of our replay test set |
| Tests ending "can't tell" | Under 15% |
| Contractor check time | Under 5 s per scan |

**AI cost estimate:** ~₹30–50 per test (assumes 3 clips, ~30 graded frames and ~3 min of voice, at public list prices; to be validated).

---

## 💼 Business model

**Workers test free; contractors pay for confidence in a hire.** Every price below is a hypothesis we still need to test.

**Beachhead:** electricians in Bengaluru.
- **Users:** electricians and ITI electrician trainees (free)
- **Likely buyers:** small contractors and facility-management firms
- **Why they'd pay:** a bad electrical hire means rework and safety risk

| Who | Pays for | Price (hypothesis) |
|---|---|---|
| Worker | Test, pass, lessons | Free |
| Contractor | Scans + job posts | ₹499 / month |
| Hiring platform | Per-check API | ₹99 / check |
| Training institute | Passes for graduates | Per test, to be priced |

**Does free-for-workers add up?** At ~₹30–50 of AI cost per test, one ₹499 contractor plan pays for about 10–16 worker tests a month. Other costs include voice minutes and AWS hosting.

**Route to market (planned, nothing arranged yet):**
1. Approach 2–3 Bengaluru ITIs to test 20 electrician trainees
2. Recruit 5 small contractors to scan passes for 4 weeks
3. Measure the pilot targets, then add plumbers

---

## 📁 Repository structure

*Planned layout; it will be updated as we build.*

```
kasauti/
├── app/              # Next.js PWA (worker + contractor screens)
├── backend/          # AWS Lambda functions (API, grading, signing)
├── rubric/           # Domestic electrician rubric (PSS/Q6001-based)
├── infra/            # AWS infrastructure config
├── docs/             # Architecture diagrams, pitch deck, screenshots
└── README.md
```

---

## 🧑‍💻 Getting started

*Coming soon.* Setup and run instructions will be added once the MVP code is pushed.

```bash
# placeholder
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

---

## 🎥 Demo

- **Pitch deck:** *link to be added*
- **Demo video:** *YouTube link to be added*

---

## 👥 Team Bhonduss

| Role | Name |
|---|---|
| Lead | Harshit Khetan |
| Member | Ishan Raj |
| Member | Hamdan Bari |

**BMS College of Engineering** · Track T-4

---

## 📚 References

1. Lok Sabha written reply, reported by All India Radio News, 1 Dec 2025 (e-Shram registrations).
2. PLFS 2025 (MoSPI) via Data For India (formal vocational training).
3. CAG performance audit of PMKVY, reported by The Wire, 8 Jan 2026. The bank-account and duplicate-photo results are separate audit findings.
4. Skyljo press release (ANI/PNN) via The Tribune, 27 Feb 2026.
5. Hunar.AI profile, YourStory.
6. Power Sector Skill Council: Electrician Domestic Solution qualification, PSS/Q6001 (NSQF level 3).

---

<p align="center"><b>Kasauti — a touchstone for skill.</b></p>
