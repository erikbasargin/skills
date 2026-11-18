---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled. Ask only questions from the current frontier. Give a recommendation for each question and wait for the user's answers before asking any dependent questions.

## Asking questions in Codex

Use Codex's built-in question pop-up when a user-input tool is available. Prefer `request_user_input` when permitted, or use `request_user_input_async` when exposed. Ask each decision as a separate question with a concise prompt and meaningful choices. Put your recommended option first and explain the tradeoff briefly where the tool supports option descriptions. Use free-text input when the tool supports it and meaningful choices would be premature.

If the frontier exceeds the tool's question limit, split the round into batches and finish offering those batches before starting the next round. Keep at most one batch pending. When the tool returns asynchronously, continue fact-finding if useful and use the user's reply as the answer. A preselected option, dismissal, or missing answer leaves the decision open. Carry open decisions forward and revisit them when needed.

If the tool is unavailable or cannot represent a question well, ask in chat. For a batch in chat, number the questions and word each so that "yes" accepts your recommendation:

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>

---

❓ **Q2** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer>
```

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open in this round belongs to a _later_ round, not this one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent with a fresh context window to find it; don't ask the user for anything you could look up yourself. Don't block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait for the sub-agent to report; ask the rest of the frontier now. The _decisions_ are the user's: put each to them and wait.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
