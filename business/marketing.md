# C-end Marketing

## Value Exchange System

The core of consumer (C-end) marketing lies in the design and operation of a **controllable Value Exchange System** structured around the user lifecycle, guiding users to exchange value in ways that align with the platform’s most immediate strategic needs. Its ultimate objective is to **maximize user lifetime value (LTV)** while maintaining strict control over cost and risk. From an execution perspective, a C-end marketing system operates through the **precise design of marginal incentives**. By intervening at specific lifecycle states, it reshapes the distribution of user behaviors at minimal incremental cost. This approach amplifies user value, reduces platform-side uncertainty, and progressively establishes a long-term, stable value exchange relationship between users and the platform.
$$
\text{LTV} = \sum_{t=1}^{T}
\big(
\mathbb{P}(\text{use})_t \times
\text{Usage Frequency}_t \times
\text{Value per Transaction}_t
\big)
\\
\text{s.t. the user exists in the system}
$$
This formulation yields four fundamental structural dimensions:

- **Existence**: whether the user exists in the system.
- **Activation / Retention**: whether the user engages in usage.
- **Frequency**: whether usage occurs with sufficient regularity.
- **Monetization**: whether each usage instance generates greater economic value.

### User State Transition

Treat users as a state machine, any meaningful change along any dimension can only occur at state-transition nodes. Marketing can act on only three categories of critical nodes:

1. **Before entering the system** (not yet a user)
2. **Positive transitions within the system** (value increases)
3. **Negative transitions within the system** (degradation / churn)



| Growth Module | Essence | Core Metrics |
| ------------------ | ---- | ---- |
| ***Acquisition***  |   Outside system → Inside system<br />Whether the user enters at all (existence problem).   | New users, signup conversion rate, CAC |
| ***Activation***   | Registered but unused → First use<br />Whether the first key action occurs. | Week-1 activation rate, first-order conversion, key path completion |
| ***Retention***    | Used → Repeated usage<br />Usage probability does not decay over time | D1 / D7 / D30 retention, DAU/WAU/MAU |
| ***Conversion***   | Intent present → Action completed<br />Increase state-transition success rate | Order conversion rate, completion rate, GMV |
| ***Engagement***   | Stable usage → High-frequency usage<br />Amplify actions per unit time | Avg. usage frequency, active days per user, engagement depth |
| ***Monetization / LTV*** | Action occurred → Higher-value action<br />Amplify value per action | ARPU, LTV, AOV, repeat purchase rate |
| ***Reactivation*** | Dormant / churned → Returned<br />Reverse state transition | Reactivation rate, post-return retention |
|                    |      |      |
|                    |      |      |

Here is the reverse application of the same framework. Use it to falsify whether a marketing initiative is addressing a real user need or a pseudo-need. You can quickly test whether a marketing plan stands up logically with the following five questions. If any of them cannot be answered clearly, it is pseudo-marketing.

1. **Which user state is it trying to change?**
    → If the target state transition is undefined, the campaign has no growth anchor.
2. **Why would the user agree to the exchange? What exactly is being exchanged, and what is the perceived value?**
    → If value exchange is unclear, the behavior is not user-driven.
3. **Would the user have done this anyway without the campaign?**
    → If yes, the campaign is merely taking credit for natural behavior.
4. **Is the incentive applied at the behavioral tipping point?**
    → If incentives are not placed at the moment of decision, they only inflate cost, not outcomes.
5. **Does this behavior compound long-term value for the platform?**
    → If it does not improve retention, frequency, or LTV, it is a one-off spike, not growth.



## C-end marketing activities

The core purpose of the marketing activities is to induce user behavior, prompting users to complete the behavioral instructions expected by the enterprise or to form a continuous behavioral habit. When users achieve the preset goals, the reward mechanism is immediately triggered, thus establishing a complete positive feedback reinforcement loop.

![order activities](./assets/order activities.svg)

### Short-term Free Activities

Short-term free activities are characterized by real-time outreach and swift conversions, arming to stimulate consumption. While they enhance conversion rates, they often provide limited benefits for long-term user retention. By lowering the user decision barriers (zero-cost participation) and intensifying time-sensitive pressure (countdown / limited-quantity), these activities drive conversions within a brief timeframe following users' exposure to activities.

Typical format including coupon-binding pop-ups and limited discounts. 

### Long-term Free Activities

Long-term free activities operate through sustained psychological intervention to guide user cognition and cultivate usage habits. Based on the behaviorist "action-feedback-reinforcement" model, they progressively shape user value recognition across the complete interaction lifecycle. Core components include:

- Visualized incentive (such as progress bar, level indicator) quantify and visualize abstract efforts to achieve a linear mapping between behavior-reward.
- Loss aversion design creates sunk costs (such as broken signature reset, privilege downgrade, other tiered penalties) 
- Precise time-period push notifications, triggering time points are set during peak user activity periods, and muscle memory is formed through periodic stimulation.
- Instant rewards use the dopamine secretion mechanism to strengthen behavioral inertia.

Long-term free activities are usually used as operating tools for exclusive scenarios in niche areas or specific user groups. Typical cases include user development tasks, member points system, content community cultivation, etc.

### Long-term Fee-based Activities

Long-term fee-based activity is essentially a gambling agreement between users and enterprise. These activities are difficult to promote to users and has a low penetration rate, so they are suitable for high-value customer groups. The design of the gambling agreement  requires balancing risk-reward ratios, with differentiated strategies across customer value tiers:

- High-net-worth users receive excess return high risk betting plan.
- Mid-tier users utilize low-risk plan.
- Long-tail users face prepayment gateways screening low-commitment populations.

Typical cases include subscription plans, monthly membership passes.

### Marketing Activities Integration

Integrated marketing activities platform, as solution that integrates multiple marketing tools, channels and strategies into a unified marketing zone, usually used to release demand at a specific marketing time point (e.g. promotional season, product launch), and can also exist as a regular marketing area for a long time. The marketing zone must follow the F-type visual heat map rules and reasonably plan the information hierarchy, divisions and pages. Long-term free activities are often used as a tool to induce users to enter the marketing zone.

Typical cases include grand sale campaign landing page, 11-11 shopping festival, game center, welfare center.
