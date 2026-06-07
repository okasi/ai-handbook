# 🔁 Loop Engineering

Loop engineering is the discipline of structuring, optimizing, and safeguarding autonomous agentic loops (execution cycles) to make them resilient, performant, and self-correcting.

---

## 🔗 Key References

*   **Peter Steinberger (@steipete) on Agentic Loops & OpenClaw**
    *   [X Status 2063697162748260627](https://x.com/steipete/status/2063697162748260627)
    *   *Focus:* Building autonomous coding systems, tracking agent trajectories, and structuring reliable execution loops.

*   **Rohan Paul (@rohanpaul_ai) on Claude Code & Agentic Workflows**
    *   [X Status 2063289804708835412](https://x.com/rohanpaul_ai/status/2063289804708835412)
    *   *Focus:* The shift toward Claude Code, agentic coding loops, and orchestrating multi-step autonomous workflows instead of writing manual prompts.

---

## 🛠️ Core Principles of Loop Engineering

1. **Self-Correction & Verification:** 
   Always integrate testing, parsing, and linting directly into the agent's action loop. If an execution step fails, let the agent read the error output and correct its course autonomously.
   
2. **Harness & Proxy Middleware:**
   Instead of modifying core prompts to handle formatting errors, wrap local inference setups with a middleware harness (e.g. a lightweight Fastify Node.js proxy). Use this harness to normalize malformed JSON, prune thoughts/scratchpads, and handle transient API timeouts.
   
   *See [local-llm.md](./local-llm.md) for details on harness implementation.*
   
3. **Turn & Timeout Tuning:**
   Tune your loop thresholds based on target model capabilities. Slow local models require more turns (e.g. bumping `max_turns` to 50+) and longer gateway timeouts to prevent premature agent stalling.
   
   *See [hermes.md](./hermes.md) for config examples.*
   
4. **Context Resets:**
   Prevent context accumulation bloating by implementing policy-gated resets or auto-compaction between major subtasks.
