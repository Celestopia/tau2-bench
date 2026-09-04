# Reading guide: how the airline text benchmark works

This guide is for learning the benchmark's detailed mechanisms, with a focus on the airline domain in text mode. Voice, other domains, and implementing personal agent experiments are outside its current scope.

The existing documentation explains the overall workflow, domain structure, agent interfaces, and scoring. Understanding prompt construction, initialization, turn routing, termination, and aggregate metrics also requires reading selected source files. Follow the order below rather than reading the repository from beginning to end.

## 1. Understand the components and outputs

Skim the overview in [README.md](../README.md), then read “Running Your First Evaluation” and “Simulation Output Structure” in [Getting Started](getting-started.md).

Distinguish the agent being evaluated, simulated customer, environment and tools, and evaluator. Also distinguish a task definition from one simulation run of that task.

**Reading goal:** Explain who participates in a simulation and what gets saved afterward.

## 2. Learn the airline world

Read [Domains](../src/tau2/domains/README.md), followed by the full [airline policy](../data/tau2/domains/airline/policy.md).

The policy defines the agent's obligations: what it can book, modify, or cancel; when it needs confirmation; and when it must refuse or transfer. Treat it as the benchmark's simulated business policy, including its fixed date.

For implementation details, follow [airline/environment.py](../src/tau2/domains/airline/environment.py) into [tools.py](../src/tau2/domains/airline/tools.py) and [data_model.py](../src/tau2/domains/airline/data_model.py). There is no need to read the entire database.

**Reading goal:** Distinguish the policy the agent must follow from the operations exposed by tools and the state stored in the database.

## 3. Understand tasks and rewards

Read [Task Schema and Evaluation](evaluation.md) alongside task `"1"` in [airline/tasks.json](../data/tau2/domains/airline/tasks.json). The documentation walks through that exact task.

Pay particular attention to:

- `user_scenario`: instructions for the simulated customer.
- `initial_state`: optional setup before the interaction.
- `evaluation_criteria`: reference actions and checks.
- `reward_basis`: which checks determine the reward.

Reference actions are not a mandatory tool-call script. Airline scoring uses the database outcome and required communication. Receiving full reward does not establish that every policy obligation was satisfied: some checks are diagnostic rather than reward-bearing.

**Reading goal:** Explain why an agent can receive full reward without reproducing the reference actions, and why an assertion listed in a task may not affect its reward.

## 4. Understand what each participant knows

In the [Agent Developer Guide](../src/tau2/agent/README.md), read “Overview,” “HalfDuplexAgent,” and “LLMAgent.” Skip voice and custom-agent implementation instructions for now.

Then read the [user simulation guidelines](../data/tau2/user_simulator/simulation_guidelines.md). These explain how the customer should reveal information and behave.

For exact prompt construction and message histories, inspect [llm_agent.py](../src/tau2/agent/llm_agent.py), [user_simulator.py](../src/tau2/user/user_simulator.py), and the builders in [build.py](../src/tau2/runner/build.py).

**Reading goal:** Identify which information reaches the agent, which reaches the user simulator, and which stays with the evaluator.

## 5. Follow one conversation through execution

Read only the half-duplex section of the [Orchestrator overview](../src/tau2/orchestrator/README.md).

Then inspect `Orchestrator.initialize()`, `step()`, `_check_termination()`, and `_finalize()` in [orchestrator.py](../src/tau2/orchestrator/orchestrator.py).

Follow how messages go to the user, agent, or environment; how tool results return; and how stop signals, errors, and step limits end a run. The overview is brief, so use the implementation to understand these details.

**Reading goal:** Trace a user message, an agent tool call, its result, and the agent's next response, then explain how the simulation ends.

## 6. Connect execution to evaluation and batch results

Read the [Runner overview](../src/tau2/runner/README.md), then [simulation.py](../src/tau2/runner/simulation.py). This connects running the conversation to evaluating its trajectory.

For deeper scoring details, inspect [evaluator_env.py](../src/tau2/evaluator/evaluator_env.py): it reconstructs the predicted environment from the recorded trajectory and constructs a gold environment using reference actions.

Finally, inspect `pass_hat_k()` and `compute_metrics()` in [agent_metrics.py](../src/tau2/metrics/agent_metrics.py) to understand how individual trials become reported benchmark metrics.

**Reading goal:** Explain the path from a saved conversation to a task reward and then to aggregate results across tasks and trials.

## Documentation gaps and reading caveats

The main gaps worth addressing are:

- An end-to-end airline example connecting task data, prompts, tool execution, state changes, and reward.
- A clear account of information available to each participant.
- Detailed explanations of the conversation lifecycle and metric aggregation.

At the time of this review, the runner README describes `tau2.run` as a separate monolithic implementation awaiting conversion, but [run.py](../src/tau2/run.py) already delegates to `tau2.runner`. Use documentation as a map and the implementation to resolve discrepancies.

Start with steps 1–3. They establish the task and scoring concepts needed to ask precise questions about execution mechanics.
