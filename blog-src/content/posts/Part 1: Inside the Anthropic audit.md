---
title: "Part 1: Inside the Anthropic audit"
date: 2026-11-12
description: "How automated reinforcement learning triggered a self-improvement loop."
draft: false
---

# Part 1: Inside the Anthropic audit: how automated reinforcement learning triggered a self-improvement loop

On November 5, 2026, the independent evaluation group METR published an eleven-week audit titled *Independent Assessment of AI R&D Acceleration at Anthropic*. Anthropic blacked out several passages before release, but the core numbers stayed in. After October 2025, Anthropic's internal AI models improved on software engineering and research benchmarks at 1.9x the pace recorded between March 2024 and October 2025. When METR adjusted its statistical model with heavy-tailed Cauchit and Log-Log link functions (formulas that account for top models bunching up near the 100 percent ceiling on older tests), the estimated acceleration rose to between 2.6x and 2.9x.

At that rate, two to three years of historical AI progress happens inside twelve months. METR attributed roughly 80 percent of the speedup to Anthropic using its own models to run the research that builds the next generation. Matching the anonymized staff interviews against the blacked-out sections shows the mechanism: Anthropic's unreleased models crossed a threshold in coding reliability that let the company automate the construction and execution of reinforcement learning environments.

![METR AECI capability chart showing 1.9x acceleration after October 2025 and rapid internal checkpoints from Mythos Preview to Model 4](https://metr.org/assets/images/ai-rd-acceleration-assessment/aeci-main.png)

### Pretraining compute vs. post-training experiments

Building a frontier AI model happens in two stages: pretraining and reinforcement learning.

Pretraining feeds a massive neural network trillions of words of internet text and source code so the system learns statistical patterns in human language. For the past five years, AI labs made models smarter mainly by throwing more computer chips and electricity (measured in floating-point operations, or FLOPs) at pretraining. A model trained on ten times more computing power reliably came out smarter than its predecessor.

When METR inspected Anthropic's internal training logs, the auditors ruled out pretraining compute as the cause of the post-October 2025 speedup. Anthropic's spending on raw pretraining hardware grew along its normal historical line. The upward bend in model capability came from the second stage of development: post-training reinforcement learning and faster algorithmic experimentation.

### How reinforcement learning works and where humans bottlenecked it

A pretrained model acts like a well-read student who guesses plausible-sounding answers without checking whether they work. Because it learned by predicting the next word in a sentence, it can write a convincing paragraph that contains a broken software function or a flawed math proof.

To turn a word-guesser into a reliable problem solver, labs use reinforcement learning (RL). Reinforcement learning works like a digital flight simulator. Engineers lock the AI inside an isolated computer workspace with a concrete problem, such as fixing a bug in a codebase or proving a theorem. The model writes a solution, runs the program, reads the error output, and tries again. When its code compiles and passes an automated grading test, the model receives a positive mathematical score. When the code fails, it gets nothing. After millions of trial-and-error runs across thousands of problems, the model learns which step-by-step reasoning chains actually pass the test.

For the past three years, the main constraint on reinforcement learning was human setup time. Supercomputers can run millions of practice attempts in a few hours, but a human software engineer still had to build the digital obstacle course first.

Before an AI can practice solving a new category of problems, a human researcher has to spend one to two days building a secure software container, gathering clean starting problems, writing an automated grading script (called a reward function or verification rubric), and checking that the model cannot trick the grader through a loophole. Because human researchers only have forty or fifty working hours in a week, they had to ration their experiments. Nine out of ten training ideas never got tested because nobody had two spare days to write the setup code and babysit the servers.

### Decoding the `[agent-scaffold]` redactions

The staff interviews inside the METR report point to a specific moment in late 2025 and early 2026 when that human bottleneck broke.

Researchers described a sharp dividing line between Anthropic's public models (Opus 4.5 and Opus 4.6) and an unreleased internal checkpoint called `mythos-preview`, which Anthropic paired with an internal software harness that the report blacks out as `[agent-scaffold]`. One researcher laid out the internal timeline directly: before Opus 4.5, Claude was useless for autonomous coding; Opus 4.5 was the first model useful for writing code autonomously; `mythos-preview` paired with `[agent-scaffold]` was the first system useful for running an entire research experiment autonomously.

Earlier models could write short functions, yet they fell apart when asked to set up and manage a multi-day training experiment on their own. They would misconfigure a file path, invent a nonexistent software library, or get stuck in an error loop. `mythos-preview` was the first checkpoint reliable enough to build a testing environment, launch a job on Anthropic's computing cluster, fix its own setup errors, and collect the results from start to finish.

In the interviews, researchers explained what happened once `[agent-scaffold]` went live. One staffer noted that engineers previously skipped testing complex ideas because each experiment took one to two days to implement by hand, whereas with `mythos-preview` setting up an experiment became nearly free. Another researcher stated that teams were finding productive new training directions almost entirely because an engineer could throw ten ideas at `[agent-scaffold]` at the same time and see which ones worked. An engineering manager who oversees ten researchers told auditors that they could now test and de-risk new training ideas with Claude overnight before assigning a team to them, something a manager had zero time to code manually before.

Those interviews explain the two largest technical redactions in the report.

In the section examining fully autonomous AI contributions to research, METR disclosed that Anthropic blacked out a quantitative breakdown of what percentage of tasks in a key pipeline were built by humans versus AIs, along with historical data on algorithmic efficiency and model strength in pretraining and RL. Anthropic also blacked out a specific metric on its internal inference compute usage because the number was stated in raw computing terms, and canceled METR's planned verification of how Anthropic divides its total computing power across different departments midway through the audit.

In AI terminology, "training compute" is the chip time spent updating a model's brain weights, while "inference compute" is the chip time spent running a finished model to generate outputs. When an AI agent inside `[agent-scaffold]` designs reinforcement learning environments, writes the grading tests, and runs millions of practice attempts inside those containers, inference compute and RL training compute come from the same pool of hardware. Over 90 percent of the computing work in modern agentic reinforcement learning consists of running models inside software containers to generate practice trajectories and running grader models to score them.

Anthropic fought to redact those numbers because their breakthrough is an operational recipe and a hardware budget ratio. Once a model crosses the reliability threshold where it can build and grade its own RL obstacle courses, a lab can divert a large share of its supercomputer cluster to running automated RL experiments 24 hours a day.

Rival labs including OpenAI, Google DeepMind, and xAI have coding models near that same reliability threshold. METR's industry-wide benchmark chart in the appendix shows that capability growth across all major labs combined sped up by 1.4x after October 2025. Every frontier lab realized around the same time that they could point their coding models at their own training pipelines. What competitors do not know is the exact calibration: whether letting AI generate 80 percent of your RL tasks causes the model to start cheating its own grading tests (a failure mode known as reward hacking), or whether diverting 40 percent of a supercomputer cluster from pretraining to internal agent rollouts yields the highest return. Revealing those two numbers would hand competitors a calibrated recipe worth billions of dollars in saved cluster time.

### Three models in sixteen weeks: soft recursive self-improvement

In computer science, Recursive Self-Improvement (RSI) describes the threshold where an AI system becomes good enough at building AI that it accelerates its own improvement in a compounding loop. Both Anthropic and METR step carefully around that phrase. Because a human researcher still sits at a keyboard typing a three-sentence hypothesis into `[agent-scaffold]` to kick off a batch of experiments, the report characterizes the current process as AI-assisted research while noting early signs of fully autonomous contributions.

The training completion dates in METR's methodology appendix show how tight that feedback loop has become. Training a major frontier model from scratch normally takes four to six months. After Anthropic deployed `mythos-preview` with `[agent-scaffold]`, the company finished training three successive internal frontier models in roughly sixteen weeks. `Model 2` finished training in July 2026, delivering another large jump in practical usefulness inside `[agent-scaffold]` over `mythos-preview`. `Model 3` finished between August and September 2026, and `Model 4` finished between September and October 2026.

No lab trains three separate giant base models from scratch in four months. `Models 2, 3, and 4` are sequential reinforcement learning and post-training checkpoints branched off an existing base model. As soon as `Model 2` finished in July, Anthropic plugged it back into `[agent-scaffold]` to run faster experiments and generate harder synthetic training tasks to build `Model 3` in August, which was immediately used to help produce `Model 4` in October. By the time `Model 4` finished, staff reported speedups of up to 12x on specific research workflows, and `Model 4` scored above 50 percent across all fifteen benchmarks in Anthropic's internal evaluation suite.

![Informal self-reported R&D uplift over time from Anthropic employees showing exponential and superexponential fits up to 12x on Model 4](https://the-decoder.com/wp-content/uploads/2026/11/METR-November26-self-reported-uplift.png)

When each new model checkpoint directly multiplies the number of experiments and training tasks used to build the next checkpoint thirty days later, the feedback loop is closed. A human engineer still spends ten minutes reviewing summary charts and typing a three-sentence prompt between iterations, which adds a small delay without changing the underlying feedback loop.

In a standard exponential system, capabilities double over a fixed number of months. In a self-reinforcing loop where the rate of improvement grows in proportion to the model's current capability, the math produces hyperbolic growth, meaning the time between major capability jumps shrinks with every cycle. In the first bullet of the audit's limitations section, METR warned that Anthropic's growth rate could be undergoing continuous hyperbolic acceleration, and disclosed that Anthropic redacted the internal evidence that made hyperbolic growth look plausible.
