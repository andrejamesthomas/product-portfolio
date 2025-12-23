# Case Study: Shipping a New Feature Without Breaking Legacy Customers

## 1. Context
We operate a product platform serving a large installed base running multiple software generations and capability levels. New features must coexist with legacy clients that do not support the same backend commands, APIs, or runtime guarantees.

* Product / domain: Connected platform feature delivery (multi-version installed base)

* Target users: End customers and internal operations/support teams

* Environment: Platform product with strong client-side capability dependencies

* Constraints: Backward compatibility, reliability and safety requirements, and operational risk from unsupported execution paths

## 2. Problem Statement
A high-value feature was requested for broad rollout, but only a subset of the installed base had the required platform capabilities. Rolling it out universally would lead to unsupported execution on legacy versions, increasing failure rates, support burden, and potentially creating safety/reliability incidents.

We needed a product strategy that delivered value quickly to eligible users while preventing feature activation on incompatible versions—without fragmenting the product experience or creating long-term maintenance debt.

## 3. Objectives

### Business Objectives
- Deliver the feature to eligible users within a predictable timeline.
- Reduce operational risk by preventing unsupported execution paths.
- Create a scalable approach for future capability-dependent features.

### User Objectives
- Provide a reliable and consistent experience for eligible users.
- Avoid confusing behaviour for ineligible users (no broken flows, no silent failures).

## 4. Discovery and Assumptions
Key discovery work focused on understanding capability distribution, failure modes, and operational impact.

- Assumption A: A meaningful percentage of the installed base is technically eligible for the feature. [Inference]
- Assumption B: Enabling the feature on ineligible versions leads to predictable failure patterns (timeouts, rejected commands, inconsistent state). [Inference]
- Assumption C: Users interpret partial failures as product instability, increasing support contacts and trust erosion. [Inference]

Validation activities included:
- Mapping version/capability coverage across the installed base (eligibility segmentation).
- Reviewing historical incident and support data for similar “unsupported command” patterns.
- Aligning with engineering on the minimal reliable capability contract required for the feature.

## 5. Options and Trade-offs

| Option | Description | Pros | Cons |
|------|-------------|------|------|
| A — Rollout to all | Enable the feature for every user/version | Fastest perceived reach | High failure rate on legacy; operational risk; support load; potential incidents |
| B — Manual allowlist | Enable only for selected accounts via manual list | Risk-controlled; quick start | Not scalable; governance overhead; error-prone |
| C — Capability gating (selected) | Enable based on explicit capability checks (version/flags/contract) | Scalable; safe by default; repeatable pattern for future | Requires eligibility model; may disappoint ineligible users |
| D — Build legacy compatibility layer | Engineering effort to support old versions | Maximum reach long-term | High cost; increases complexity; slows delivery; creates ongoing debt |

We discarded Option A due to predictable reliability risk and Option D due to disproportionate cost and long-term maintenance overhead relative to expected incremental value.

## 6. Product Decision
We chose capability gating as the default strategy: the feature would be available only when the platform explicitly confirmed eligibility (version + capability contract). Ineligible users would see a clear, non-broken experience (feature hidden or presented as unavailable with a rationale, depending on the surface area).

Rationale:
- Reliability and trust were treated as first-class product requirements, not engineering concerns.
- The approach scales: future features can reuse the same eligibility model and rollout controls.
- It creates a clear contract between product intent and platform reality, reducing ambiguous failure modes.

## 7. Delivery Approach
We delivered in phases:
1. Define eligibility criteria and capability contract (what must be true to safely enable the feature).
2. Implement gating controls (server-side checks + controlled rollout mechanism).
3. Launch to a small eligible cohort, monitor reliability and support signals, then scale.
4. Document the pattern (playbook) so future teams can reuse the approach.

Collaboration:
- Engineering: defined the capability contract and failure-safe behaviour.
- Support/Operations: aligned on expected user-visible outcomes and escalation paths.
- Stakeholders: agreed on eligibility-based rollout criteria and communication plan.

## 8. Metrics and Impact
We tracked success using a combination of adoption and guardrail metrics:

Leading indicators:
- Eligible user activation rate
- Feature success rate (happy-path completion)

Guardrails:
- Error rate / rejected commands for feature-related operations
- Support contact rate for feature-related topics
- Incident rate linked to feature activation attempts on legacy versions

We considered the rollout successful if adoption increased among eligible users while guardrail metrics remained within predefined thresholds.

## 9. Outcome and Learnings
What worked well:
- Gating prevented predictable failure modes and reduced operational uncertainty.
- A phased rollout created confidence and shortened feedback loops.

What did not work as expected:
- Some stakeholders initially underestimated the user perception impact of “not available” messaging. [Inference]
- Eligibility definitions required iteration to match real-world edge cases.

What we would do differently:
- Invest earlier in user-facing communication for ineligible cohorts to reduce frustration.
- Formalise the eligibility model sooner as a shared platform capability (not a one-off feature rule).

## 10. Open Questions or Next Steps
- Should eligibility be exposed as a reusable platform service for all teams?
- Can we create a standardised user-facing pattern for “capability unavailable” to improve consistency?
- What is the cost/benefit threshold for investing in backward compatibility for legacy cohorts?
