---
name: advisor
description: "Consult a user-selected model and optional reasoning effort for a second opinion on decisions, milestones, or hard tasks. Use for requests such as 'advise gpt-6 astra on key decision', 'consult with gpt 6.1 terra xhigh on decision', or an ongoing advisor assignment."
---

# Advisor

Obtain an actual assessment from an advisor subagent, then use it to advance the user's task. The main agent owns the final decision and implementation.

## Interpret the request

Treat "advise <model> on <topic>", "consult with <model> [effort] on <topic>", "ask <model> for a second opinion", and `$advisor` as consultation requests. Resolve references such as "this decision" from the current task. Ask for the decision only when the context leaves materially different interpretations.

Read the requested model, optional reasoning effort, topic, and consultation scope. Model and effort choices are independent.

- For a named decision or milestone, consult once. A focused follow-up is appropriate when the answer needs clarification.
- When the user assigns an advisor throughout a task, retain that assignment in the chat and consult at consequential decisions, milestone reviews, and difficult blockers within that task. Consult before committing to a choice whose outcome the advice could change. Group related questions and reuse relevant advice until new evidence changes the decision.
- A later model or effort request updates the assignment within the user's stated scope. A one-time override applies only to that consultation. End ongoing consultation when the task finishes or the user cancels it.

## Resolve the model and effort

Inspect the live subagent tool description for available model IDs, supported efforts, and override rules. Accept any model the active tool supports. Normalize case, spaces, and hyphens only when they identify one unambiguous supported model. For example, `gpt 6.1 terra` can resolve to `gpt-6.1-terra`, and `gpt-6 astra` can resolve to `gpt-6-astra`. Preserve version numbers. Resolve a short family name only when the available choices make it unambiguous.

Use the user's explicit effort exactly when supported. Normalize ordinary spellings such as "extra high" to `xhigh` if that is a supported value. When effort is omitted, use the tool's default for the selected model. When the model is omitted, use the current task's advisor assignment if one exists, otherwise inherit the main agent's model. An existing ongoing assignment also supplies an omitted effort when its model still matches the selected model. Follow the live tool's inheritance rules when both overrides are omitted.

If the requested model or effort is unavailable or ambiguous, explain the specific issue and ask for a supported choice. Do not silently substitute another model or effort, or impersonate the requested advisor. If the tools cannot create an advisor, report that limitation. Scope any such blocker to the consultation and continue independent authorized work.

## Consult the advisor

Briefly tell the user which model will advise and the explicit effort, or that its default effort will be used.

Spawn one advisor using the available subagent tool. With `collaboration.spawn_agent`, model or effort overrides require `fork_turns="none"`. Set `model` and `reasoning_effort` as resolved above, omitting fields that should use the tool's defaults. Full-history forks inherit the parent settings and cannot be used to select a different model or effort. Check the active schema if the tool changes.

Give the advisor a self-contained brief, especially when using `fork_turns="none"`. Include the user's objective, the specific decision, constraints, relevant evidence, candidate approaches, work already tried, and the exact help needed. Provide relevant file paths or excerpts and any essential artifact context. Label uncertain facts and give the advisor room to challenge the current approach.

Instruct the advisor to inspect relevant evidence and provide advice only. It must avoid file edits, external mutations, further delegation, and recursively invoking this skill. Ask for a recommendation, its main tradeoffs, material risks or missing evidence, and the next useful action or check. Request source or file references when its claims depend on them. Keep the depth proportionate to the decision.

Continue work that is independent of the consultation while the advisor runs. Obtain the completed response before making the decision under review. Use the existing advisor for clarification on the same topic, supplying any changed facts. Spawn a fresh advisor when the requested model or effort changes. Follow the live tools' lifecycle rules when reusing a completed agent.

## Apply and report the advice

Evaluate the recommendation against the user's constraints and available evidence. Resolve important factual disagreements with targeted checks. Carry out the next authorized step when the user requested ongoing execution. For an advice-only request, return the assessment.

Tell the user which advisor was actually called, the effort explicitly used or that its default was requested, the main recommendation, and what you adopted or changed. Mention a material disagreement when it affects your decision. Claim a successful consultation only after a real advisor response arrives. If the consultation fails, describe the failure and keep your own judgment clearly attributed.

## Examples

- "advise gpt-6 astra on key decision" requests one consultation with `gpt-6-astra` on the current key decision, using its default effort unless an applicable assignment supplies one.
- "consult with gpt 6.1 terra xhigh on decision" requests `gpt-6.1-terra` at `xhigh` for the current decision.
- "Use GPT-6 Astra high as an advisor at milestones and whenever this task gets hard" establishes that model and effort for consultations during the current task.
- "$advisor consult gpt-6-luna medium on this approach" requests one consultation with those settings, subject to the live tool's support.
