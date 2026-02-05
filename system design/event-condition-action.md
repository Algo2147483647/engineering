# Event-driven model: Event-Condition-Action

[TOC]

## Basic model: ECA

> **WHEN**  <*event*>  **IF**  <*condition*>  **THEN**  <*action*>.

The event-driven model is a software architecture paradigm that places events at its core. Its central idea is that the occurrence of events drives the program’s execution flow, rather than relying on traditional sequential control flow. In this model, the system’s behavior revolves around event generation, detection, dispatch, and handling, thereby enabling a highly decoupled, asynchronous, and reactive system design.

- **Events**: A detectable occurrence, which carries the attribute parameters and type of the event, and defines specific events or signals that can cause a change in the system state or require a system response. It is the starting point of the entire model’s execution and may originate from external inputs (such as user clicks), internal state changes (such as data updates), or time-based conditions (such as a timer expiring).
- **Conditions**: After the event is triggered, this consists of the logical rules or preconditions used to determine whether the corresponding action should be executed. The execution prerequisites ensure that subsequent actions are performed only when specific conditions are met.
- **Actions**: The concrete operations or behaviors performed by the model when the event occurs and the conditions are satisfied. It represents the final realization of event handling and may include updating data, invoking services, granting rewards, sending messages, or triggering new events, thereby completing a full event-response cycle.

![ECA](assets/ECA.svg)

### Events Triggering

#### Attributes

**Attributes** are data fields carried by a specific business event, containing explicit business information. When an identifiable event occurs in the system (such as “user login” or “order payment success”), the event object is not merely a signal—it is a data packet. The various pieces of information contained in this packet are the event attributes. These attributes give the event concrete business meaning, transforming it from an abstract notification into a contextual object that can be used for logical decisions and business processing.

**Core issue: Which Events can be combined with which Conditions and Actions?** Each Event, when fired, provides a set of event attributes carrying specific business information (e.g., user ID, order amount). Each Condition and Action, in turn, declares the set of attributes it requires for execution (for example, “check amount” requires order amount; “grant reward” requires user ID). The system automatically determines whether a combination is valid and executable by checking whether the attributes provided by the Event satisfy the requirements of the Condition and Action. This mechanism allows Events, Conditions, and Actions to automatically adapt based on their data interfaces, supporting a flexible, reliable, and dynamically configurable business rules system.

#### Design Considerations

**Consistency issues in data dependencies between Events and Conditions**: Events may indicate the occurrence of a “local event” within the system, whereas Condition evaluation requires the “global state to be ready.” The essence of the problem lies in the temporal misalignment between the “local event” and the “global state readiness”: when a event occurs, other related data required for subsequent condition evaluation may not yet have been generated, persisted, or may still be in an inconsistent intermediate state. Because different system components process data at different speeds, a time gap emerges between cross-component state consistency and the requirements of business logic. Therefore, the key to system design is to clearly identify and define the true business moment at which “all required data are ready and consistent,” and to use that moment as a reliable triggering signal.

### Conditions Evaluation

The full lifecycle from rule-template configuration to final resolution of rules is a process of progressively aggregating information and refining conditions.

- **System**: Starting from a predefined condition template and static configuration, the system produces a set of baseline conditions that form the foundational framework. It then enriches these baseline conditions with dynamic contextual information to derive the final, complete condition set.

- **Rule Engine**: Rule Engine performs the final condition evaluation and emit resolution results.. Taking the complete condition as input and incorporating external system data (for example, payment status from third-party systems), it translates the condition into an executable internal representation (e.g., an abstract syntax tree and symbol table) that the rule engine can interpret. The engine then evaluates the logic and returns the result (decision, flags, or computed outputs).

![condition](./assets/condition.svg)

### Action Execution

When multiple actions are executed sequentially, the processing mechanism operates in two distinct modes:

- **Synchronous Action**: Due to the execution results of synchronous actions are immediately available, triggers subsequent actions automatically upon completion.
- **Asynchronous Action**: After initiation, the current execution flow pauses, awaiting a completion notification from an external system via the callback interface. The process resumes and proceeds to subsequent actions only after receiving the confirmation signal.

![202505252226](./assets/202505252226.svg)

## Relationship Model: ECA Graph

The most common approach to designing associations among ECAs is to use a **directed acyclic graph (DAG)** to model the relationships between multiple ECAs. Each ECA can be encapsulated as a node in the graph, making it the most fundamental execution unit—referred to as a **ECA Node**—within the system. The dependencies and execution order among these units are defined by the edges of the DAG, ensuring that all ECAs are executed in topological order while also enabling scheduling opportunities for nodes that can run in parallel.

1. **Strategy (ECA Graph):** A directed acyclic graph composed of multiple ECA Nodes and their dependency relationships. It defines the complete execution flow in complex business scenarios. By orchestrating ECA Nodes—such as sequencing, parallel execution, and conditional branching—it describes the system’s overall behavior and decision paths.

