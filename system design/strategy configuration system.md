# Strategy Configuration System

## Background

### Strategy 

A strategy refers to a combination of rules and actions designed for a specific objective or business scenario. It describes under what conditions, for which target objects, what kind of judgment should be triggered, and what actions should be executed.

For example, “If a new user has not placed an order within 24 hours after registration, send them a coupon of 20 off for orders over 100” is a typical strategy. In this example, “new users” are the target objects, “24 hours after registration” is the trigger timing, “has not placed an order” is the judgment condition, “send a coupon” is the execution action, and “20 off for orders over 100 coupon” is the resource configuration.

Therefore, a strategy is essentially a structured expression of business decision logic. It typically includes core elements such as target audience, trigger timing, judgment conditions, execution actions, and resource configuration.

- **From the business perspective**, a business strategy answers the question: “How should this be done?” Business users focus on the strategy objective, rule content, and execution outcome, such as which audience to operate on, when to engage them, what benefits to provide, and through which channel to reach them.

- **From the system perspective**, a business strategy answers the question: “How should the system execute it?” The system needs to decompose the business strategy into structured objects that can be recognized, evaluated, scheduled, and executed, such as triggers, condition judgments, action executions, resource references, and frequency control rules.

### Strategy Configuration System

In marketing, growth, operations, and other business scenarios, many business actions are not fixed. Instead, they are continuously adjusted based on factors such as campaign objectives, audience characteristics, trigger timing, promotion rules, and engagement channels.

To enable business users to configure these rules flexibly while ensuring that the system can execute them stably and accurately, “business strategy” needs to be abstracted from code logic and uniformly expressed, managed, and compiled through a configuration service.

A **business strategy configuration service** is a system capability used to carry, manage, and generate business strategies.

## Form-based strategy configuration

The entire process can be summarized as follows: business users complete business parameter configuration through forms. Based on the form structure and component definitions, the system parses, validates, and compiles these configuration contents into runtime strategy objects.

In this process, the form is responsible for expressing and carrying business configuration. It organizes the parameters filled in by business users into structured configurations. Components are responsible for converting configuration into strategies. They parse, validate, and compile local configurations within the form. Strategy output refers to the set of objects that the system can actually recognize and execute at runtime.

1. **Input Layer**: Business users fill in business parameters. The form structure defines how the parameters are organized, their hierarchical relationships, and constraint rules, ultimately forming a complete form configuration.
2. **Component Compilation Layer**: The system parses the form into a key-value collection and maps it to corresponding component instances based on component definitions. Components inspect, parse, and compile the configuration content, generating the corresponding strategy fragments.
3. **Strategy Output Layer**: After component compilation, the system generates runtime objects such as strategy groups, activity strategies, and execution units, transforming the original business configuration into strategy output that can be scheduled, executed, and managed by the system.



![The compilation chain from business policy configuration to runtime policy](./assets/img1.png)

To make it easier to understand the complete transformation process from business configuration to executable strategies, the following section provides a unified explanation of the core concepts involved in this process.

### Component: The Minimum Compilable Unit of Strategy Configuration

A component is the smallest compilable unit of strategy configuration and the core bridge between “form configuration” and “strategy output.” It transforms form fields that operators can understand and configure into strategy objects that the system can parse, schedule, and execute. It is both a configuration unit within the form and a compilation unit in the strategy generation process.

- **On the form side**, a component appears as a configurable control, such as an audience selector, time configurator, coupon configurator, or engagement channel configurator. Operators fill in specific parameters through these components to express local logic.

- **On the compilation side**, a component is not merely a frontend control, but a strategy unit with business semantics and compilation capability. It can read key-value configurations from the form, validate their legality, parse their structure, perform semantic transformation, and compile them into strategy fragments that the system can recognize. Each component is responsible for generating part of the strategy. Multiple components collaborate according to the form structure and are compiled together to eventually form a complete executable strategy.

## Canvas-based strategy configuration
