# Strategy System

## Strategy

**Strategy is the structured expression of business intent.** Business intent refers to the outcome that operators want to achieve, but at the outset this intent is often vague, abstract, and shaped by subjective judgment. The role of strategy is to break that intent down into a clear logical structure so that the system can understand it and drive execution.

### Strategy Rule

#### Strategy Object



### Strategy Lifecycle

## Strategy System

## Strategy Configuration System

Many strategy actions are not fixed. Instead, they are continuously adjusted based on factors such as campaign objectives, audience characteristics, trigger timing, promotion rules, and engagement channels. To enable operators to configure these rules flexibly while ensuring that the system can execute them stably and accurately, strategy needs to be abstracted from code logic and uniformly expressed, managed, and compiled through a configuration service.

A **strategy configuration service** is a system capability used to carry, manage, and generate  strategies.

### Form-based strategy configuration

The entire process can be summarized as follows: operators complete parameter configuration through forms. Based on the form structure and component definitions, the system parses, validates, and compiles these configuration contents into runtime strategy objects.

In this process, the form is responsible for expressing and carrying configuration. It organizes the parameters filled in by operators into structured configurations. Components are responsible for converting configuration into strategies. They parse, validate, and compile local configurations within the form. Strategy output refers to the set of objects that the system can actually recognize and execute at runtime.

1. **Input Layer**: Operators fill in parameters. The form structure defines how the parameters are organized, their hierarchical relationships, and constraint rules, ultimately forming a complete form configuration.
2. **Component Compilation Layer**: The system parses the form into a key-value collection and maps it to corresponding component instances based on component definitions. Components inspect, parse, and compile the configuration content, generating the corresponding strategy fragments.
3. **Strategy Output Layer**: After component compilation, the system generates runtime objects such as strategy groups, activity strategies, and execution units, transforming the original configuration into strategy output that can be scheduled, executed, and managed by the system.

<img src="./assets/202606170400.png" alt="202606170400" style="zoom: 21%;" />

#### Component: The Minimum Compilable Unit of Strategy Configuration

A component is the smallest compilable unit of strategy configuration and the core bridge between “form configuration” and “strategy output.” It transforms form fields that operators can understand and configure into strategy objects that the system can parse, schedule, and execute. It is both a configuration unit within the form and a compilation unit in the strategy generation process.

- **On the form side**, a component appears as a configurable control, such as an audience selector, time configurator, coupon configurator, or engagement channel configurator. Operators fill in specific parameters through these components to express local logic.

- **On the compilation side**, a component is not merely a frontend control, but a strategy unit with business semantics and compilation capability. It can read key-value configurations from the form, validate their legality, parse their structure, perform semantic transformation, and compile them into strategy fragments that the system can recognize. Each component is responsible for generating part of the strategy. Multiple components collaborate according to the form structure and are compiled together to eventually form a complete executable strategy.

![The compilation chain from business policy configuration to runtime policy](./assets/img1.png)

### Canvas-based strategy configuration

## Strategy Execute System

## Strategy Evaluate System

## Strategy Reach System

