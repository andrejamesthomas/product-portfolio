# Case Study: Shipping a New Feature Without Breaking Legacy Customers

## 1. Context
We operate a product platform serving a large installed base running multiple software generations and capability levels. New features must coexist with legacy clients that do not support the same backend commands, APIs, or runtime guarantees.

- Product / domain: Connected platform feature delivery (multi-version installed base)
- Target users: End customers and internal operations/support teams
- Environment: Platform product with strong client-side capability dependencies
- Constraints: Backward compatibility, reliability and safety requirements, and operational risk from unsupported execution paths

## 2. Problem Statement
A high-value feature was prioritised for rollout, but only a subset of the installed base (~30–40%) had the required platform capabilities to support it safely. Enabling the feature universally would trigger predictable failure modes on legacy versions, increasing operational noise and undermining trust.

Examples of observed or expected failure modes included:

- Rejected or partially executed commands due to unsupported capability contracts
- Inconsistent client state after retries or timeouts
- Increased support contacts framed as “feature instability” rather than technical incompatibility

The core challenge was delivering value to eligible users without degrading reliability for everyone else.

## 3. Objectives

### Business Objectives
- Deliver the feature to eligible users within a predictable timeline.
- Reduce operational risk by preventing unsupported execution paths.
- Create a scalable approach for future capability-dependent features.

### User Objectives
- Provide a reliable and consistent experience for eligible users.
- Avoid confusing behaviour for ineligible users (no broken flows, no silent failures).

## 4. Discovery and Assumptions
Discovery focused on capability distribution, operational risk, and user perception.

Key assumptions:

- Only a minority of the installed base was technically eligible at launch (~30–40%).
- Enabling the feature on ineligible versions leads to systematic, not random, failure modes.
- Users interpret partial failures as product instability rather than version incompatibility.

Validation activities included:

- Mapping version and capability coverage across the installed base.
- Reviewing historical support and incident patterns for similar unsupported-command scenarios.

Aligning with engineering on the minimal capability contract required to guarantee safe execution.

## 5. Options and Trade-offs

| Option                           | Description                                           | Pros                                 | Cons                                                       |
| -------------------------------- | ----------------------------------------------------- | ------------------------------------ | ---------------------------------------------------------- |
| A — Rollout to all               | Enable the feature for every user/version             | Fast perceived reach                 | High failure rate on legacy; support load; incident risk   |
| B — Manual allowlist             | Enable only selected users manually                   | Risk-controlled; fast initial launch | Not scalable; governance overhead; error-prone             |
| C — Capability gating (selected) | Enable only when explicit capability criteria are met | Scalable; safe by default; reusable  | Reduced reach at launch; requires eligibility modelling    |
| D — Legacy compatibility layer   | Extend support to old versions                        | Maximum reach                        | High engineering cost; long-term complexity; slows roadmap |

Option A was rejected due to predictable reliability degradation.

Option D was rejected because the cost and long-term maintenance burden outweighed the incremental value of expanded reach.

## 6. Product Decision
We selected capability-based gating as the default strategy. Feature activation required explicit confirmation of platform eligibility (version + capability contract). Ineligible users were prevented from entering broken flows.

A key UX decision was to hide the feature entirely for ineligible users instead of exposing it with a disabled or error-prone state. This reduced confusion and avoided creating a perceived “broken feature”, at the cost of discoverability.

This decision faced initial pushback from stakeholders concerned about reduced launch reach, but reliability and trust were treated as non-negotiable product requirements.

## 7. Delivery Approach
Delivery was structured in phases:
- Define eligibility criteria and capability contract.
- Implement server-side gating and controlled rollout mechanisms.
- Launch to a small eligible cohort and monitor reliability and support signals.
- Scale rollout progressively and document the pattern for reuse.

Cross-functional alignment:
- Engineering: defined failure-safe behaviour and capability checks.
- Operations/Support: aligned on expected user-visible outcomes and escalation thresholds.
- Stakeholders: agreed on eligibility-based success criteria and communication boundaries.

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
