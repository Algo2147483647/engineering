# Event-driven model: Event-Condition-Action

[TOC]

## Event-Condition-Action

> WHEN <*event*> IF <*condition*> THEN <*action*>.

An Event-Condition-Action (ECA) model is a rule-based paradigm used in event-driven systems to automate responses based on specific conditions.

- **Events**: A detectable occurrence, which carries the attribute parameters and type of the event, and defines when the event is triggered, including timed triggering, asynchronous events and synchronous calls.
- **Conditions**:  A set of criteria that must be met before actions is allowed to execute.
- **Actions**: The operations that are executed when the event occurs and the condition is met.

![ECA](assets/ECA.svg)

### Event

A detectable occurrence, which carries the attribute parameters and type of the event, and defines when the event is triggered, including timed triggering, asynchronous events and synchronous calls. Events may be triggered synchronously through API calls or asynchronously via MQ messages.

### Condition

Condition parsing is achieved through a rule engine.



### Action

When multiple actions are executed sequentially, the processing mechanism operates in two distinct modes:

- **Synchronous Action**: Due to the execution results of synchronous actions are immediately available, triggers subsequent actions automatically upon completion.
- **Asynchronous Action**: After initiation, the current execution flow pauses, awaiting a completion notification from an external system via the callback interface. The process resumes and proceeds to subsequent actions only after receiving the confirmation signal.

![202505252226](./assets/202505252226.svg)

### Relationship between E-C-A

The data required by the Condition can be obtained from the Trigger or from other channels. If the data is obtained from the Trigger, both the Trigger and the Condition need to work together. The ECA unit can be configured with sets of input and output parameter types; they can only be used together if the parameter sets match.

## Rule Engine: Core implementation

Pattern Matching: The engine matches facts with rule conditions and triggers rules that meet the conditions.

The construction of a Condition consists of a Condition Template, static data, and dynamic data. The Condition Template, described by a script, is a framework used to construct a complete and parsable Condition after parameter population. Static data consists of parameters known during the construction process before runtime, while dynamic data is data obtained at runtime and includes the runtime context.

**Rule base**: Define rules using a format that is easy to understand and modify, such as JSON or a DSL (Domain Specific Language).



- **Rule Parser**: Parses user-defined rule scripts into internal data structures that can be used by the condition engine.
- **Data Retrieval**: Retrieve the necessary field values from the event object using the dictionary or object accessors.
- **Condition Engine**: Implement conditional operations based on Boolean logic, expression calculation, etc.
- **Caching**: Cache the results of evaluated conditions to improve performance.

![condition](./assets/condition.svg)

## Rule System

The hierarchical architecture of Rule System adopts a tree topology structure, divided into three tiers from top to bottom to form a complete execution framework:

- **Policy Group**: Serves as the root node and global container, coordinating a series of policy through centralized orchestration. A policy group itself may not exist; instead, a policy group ID is provided as an identifier to manage a set of policies.
- **Policy**: The fundamental unit of complete strategic plan, with each policy has a set of rules.
- **Rule**: Rules serve as fundamental execution units. Their execution dependencies are defined via a directed acyclic graph, ensuring that rules can be scheduled in parallel according to the topological order. The core logic of each rule is implemented using the event-driven model.

![202508091223](./assets/202508091223.svg)

### Circulation between Rules

**Policy Flow**: 

- **Idempotent key**: Each policy transition instance is identified by a designated unique key. This unique key can be input or customized according to specific business requirements. We combine the external unique key with the policy ID to form a composite idempotent key, which can be further combined with a round number to create a complete idempotent identifier `uniqu_key-polcy_id-round_number`. This mechanism not only ensures the uniqueness of each policy flow but also supports initiating multiple independent and distinguishable flow processes for the same policy.

- **Independence**: Each policy flow can be circulated independently.

**Policy-Rule State Synchronization:** When an policy officially launches, its state transitions to *In Progress*. Simultaneously, the topologically first associated rule in the ordered sequence is unlocked, transitioning its state to *In Progress* and initiating its execution. The policy concludes once all its constituent tasks have ended.

Storage: The Policy must track which Rule the current processing flow has reached. Each Rule can be assigned a state or state machine mechanism, or simply flagged to indicate whether it should be executed in the current flow.

**Transitions between Rules**:

- **Transitions between parent and child nodes (vertical transitions)**: This defines how a child node can be initiated. Conditions can be set for when the current rule can be activated, such as after all parent nodes are completed or when specific criteria are met.
  - **All Completed (AND)**: The current node is set to Ready when all parent nodes are in the Completed state. Suitable for strict linear processes or scenarios where all prerequisite tasks must be completed.
  - **Any One Completed (OR)**: The current node is set to Ready when any one of the parent nodes is in the Completed state. Suitable for multi-path selection scenarios, where completing any prerequisite task can initiate subsequent tasks.
  - **Custom Quantity (M-of-N)**: The current node is set to Ready when M out of N parent nodes are in the Completed state. Suitable for scenarios that require a certain proportion of prerequisite conditions to be met.
- **Transitions between sibling nodes (horizontal transitions)**: This defines the mutual influence between child nodes under the same parent node. Rules can be established for transitions between child nodes under the same parent node.
  - Mutual Exclusion: Multiple branches where only one can be selected. Once one path is chosen, the others are closed. When one sibling node is unlocked (becomes Ready) or activated (becomes Active), all other sibling nodes immediately become Invalid.
  - Parallel: All sibling nodes are independent of each other and can individually become Ready based on the parent-child rules.
  - Dependency: There is an implicit sequential dependency among sibling nodes.

