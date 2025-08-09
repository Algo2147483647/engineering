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

Condition 的解析通过规则引擎实现

Condition 所需的数据可以从 Trigger 中获取, 也可以调用其他渠道获取数据. 从 Trigger 中获取的化, 需要二者协同配合.

### Action

When multiple actions are executed sequentially, the processing mechanism operates in two distinct modes:

- **Synchronous Action**: Due to the execution results of synchronous actions are immediately available, triggers subsequent actions automatically upon completion.
- **Asynchronous Action**: After initiation, the current execution flow pauses, awaiting a completion notification from an external system via the callback interface. The process resumes and proceeds to subsequent actions only after receiving the confirmation signal.

![202505252226](./assets/202505252226.svg)

## Condition Rule Engine

- **Rule script definition**: Define rules using a format that is easy to understand and modify, such as JSON or a DSL (Domain Specific Language).
- **Rule Parser**: Parses user-defined rule scripts into internal data structures that can be used by the condition engine.
- **Data Retrieval**: Retrieve the necessary field values from the event object using the dictionary or object accessors.
- **Condition Engine**: Implement conditional operations based on Boolean logic, expression calculation, etc.
- **Caching**: Cache the results of evaluated conditions to improve performance.

## Event System

The hierarchical architecture of Event System adopts a tree topology structure, divided into three tiers from top to bottom to form a complete execution framework:

- **Policy Group**: Serves as the root node and global container, coordinating a series of policy through centralized orchestration.
- **Policy**: The fundamental unit of complete strategic plan, with each policy has a set of rules.
- **Rule**: Rules serve as fundamental execution units. Their execution dependencies are defined via a directed acyclic graph, ensuring that rules can be scheduled in parallel according to the topological order. The core logic of each rule is implemented using the event-driven model.

![202508091223](./assets/202508091223.svg)

### Circulation between Rules

**Policy-Rule Synchronization:** When an policy officially launches, its state transitions to *In Progress*. Simultaneously, the topologically first associated rule in the ordered sequence is unlocked, transitioning its state to *In Progress* and initiating its execution. The policy concludes once all its constituent tasks have ended.

The Policy must track which Rule the current processing flow has reached. Each Rule can be assigned a state or state machine mechanism, or simply flagged to indicate whether it should be executed in the current flow.

不同流转之间的独立性: 两次 Rules 流转在什么情况下被认为是重复的, 如果不重复可独立进行各自流转, 如果重复则按照当前 task 继续流转. 

Parents-Child Rules Circulation: Rule 之间的流转推图, 可以设定当全部父节点完成或者满足什么条件下, 当前 Rule 可以开启. 也可以设定同一父节点下的子节点之间的流转规则, 如当父节点下有一个节点被解锁了, 则其他节点被置为无效.
