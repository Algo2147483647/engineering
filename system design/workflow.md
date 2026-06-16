# Workflow / Process Orchestration

[TOC]

**Workflow / Process Orchestration** is a architectural paradigm used to model, coordinate, and execute a multi-step business or system process. A complex and complete work process/workflow is decomposed into a sequence or graph of independent tasks (steps), each with defined inputs, outputs, and execution conditions. Orchestration is the control layer that coordinate these tasks by scheduling, which includes allocating resources, starting and stopping execution, and monitoring execution states.

## Tasks

Tasks are the fundamental building blocks of a workflow. Each task represents a distinct, self-contained piece of work.

- **Input & Output:** The data or resources required for the task to begin execution.
- **Process Logic:** The specific operation, calculation, or action performed within the task.
- **Conditions:** The conditions or triggers that must be met for the task to execute, such as dependencies on other tasks or external events.
- **Resources**
- **Error/Exception Handling**

### State of tasks & State transition

The state of a tasks represents its entire lifecycle and potential abnormal situations it may encounter.

- Pending: The task is defined but has not started yet.
- Ready: The task is prepared to execute because all dependencies and conditions are met.
- Running: The task is actively executing.
- Completed: The task has successfully executed and produced its expected output.
- Paused: The task is temporarily paused, awaiting external input, user intervention, or resolution of an issue.
- Stopped: The task was deliberately stopped before or during execution.
  - Timed Out
- Skipped: No execution is performed, but the workflow continues with other tasks.
- Retrying: The task is re-executing after a previous failure.

## Relationship between Tasks

- Sequential Dependency: One task can only begin after the completion of another.
- Parallel Execution: Multiple tasks execute simultaneously without depending on each other.
- Iterative Relationship (Loops): A task or group of tasks is repeatedly executed until a specific condition is met.

### Orchestration

- Workflow collection & Task units collection



![Processor](assets/Processor.svg)


![Workflow](assets/Workflow.svg)

Process Definition, The **process definition** is the static template of the workflow.

Process Instance, A **process instance** is one runtime execution of a process definition.

Error Handling

