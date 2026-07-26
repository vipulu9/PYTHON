/home/vuniyal/git/summarizeragent_agent/app/agent/.venv/lib/python3.12/site-packages/a2a/server/request_handlers/default_request_handler.py:196: UserWarning: The default A2A response stream implemented in the strands sdk does not conform to what is expected in the A2A spec. Please set the `enable_a2a_compliant_streaming` boolean to `True` on your `A2AServer` class to properly conform to the spec. In the next major version release, this will be the default behavior.
  await self.agent_executor.execute(request, queue)
INFO:strands.telemetry.metrics:Creating Strands MetricsClient
I will generate a deterministic summary based on the provided input.
Tool #1: IncidentSummary
INFO:__main__:
┌──────────────────────────────────────────────────┐
│ 📊 ANALYZER SINGLE-REQUEST METRICS               │
├──────────────────────────────────────────────────┤
│ 🔄 Agent Cycles    : 1
│ 📥 Standard Input  : 2 tokens
│ 📤 Output Generated: 223 tokens
│ 💾 Cache Written   : 2604 tokens
│ ⚡ Cache Read      : 0 tokens (Discounted!)
├──────────────────────────────────────────────────┤
│ 📈 Total Processed : 2829 tokens
└──────────────────────────────────────────────────┘

INFO:     127.0.0.1:59250 - "GET /.well-known/agent-card.json HTTP/1.1" 200 OK
INFO:     127.0.0.1:59266 - "POST / HTTP/1.1" 200 OK

Tool #2: IncidentSummary
INFO:__main__:
┌──────────────────────────────────────────────────┐
│ 📊 ANALYZER SINGLE-REQUEST METRICS               │
├──────────────────────────────────────────────────┤
│ 🔄 Agent Cycles    : 1
│ 📥 Standard Input  : 2 tokens
│ 📤 Output Generated: 209 tokens
│ 💾 Cache Written   : 2972 tokens
│ ⚡ Cache Read      : 0 tokens (Discounted!)
├──────────────────────────────────────────────────┤
│ 📈 Total Processed : 3183 tokens
└──────────────────────────────────────────────────┘

