# Oracle Methodology

> How Gacha Galaxy computes reference prices and fair market values across collectible card marketplaces.

This document describes the **public methodology** behind Gacha Galaxy's price oracle. The specific weighting parameters used in our Fair Market Value calculation are proprietary and not published here. The **Verifiable FMV Proofs** feature (on Horizen Base L3) will publish zero-knowledge attestations of correct FMV computation without revealing the proprietary weighting.

---

## Principles

1. **Live data only** — listings ingested every 20 minutes, never stale
2. **Grading-aware** — PSA, BGS, CGC, SGC, Beckett, and raw are tracked separately
3. **Multi-source** — 7 marketplaces indexed for triangulation
4. **Transparent inputs** — every observation feeding FMV is auditable
5. **Verifiable settlement** — prediction signals resolve on oracle data without manual edits

---

## Data sources

| Marketplace | Type | Coverage |
|---|---|---|
| eBay | Web2 | Global, broadest catalog |
| Collector Crypt | Web3 | Vintage / graded |
| Courtyard | Web3 | Graded |
| Phygitals | Web3 | Phygital tokens |
| Beezie | Web3 | Vintage / graded |
| Renaiss | Web3 | Graded |
| Dyli | Web3 | Cards |

Each marketplace is integrated via a dedicated adapter that normalizes listing data into a unified schema before any pricing logic runs.

---

## Card identity normalization

Cards from different marketplaces are resolved to a normalized identity using:

- **Set** + **card number** + **variant** (e.g., reverse holo, first edition, shadowless)
- **Grading authority** + **grade**
- **Population data** (where available)

Two listings with different marketplace metadata but the same normalized identity are treated as the same asset for FMV calculation.

---

## Grading-aware pricing

Different grades of the same card are **never** averaged together. PSA 10 ≠ PSA 9 ≠ CGC 10 ≠ raw, and our oracle preserves this distinction throughout.

For each (card identity, grading authority, grade) tuple, we maintain:

- A live price series (one observation per ~20 minutes from each marketplace where the card is listed)
- A historical snapshot archive
- A computed Fair Market Value (FMV) updated hourly

---

## Fair Market Value (FMV) overview

FMV is computed from live listings and recent transactions across all indexed marketplaces, weighted by:

- **Marketplace reliability and volume** — high-volume venues with reliable settlement carry more weight
- **Recency** — recent observations weighted more than older ones
- **Grading authority** — same grade from different authorities is not treated as identical
- **Liquidity** — thinly traded grades are penalized to avoid noise

The specific weighting parameters are proprietary. The **Verifiable FMV Proofs** feature (Horizen Base L3, deploying in 2026) will publish zero-knowledge attestations that prove a given FMV was computed correctly from a committed input set using the committed algorithm version, without exposing the weighting itself.

This unlocks enterprise data licensing for partners (grading services, marketplaces, research desks) who need to trust the FMV without us open-sourcing the proprietary weighting.

---

## Mispricing Scanner

The Mispricing Scanner is a derived product. For each tracked card-grade tuple, we compute:


Public methodology overview for Gacha Galaxy's price oracle and FMV computation.
