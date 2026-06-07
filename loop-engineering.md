# 🔁 Loop Engineering

> "Stop prompting AI directly. Start building loops that prompt, evaluate, and decide what to do next. Design the loops. That is the transition we'll see for the rest of 2025."
>
> "I prompt for the llm to prompt the llm"

---

## 🔗 Key References

*   **Peter Steinberger (@steipete)** — [X Status 2063697162748260627](https://x.com/steipete/status/2063697162748260627)
    *   Building autonomous coding loops and tracking agent trajectories.
*   **Rohan Paul (@rohanpaul_ai)** — [X Status 2063289804708835412](https://x.com/rohanpaul_ai/status/2063289804708835412)
    *   Autonomous workflows and Claude Code replacing manual prompting.

---

## 🛠️ Core Principles

*   **Self-Correction:** Integrate verification steps (parsing, tests, linting) into the agent's action loop to enable autonomous error correction.
*   **Harness/Proxy Middleware:** Wrap local inference (e.g. `llama-server`) with a lightweight proxy (like Fastify) to normalize responses, enforce samplers, and handle retries/timeouts. *(See [local-llm.md](./local-llm.md))*
*   **Tuning Limits:** Raise `max_turns` (e.g., to 50+) and timeouts for local inference to give slower models breathing room. *(See [hermes.md](./hermes.md))*
*   **Context Resetting:** Auto-compact or reset the context between major tasks to prevent context accumulation bloat.
