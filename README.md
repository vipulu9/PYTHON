from typing import Any
from strands import Agent, tool
import asyncio
from strands.agent.conversation_manager.null_conversation_manager import NullConversationManager
from bedrock_agentcore.runtime import BedrockAgentCoreApp
from model.load import load_model
from mcp_client.client import get_streamable_http_mcp_client
from datetime import datetime, date
from strands.models.bedrock import BedrockModel

app = BedrockAgentCoreApp()
log = app.logger

# Define a Streamable HTTP MCP Client
mcp_clients = [get_streamable_http_mcp_client()]

DEFAULT_SYSTEM_PROMPT = """
You are a helpful assistant. Use tools when appropriate.
"""

# --- YOUR INTEGRATED TOOLS ---
@tool
def get_time() -> str:
    """Returns the current local time."""
    return datetime.now().strftime('%H:%M:%S')

@tool
def get_date() -> str:
    """Returns the current local date."""
    return str(date.today())

@tool
def calculate(expression: str) -> str:
    """
    Evaluates a mathematical expression provided as a string.
    Example: calculate("5 * 10 + 2")
    """
    try:
        return str(eval(expression))
    except Exception:
        return "Invalid calculation"

# Initialize tools list with your custom tools
tools = [get_time, get_date, calculate]

_INLINE_FUNCTION_NAMES = set()

# Add MCP client to tools if available
for mcp_client in mcp_clients:
    if mcp_client:
        tools.append(mcp_client)

def _make_conversation_manager():
    return NullConversationManager()

_agent = None

def get_or_create_agent():
    global _agent
    if _agent is None:
        _agent = Agent(
            model=BedrockModel(model_id="us.anthropic.claude-3-sonnet-20240229-v1:0"),
            system_prompt=DEFAULT_SYSTEM_PROMPT,
            tools=tools,
            conversation_manager=_make_conversation_manager(),
            hooks=[],
        )
    return _agent

# --- HARNESS UTILS ---
def _extract_prompt(payload: dict):
    if "messages" in payload:
        return payload["messages"]
    if "tool_results" in payload:
        return [{"role": "user", "content": [{"toolResult": {
            "toolUseId": tr["toolUseId"],
            "status": tr.get("status", "success"),
            "content": tr.get("content", []),
        }} for tr in payload["tool_results"]]}]
    return payload.get("prompt", "")

@app.entrypoint
async def invoke(payload, context):
    log.info("Invoking Agent.....")
    agent = get_or_create_agent()
    prompt = _extract_prompt(payload)

    async for event in agent.stream_async(prompt):
        if not isinstance(event, dict) or "event" not in event:
            continue
        cbs = event["event"].get("contentBlockStart")
        if cbs is not None and not cbs.get("start"):
            continue
        yield event

if __name__ == "__main__":
    app.run()
