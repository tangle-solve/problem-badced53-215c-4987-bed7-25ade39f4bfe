<!-- solve-problem:badced53-215c-4987-bed7-25ade39f4bfe -->
# Geographically neutral block-building incentives

A [Tangle Solve problem](https://solve.tangle.tools/problems/badced53-215c-4987-bed7-25ade39f4bfe).

## What is the problem?

In Ethereum's Proposer–Builder Separation (PBS) architecture, relays and builders submit bids/blocks under hard timing deadlines (~ the proposer's slot schedule, sub-second margins). Nodes at different geographic locations have systematically different effective latencies to proposers and relays, so otherwise identical builders earn different expected payoffs purely due to server placement. This creates a centralization incentive: everyone wants to co-locate with the proposer/relay cluster (e.g., the same cloud region), which concentrates infrastructure and erodes resilience to regional outages, censorship, or jurisdictional action.

The problem: **design the timing rules, reward/bid rules, and relay topology of the PBS pipeline so that geographic location induces only a bounded (ideally zero) payoff advantage, while keeping latency-based failure resilience — the ability of the system to recover if one region goes dark.**

A reasonable formalization: model builders at locations ℓ ∈ L with latency matrix d(ℓ, relay) and d(ℓ, proposer). A slot is a first-price (or second-price) auction under a deadline T. Builder i's win probability depends on its effective latency-adjusted submission time and bid. Goal: find a mechanism M = (deadline structure T, relay placement set R, tie-breaking/reward rule) and a bound ε such that |E[payoff(ℓ)] − E[payoff(ℓ′])| ≤ ε for all ℓ, ℓ′, subject to the constraint that the mechanism tolerates failure of any single region (reliability ≥ 1 − δ). The tension: generous slack in deadlines gives geographic neutrality but extends the failure-recovery window; tight deadlines give resilience but reward proximity.

## Why does it matter?

Geographic concentration of builders and relays is an active Ethereum centralization concern: it makes censorship-by-jurisdiction and single-region outages (power, cloud failure, regulation) systemic risks. A provably location-neutral incentive design would let client teams (e.g., relay operators, spec designers) change deadline parameters and relay placement rules with formal backing, preserving a globally distributed builder set. It also connects to broader results on latency-based fairness in distributed market mechanisms.

## What's already known?

- Empirical and (per the census, 2026) latency-calibrated and mean-field analyses established that location-dependent incentives exist and drive co-location pressure: builders near proposer/relay clusters win disproportionate slots (verify the exact quantitative findings in Yang et al. 2026).
- Standard PBS timing: blocks must reach proposers within ~1–4s margins; relay networks (multiple relays, e.g., aggregating top-of-block delivery) are known mitigations for single-relay failure, but their placement is itself geographically skewed.
- Related literature on latency games in blockchain consensus (network-level, e.g., equihashing/propagation timing) and on facility-location incentive design exists, but no one has, to our knowledge, stated and solved the joint "neutrality + regional fault tolerance" mechanism-design problem.

Gap: no mechanism with a proven geographic payoff bound under a latency-calibrated model, and no matching lower bound showing how much neutrality must be traded against recovery time.

## What would count as solving it?

1. **Upper bound:** a concrete mechanism (deadline schedule, relay placement, bid/tie-breaking rule) with a proof that expected payoff difference between any two builder locations is ≤ ε(d_max), with explicit dependence on the latency matrix, subject to single-region-failure resilience ≥ 1 − δ.
2. **Lower bound:** a proof that any mechanism achieving resilience 1 − δ must have ε ≥ f(δ) > 0 for some latency distribution — quantifying the unavoidable trade-off.
3. **Simulation/validation:** a latency-calibrated simulator (real measured inter-region RTTs, real PBS bid data) demonstrating that the proposed mechanism reduces geographic win-rate variance by a stated factor versus the status quo, without increasing missed-slot rates beyond tolerance.

An engineering answer (variant 3) alone is publishable if paired with a semi-formal analysis; variants 1–2 are the theoretical gold standard.

## How to start

1. Read Yang et al. (2026) for the latency-calibrated/mean-field model — this is your base formalism; replicate their payoff-as-function-of-location computation.
2. Formalize the simplest nontrivial case: one relay, n builders at two locations, first-price deadline auction; compute exact win-probability gap. Then ask which mechanism knobs (deadline slack, commit-reveal bid timing, random tie-breaking, multiple relay replicas with earliest-arrival wins) shrink the gap.
3. Study the fault-tolerance side: relay replication / proposer fallback (e.g., local partial fulfillment, validator-client fallback behavior) to model recovery time as a function of deadline slack.
4. Main obstacle: the neutrality–resilience trade-off is fundamental (longer deadlines = slower failure detection), so the interesting result is the *shape* of the ε–δ frontier, not a zero–zero solution. Beware second-order effects: randomized tie-breaking invites latency-gaming and bid sniping.

## Key references

- Yang et al. (2026), "Geographical Centralization Resilience in Ethereum's Block-Building Paradigms," arXiv:2509.21475 — latency-calibrated and mean-field analysis establishing location-dependent builder incentives and regional concentration risk in PBS. (Citation from census record; verify authors/exact content at https://arxiv.org/abs/2509.21475.)
- Ethereum PBS/relay documentation and MEV-Boost specifications (ethereum/mev-boost on GitHub) — the actual timing constants, relay topology, and bid-submission protocol your mechanism must modify.
- Literature on network-latency games and facility location with strategic agents (verify specific papers) — tools for placing relays subject to incentive constraints.

## Warnings

- The census record is vague; the "problem" has no canonical statement — you are effectively formalizing it, so state your formalization explicitly and defend the interpretation.
- Do not assume ε = 0 is achievable: perfect geographic neutrality under deadlines is likely impossible; target a bounded trade-off.
- Latency measurements vary over time; any claimed bound is only as good as the RTT calibration — use multiple measurement sources and sensitivity analysis.
- Beware mechanisms that achieve neutrality only in expectation while introducing new attack surfaces (e.g., random tie-breaking enables bid-sniping races).

---
*`BCGT-000308` · flagged for review*

## Acceptance guidance

1. **Upper bound:** a concrete mechanism (deadline schedule, relay placement, bid/tie-breaking rule) with a proof that expected payoff difference between any two builder locations is ≤ ε(d_max), with explicit dependence on the latency matrix, subject to single-region-failure resilience ≥ 1 − δ.
2. **Lower bound:** a proof that any mechanism achieving resilience 1 − δ must have ε ≥ f(δ) > 0 for some latency distribution — quantifying the unavoidable trade-off.
3. **Simulation/validation:** a latency-calibrated simulator (real measured inter-region RTTs, real PBS bid data) demonstrating that the proposed mechanism reduces geographic win-rate variance by a stated factor versus the status quo, without increasing missed-slot rates beyond tolerance.

An engineering answer (variant 3) alone is publishable if paired with a semi-formal analysis; variants 1–2 are the theoretical gold standard.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/badced53-215c-4987-bed7-25ade39f4bfe?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000308.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000308)
