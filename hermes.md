https://x.com/sudoingX/status/2055548902099894480

if your agent keeps crashing on local inference here's what to check:

> max_turns: default is tuned for fast frontier models. bump from 30 to 50. slow local models need more breathing room per agentic loop.

>gateway_timeout: raise from 600 to 1200. local inference at 12-17 tok/s will timeout silently and look like crashes.

> context accumulation: auto-reset is off by default. your session grows until you /reset. long convos choke the agent. reset between major tasks.

if you're running anything under 20 tok/s locally, these three settings are the difference between "broken" and "flying." tune your config before you blame the tool.


```
Update Hermes Config/Settings:
- max_turns: bump from 30 to 50
- gateway_timeout: raise from 600 to 1800
```
