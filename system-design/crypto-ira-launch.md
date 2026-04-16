# System Design: Crypto IRA Launch

> End-to-end delivery of a market-first hybrid Crypto IRA at Crypto.com — cross-functional execution across Legal, Tax, Engineering, and Marketing within 6 months.

---

## Context

The U.S. IRS allows self-directed IRAs to hold alternative assets including cryptocurrency. Crypto.com identified this as a market opportunity with no direct competitor offering a seamless native crypto IRA experience.

**My role**: Technical Project Manager leading cross-functional delivery across 5 global functions.

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        User-Facing Layer                      │
│                                                              │
│   Mobile App (React Native)                                  │
│   ├── IRA Account Creation Flow                              │
│   ├── Contribution & Withdrawal UI                           │
│   ├── Tax Document Center                                    │
│   └── Compliance Disclosure Screens                          │
└──────────────────────┬───────────────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────────────┐
│                      API Gateway Layer                        │
│                                                              │
│   ├── Authentication & KYC Service                           │
│   ├── IRA Account Management Service                         │
│   ├── Transaction Engine                                     │
│   │   ├── Contribution limits enforcement (IRS rules)        │
│   │   ├── Early withdrawal penalty calculation               │
│   │   └── Required Minimum Distribution (RMD) logic         │
│   └── Rate Limiting & Fallback Mechanisms                    │
└──────────────────────┬───────────────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────────────┐
│                   Compliance & Reporting Layer                 │
│                                                              │
│   ├── IRS Form Generation (5498, 1099-R)                     │
│   ├── Custodian Integration (third-party IRA custodian)      │
│   ├── Audit Trail & Record Keeping                           │
│   └── Regulatory Reporting Pipeline                          │
└──────────────────────────────────────────────────────────────┘
```

---

## Delivery Framework

```
Regulatory Milestones (fixed dates)          Agile Sprints (2-week cycles)
         │                                            │
         └──────────────── Aligned via ───────────────┘
                      Milestone Mapping

Q1 Tax Season Deadline
         │
Week -24: Legal review of IRA structure
Week -20: Custodian partnership finalized
Week -16: Core transaction engine complete
Week -12: Compliance disclosure approved
Week -8:  Beta testing with internal users
Week -4:  Regulatory sign-off
Week -2:  Staged rollout begins
Week  0:  Public launch ✅
```

---

## Key Technical Challenges

### 1. React Native Migration During Delivery
The team was mid-migration from native iOS/Android to React Native when the IRA project started.

**Decision**: Scope the IRA feature to target React Native only — accelerate migration for affected modules rather than building on legacy native.

**Outcome**: First team-wide cross-platform feature; reduced future maintenance overhead.

### 2. High-Concurrency at Tax Season
Expected traffic spike during Q1 tax contribution deadline (April 15).

**Approach**: Led API architecture review and stress testing at 10x expected load. Defined:
- Rate limiting per user and per endpoint
- Fallback mechanisms for custodian API timeouts
- Queue-based contribution processing for peak periods

**Outcome**: Mitigated 95% of P1+ risks prior to release.

### 3. Cross-Jurisdiction Compliance
Tax rules differ across U.S. states. Legal and Tax teams had dependencies on Engineering timelines.

**Approach**: Introduced a regulatory milestone map alongside the sprint board. Legal sign-offs became explicit acceptance criteria for relevant stories.

**Outcome**: 20% reduction in time-to-market; launched ahead of Q1 tax season.

---

## Cross-Functional Coordination Model

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Legal   │    │   Tax    │    │   Eng    │    │Marketing │
└────┬─────┘    └────┬─────┘    └────┬─────┘    └────┬─────┘
     │               │               │               │
     └───────────────┴───────────────┴───────────────┘
                              │
                    ┌─────────▼──────────┐
                    │  TPM (Hsin Chen)   │
                    │                    │
                    │ · Dependency map   │
                    │ · Milestone lock   │
                    │ · Risk escalation  │
                    │ · Decision log     │
                    └────────────────────┘
```

---

## Results

| Metric | Result |
|--------|--------|
| Delivery | On time, ahead of Q1 tax season |
| Risk mitigation | 95% of P1+ risks resolved pre-launch |
| Time-to-market | Reduced by 20% via Agile-regulatory alignment |
| Team size | 20+ members across 5 global functions |
| Platform | Market-first hybrid Crypto IRA |