2. **ECA Node:** The basic node within a Strategy, encapsulating a complete ECA logical unit. It receives inputs, evaluates its internal Conditions, executes the specified Action when those conditions are met, and produces outputs. The dependency relationships between ECA Nodes determine the topological execution order and make the ECA Node the smallest building block of an executable workflow.

![202602052353](./assets/202602052353.svg)

### Flow Dimension: instantiating idempotency key

> **Flow dimension = event attribute A + event attribute B + …**

ECA graphs themselves, as well as each step within them, can be executed concurrently. To clearly track and isolate each independent business flow, the system needs to assign a **globally unique execution key** to every complete graph execution instance for identification.

1. **Idempotency of ECA graph transitions**: For the same user’s single transition request, whether the system receives it once or multiple times (for example due to network retransmission or repeated user clicks), the system will produce exactly one correct transition effect (such as a state update, points credit, or reward issuance) and will not cause duplicate payouts or state inconsistencies.
2. **Flow dimension**: The flow dimension is designed to realize idempotency for ECA graph transitions; it is formed by a combination of event attributes: event attribute A + event attribute B + …. Event attributes are typically parameters carried within the triggering event (Trigger). In real business scenarios, you may choose appropriate dimensions as the basis for idempotency according to the product’s specifics — for example user UID, order ID, estimated-call ID (used for callback operations), etc.

### Run

1. **Triggering and Initial ECA Selection**: When the system receives this event, it first retrieves all ECAs that are currently in the “in progress” state. Then, based on the trigger conditions, it traverses these ECAs to select the first ECA in each ECA path (i.e., the Head ECA) and aggregates them into a Head ECA set. The core purpose of this step is to focus on the starting ECA of each path, avoiding repeated processing of ECAs within the same path, thereby ensuring process efficiency and logical clarity.
2. **Iterate over Head ECAs and Execute Calculation Logic**: The system processes each ECA in the Head ECA set sequentially. For each Head ECA, the rule engine is first invoked to execute the calculation logic for the ECA—this is the core computational step, determining the that the ECA should receive. Next, the system checks whether the ECA has been completed: if the ECA is not completed, subsequent processing is skipped, and the system moves directly to the next Head ECA; if the ECA is completed, it proceeds to handle subsequent ECAs, advancing the ECA status forward.
3. **Recursive Processing of Subsequent ECAs (Core Logic)**: After the current Head ECA is completed, the system retrieves all ECAs associated as its subsequent ECAs and evaluates each one. It first checks whether the subsequent ECA currently meets its start conditions; if it cannot start, it is temporarily skipped. If it can start, its status is updated to “in progress,” and the system immediately executes the full “ECA Calculation Logic” recursively for this ECA. This loop continues, forming a recursive processing mechanism that allows the ECA chain to automatically and coherently advance step by step until the path ends.
4. **Process Completion**: Once the last ECA in the Head ECA set has been processed—that is, all starting ECAs and their triggered subsequent recursive chains have completed calculation and status evaluation—the entire calculation process concludes. At this point, the system has completed all ECA computations and status updates driven by the triggering event for this round.

![202512310127](./assets/202512310127.svg)



**Transitions between ECAs**:

**Transitions between parent and child nodes (vertical transitions)**: This defines how a child node can be initiated. Conditions can be set for when the current ECA can be activated, such as after all parent nodes are completed or when specific criteria are met.

- **All Completed (AND)**: The current node is set to Ready when all parent nodes are in the Completed state. Suitable for strict linear processes or scenarios where all prerequisite ECAs must be completed.
- **Any One Completed (OR)**: The current node is set to Ready when any one of the parent nodes is in the Completed state. Suitable for multi-path selection scenarios, where completing any prerequisite ECA can initiate subsequent ECAs.
- **Custom Quantity (M-of-N)**: The current node is set to Ready when M out of N parent nodes are in the Completed state. Suitable for scenarios that require a certain proportion of prerequisite conditions to be met.

**Transitions between sibling nodes (horizontal transitions)**: This defines the mutual influence between child nodes under the same parent node. ECAs can be established for transitions between child nodes under the same parent node.

- **Mutual Exclusion**: Multiple branches where only one can be selected. Once one path is chosen, the others are closed. When one sibling node is unlocked (becomes Ready) or activated (becomes Active), all other sibling nodes immediately become Invalid.
- **Parallel**: All sibling nodes are independent of each other and can individually become Ready based on the parent-child ECAs.
- **Dependency**: There is an implicit sequential dependency among sibling nodes.

### Cross-ECA Node Communication

### Scope of DAG Support Capabilities

## Complex System: Strategy Group



![202602060036](./assets/202602060036.svg)

## Core implementation: Rule Engine