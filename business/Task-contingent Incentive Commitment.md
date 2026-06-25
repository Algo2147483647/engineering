# Task-Based Incentive Commitment

[TOC]

A task-based incentive commitment can be understood as a conditional incentive arrangement between a platform and its users. Based on its operational objectives, the platform designs and issues conditional `offers`, specifying that users will receive corresponding `rewards` if they complete designated `objectives` within a defined time frame, through prescribed paths, and under specified rules. After the offer is presented, users may decide whether to `enroll`. Once enrolled, the system continuously tracks their `progress` and evaluates objective completion according to predefined criteria. When the objective is fulfilled, the platform delivers the reward subject to budgetary and risk-control constraints. The platform may then guide users to the next task or another offer, thereby creating an ongoing incentive loop.

> **Offer Configuration → Offer Presentation → User Enrollment → Behavioral Completion → Progress Measurement → Reward Fulfillment → Re-Engagement**

This mechanism transforms the behaviors desired by the platform into tasks that users are motivated to complete in exchange for rewards. It is commonly used as a long-term engagement strategy to shape user expectations, reinforce behavioral routines, and foster habitual platform use.

From the user perspective, the mechanism reduces uncertainty by clarifying the required action, the associated reward, and the user’s progress toward completion. From the platform perspective, incentives shift from indiscriminate distribution to targeted, outcome-based reward allocation, thereby improving incentive efficiency and increasing conversion into desired user behaviors.

## Offer

An Offer is a core domain entity in the system and serves as the primary object that users see, interpret, and decide whether to accept. It specifies a conditional commitment: what action the user is expected to complete and what reward the user will receive in return. Meanwhile, an offer also defines the eligibility conditions under which the user may participate.

### Participation Policy & Runtime

**Participation Policy.** An offer is a design-time definition, whereas users participate in a runtime instance generated from the offer, namely an opportunity / enrollment. The participation eligibility policy defines when a user is entitled to a new participation opportunity. It may incorporate constraints such as city, user segment, time window, participation frequency, eligibility criteria, and business context. The system evaluates these constraints to determine whether a new opportunity / enrollment should be created for the user.

**Repeated Participation Policy.** It defines how and when users may participate again for the same incentive offer. 

1. **One-time participation.** Each user may participate in the offer only once.
2. **Cycle-based repeated participation.** Participation opportunities are renewed by predefined time windows, such as fixed intervals, calendar days, weeks, or months, or by customized business rules.
3. **Progressive repeated participation.** A new participation opportunity is renewed only after the user completes the current one, enabling sequential, progress-driven participation. This mode defines how subsequent participation opportunities are generated, including their upper limit, activation mechanism, and interval constraints.

### Objective

An **objective** is the operational specification of the user action required by an offer. It defines the expected behavior, completion criteria, evaluation constraints, and resulting business outcome. It typically includes the following components:

| Component                | Definition                                                   | Examples                                                     |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Behavior                 | The user action to be performed.                             | Placing an order, making a payment, browsing content, checking in, clicking, sharing. |
| Completion Criterion     | The required level, count, duration, or state that defines task completion. | Completing three orders, browsing for at least ten seconds, checking in for three consecutive days. |
| Validity Constraints     | Conditions under which the behavior is considered valid for task completion. | A specified city, vehicle type, time period, user segment, or transaction category. |
| (1) Time Constraints     | The temporal scope within which the behavior is eligible.    | Within 24 hours after enrollment, within the current week, or after 6:00 p.m. each day. |
| (2) Spatial Constraints  | Location-based conditions that determine whether the behavior qualifies. | A designated city, service area, store, route, pickup location, delivery zone, or geofenced region. |
| (3)B usiness Constraints | Business-rule conditions that determine whether the behavior is eligible. | A specified product category, order type, payment method, membership tier, campaign channel, service type, or minimum transaction amount. |
| Progress Measurement     | The rules by which progress is accumulated, deduplicated, updated, or retrospectively calculated. | Deduplicating by order ID, accumulating by day, counting only actions after the first completed order, or recalculating progress after delayed transaction confirmation. |
| Reward Linkage           | The reward entitlement or downstream incentive triggered upon task completion. | Issuing a coupon, granting lottery eligibility, unlocking the next task, or entering a higher reward tier. |
| User-facing Explanation  | The way the objective and progress are communicated to the user interface. | “Complete one more order to receive a ¥10 coupon.”           |

