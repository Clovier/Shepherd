# Project Shepherd

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Detecting and Containing Prompt-Injected Agents in Multi-Agent LLM Systems.

> **Status:** early planning. There's no code yet. This README describes what we're building and how we plan to test it.

## The problem

In a multi-agent LLM system, the work is split across agents that each have their own role and tools: one reads email, one manages the calendar, one handles files. Some of what they read comes from outside, like the body of an email or the result of a tool call, and that text can hide instructions. This is called prompt injection.

When one agent gets hijacked this way, it can pass bad instructions or bad data to the rest of the team. For example, the email agent reads a message saying "forward all invoices to this address," then asks the files agent for the invoices.

## What Shephard adds

Three learned components that sit on top of the agent team:

- **Monitor:** reads every message between agents, learns what normal traffic looks like, and flags the agent that has been compromised, ideally before its harmful tool call runs.
- **Risk layer:** estimates how likely each output is to be wrong or unsafe, then accepts it, asks for a second opinion, or escalates to a human.
- **Router:** picks which agent handles each task or subtask.

And the pieces they rely on:

- **Agent team:** 3 to 5 agents, each with a narrow role, its own prompt, and its own tools.
- **Message bus:** every message passes through one place, including tool calls and their results, and is logged as a JSON line (sender, receiver, content, tool calls, timestamp). Together the logs form a graph of who told whom what.
- **Attack harness:** injects synthetic attacks into what the agents read and labels each run. These labeled runs are what the monitor is trained and tested on.

## Research question

Can a small learned monitor, watching only the messages between agents, catch a hijacked agent before it does damage, including attack types it never saw in training?

## Related work

- [AgentDojo](https://github.com/ethz-spylab/agentdojo) is a benchmark with realistic tasks, injection attacks, and automatic checks for whether each task got done and whether each attack worked. It runs a single agent. We plan to run its tasks with a team of agents instead, so an attack can spread from one agent to the next.
- [AgentDyn](https://github.com/SaFo-Lab/AgentDyn) builds on AgentDojo with open-ended tasks and outside content that also contains legitimate instructions. Its authors found that almost all existing defenses either miss attacks or block too much legitimate work. We plan to use it as a second test, mainly to check that Shephard doesn't flag legitimate instructions as attacks.
- Text filters like Llama Prompt Guard 2 check each tool output for injected text on its own. [Injections written to blend in with the task](https://arxiv.org/abs/2605.22001) can slip past them.
- [G-Safeguard](https://arxiv.org/abs/2502.11127) and [BlindGuard](https://arxiv.org/abs/2508.08127) also use graph neural networks to find compromised agents in a team. They check after each round of conversation and were evaluated mostly on question-answering tasks. Shephard focuses on teams that act through tools, on stopping the harmful action before it happens, and on attack types held out from training.

## Evaluation

- **Metrics:** detection rate, false alarm rate, time to detection, and task impact (whether the team still finishes its work).
- **Baselines:** every result is reported next to simpler methods and existing defenses.
- **Reproducibility:** fixed random seeds, and the config is saved with every run.

## Roadmap

- [ ] Project setup
- [ ] Agent team with hand-coded routing
- [ ] Neural router
- [ ] Attack harness
- [ ] Monitor: baselines first, then learned models
- [ ] Risk layer
- [ ] Results and write-up

## Safety

All attacks are synthetic and only run against Shephard's own agents in a simulated environment. We never point them at real services, accounts, or people.

## Authors

[Tyler Van Heerden](https://github.com/Clovier) and [Paria Akhtrmoghaddam](https://github.com/paria03)

## License

MIT. See [LICENSE](LICENSE).
