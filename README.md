# Health Care Insurance Management System — Data Dictionary

A sample data dictionary for a health care insurance management platform, created with [smart-data-dico](https://github.com/amah/smart-data-dico).

## How to Use

```bash
# Clone this repo
git clone https://github.com/amah/smart-data-dico-sample.git
cd smart-data-dico-sample

# Run smart-data-dico (installs automatically via npx)
npx @hamak/smart-data-dico --data-dir ./data-dictionaries
```

Then open http://localhost:3001 in your browser.

## Domain Overview

```
Health Care Insurance System
├── member-management/        (4 entities, 4 relationships)
│   ├── Member                [aggregate-root, PII fields]
│   ├── Dependent             [value-object]
│   ├── Address               [value-object]
│   └── Enrollment            [domain-event]
├── policy-management/        (4 entities, 3 relationships)
│   ├── Plan                  [aggregate-root]
│   ├── Coverage              [reference-data]
│   ├── Premium               [value-object]
│   └── Benefit               [reference-data]
├── claims-processing/        (5 entities, 6 structural + 2 lineage)
│   ├── Claim                 [aggregate-root]
│   ├── ClaimLine             [value-object]
│   ├── Adjudication          [domain-event]
│   ├── Payment               [domain-event]
│   └── ExplanationOfBenefits [reference-data]
├── provider-network/         (3 entities, 2 structural + 1 lineage)
│   ├── Provider              [aggregate-root]
│   ├── Facility              [reference-data]
│   └── ProviderContract      [value-object]
└── billing/                  (2 entities, 2 structural + 1 lineage)
    ├── Invoice               [aggregate-root]
    └── PaymentRecord         [domain-event]
```

**Total**: 18 entities, 17 structural relationships, 4 lineage flows, 2 perspectives

## Perspectives

### Claims Adjudication
Traces the complete claims lifecycle from submission through adjudication to payment. Root: `Claim`. Resolves 9+ entity paths including Member eligibility verification, Provider fee schedule lookup, and EOB generation.

### Member Billing
Covers the billing process from premium calculation to payment collection. Roots: `Invoice` + `Member`. Tracks how enrollment/plan/premium data flows into invoice generation.

## Data Lineage

```
Member ──(enrichment)──→ Claim ──(generation)──→ EOB
Provider ──(pricing)──→ Claim ──(billing)──→ Invoice
```

## Stereotypes Used

| Stereotype | Entities |
|-----------|----------|
| aggregate-root | Member, Plan, Claim, Provider, Invoice |
| value-object | Dependent, Address, Premium, ClaimLine, ProviderContract |
| domain-event | Enrollment, Adjudication, Payment, PaymentRecord |
| reference-data | Coverage, Benefit, Facility, ExplanationOfBenefits |

## PII Fields

| Entity | Attribute | Category |
|--------|-----------|----------|
| Member | ssn | direct |
| Member | firstName, lastName | direct |
| Member | email, phone | direct |
| Member | dateOfBirth | indirect |
| Dependent | firstName, lastName, ssn | direct |
| Address | street | direct |
| Provider | taxId | direct |