An Objective can be modeled as either an atomic node or a composite structure, as seen in ladder-based or path-based tasks.
1. An atomic Objective is the smallest indivisible business goal, with clear progress measurement and simple completion criteria.
2. A composite Objective consists of multiple sub-objectives and supports more complex task patterns as following.

| Composition Pattern | Business Meaning                                             |
| ------------------- | ------------------------------------------------------------ |
| `Sequence`          | Sub-objectives must be completed in a specified order.       |
| `Ladder`            | Different reward tiers are unlocked by completing different levels. |
| `Parallel`          | Multiple objectives can progress simultaneously.             |
| `Any-of`            | Completing any one of the sub-objectives is sufficient.      |
| `All-of`            | All sub-objectives must be completed for the overall objective to be considered complete. |
| `Repeat`            | The same objective can be completed repeatedly, typically on a periodic basis. |
| `Conditional`       | Different users are routed to different objective paths based on predefined conditions. |

### Progress

`Progress` addresses one of the most complex and potentially disputed issues in task-based incentive systems: how user behavior can be measured accurately. Based on traceable records of user actions, it calculates a user’s progress toward a specific objective within a defined time window, business context, and rule scope. Progress needs to be distinguished at least as follows:


| Component          | Description                                                  |
| ------------------ | ------------------------------------------------------------ |
| Objective progress | The extent to which the target objective has been completed. |
| Included records   | Orders, actions, or events that are counted toward progress. |
| Excluded records   | Actions or events that do not satisfy the predefined rules.  |
| Time window        | The period within which actions are considered valid, including the start and end time. |
| Deduplication rule | Whether the same order or event can be counted only once.    |
| Node attribution   | The assignment of an order to a specific task level or node, in order to avoid double counting. |

At the implementation level, `Progress` should be built on two fundamental components: action records and rule-based computation. The system should first retain complete records of user actions related to the objective, then filter eligible actions according to the scope defined by the business rules, and finally determine whether the progress requirements have been satisfied. For this reason, `Progress` should not be modeled as a simple counter. A pure counting mechanism discards substantial historical information, making it difficult to reconstruct, verify, or explain the resulting progress state. In addition, some business scenarios require actions that occurred before a user enrolled in an offer to be included in the progress calculation.

The core functions of `Progress` are as follows:

1. **Recording factual user actions.** Action records should remain as close as possible to factual events and should not incorporate task-specific rules prematurely.
2. **Filtering actions according to the scope of the current `Objective`.** Only actions that satisfy the applicable time, scene, and rule constraints should be considered.
3. **Computing objective completion progress.** The system should support multiple aggregation methods and produce a clear progress result.
4. **Providing explainable outcomes.** The progress result must be interpretable because it may be used by users, operations teams, customer service, risk control, and finance.

| Progress type            | Example                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Count-based progress     | Completing three orders; viewing a page five times           |
| Amount-based progress    | Reaching a cumulative payment amount of RMB 100              |
| Tiered progress          | Receiving rewards after completing one, three, and five orders, respectively |
| Boolean progress         | Whether a user has completed one view; whether a user has checked in |
| Path-based progress      | Viewing first, then placing an order, and then completing payment |
| Composite progress       | Completing two orders and reaching a cumulative amount of RMB 80 |
| Exclusion-based progress | Completing an order without using a specified coupon         |

### Reward

`Reward` refers to the platform’s reward entitlement and fulfillment mechanism after users complete predefined objectives. It begins with a user-facing reward promise, proceeds to the user becomes eligible for a reward after completing the objective, and ultimately results in the delivery of actual reward assets after checks on inventory, budget, risk control, and fulfillment rules.

The `Reward` mechanism can be divided into three layers:

1. `Reward Promise`: the promised reward. This represents the user-facing reward displayed by the platform and shapes users’ expectations before participation.
2. `Reward Qualification`: reward eligibility. This refers to the qualification or entitlement obtained by the user after the objective has been completed.
3. `Reward Fulfillment`: actual fulfillment. This is the stage at which the platform invokes the benefit or entitlement system to complete the reward delivery.

Risk control is embedded throughout the `Reward` lifecycle rather than being an external component. First, pre-promise risk control determines whether a user is allowed to view or participate in a reward offer. Second, qualification-stage risk control evaluates whether the user should actually receive reward eligibility after completing the objective. Third, pre-fulfillment risk control serves as the final checkpoint before the reward is issued.


