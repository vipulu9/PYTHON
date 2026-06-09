# PYTHON
import os
import json
import requests
from datetime import datetime, date
from dotenv import load_dotenv

# Load the variables from the .env file
load_dotenv()

# We no longer initialize an OpenAI client. 
# Instead, we define the API URL and our API Key.
API_URL = "https://openrouter.ai/api/v1/chat/completions"
API_KEY = os.environ.get("OPENROUTER_API_KEY")

# ---------------------------------------------------------
# 1. Tools
# ---------------------------------------------------------

def get_time() -> str:
    """Returns the current local time."""
    return f"Current time is {datetime.now().strftime('%H:%M:%S')}"

def get_date() -> str:
    """Returns today's current date."""
    return f"Today's date is {date.today()}"

def calculate(expression: str) -> str:
    """Calculates a mathematical expression and returns the result."""
    try:
        # Note: eval() is unsafe for production apps. Use safely in sandboxed environments.
        result = eval(expression)
        return f"Result: {result}"
    except Exception:
        return "Invalid calculation!"

# Map the functions in a dictionary for easy execution later
available_tools = {
    'get_time': get_time,
    'get_date': get_date,
    'calculate': calculate,
}

# OpenRouter requires explicit JSON schemas for tools
tool_schemas = [
    {
        "type": "function",
        "function": {
            "name": "get_time",
            "description": "Returns the current local time."
        }
    },
    {
        "type": "function",
        "function": {
            "name": "get_date",
            "description": "Returns today's current date."
        }
    },
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "Calculates a mathematical expression and returns the result.",
            "parameters": {
                "type": "object",
                "properties": {
                    "expression": {
                        "type": "string",
                        "description": "The mathematical expression to evaluate (e.g., '2 + 2', '10 * 5')."
                    }
                },
                "required": ["expression"]
            }
        }
    }
]

# ---------------------------------------------------------
# 2. Agent Brain
# ---------------------------------------------------------

def agent(user_input: str, chat_history: list) -> str:
    """Sends the user input to OpenRouter via direct HTTP request and handles tool calls."""
    
    # Append the user's message to the conversation history
    chat_history.append({'role': 'user', 'content': user_input})

    # Prepare the headers and payload for the HTTP POST request
    headers = {
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json"
    }
    
    payload = {
        "model": "openrouter/free",
        "messages": chat_history,
        "tools": tool_schemas
    }

    # First API Call
    response = requests.post(API_URL, headers=headers, json=payload)
    response.raise_for_status() # Throw an error if the request fails
    
    # Parse the JSON response into a Python dictionary
    response_data = response.json()
    message = response_data['choices'][0]['message']

    # Check if the LLM decided it needs to use a tool
    if 'tool_calls' in message and message['tool_calls']:
        # Append the LLM's tool request dictionary to the history
        chat_history.append(message)
        
        # Iterate over all the tools the LLM asked to run
        for tool_call in message['tool_calls']:
            tool_name = tool_call['function']['name']
            
            # Arguments come back as a JSON string, so we must parse it
            try:
                tool_args = json.loads(tool_call['function']['arguments'])
            except json.JSONDecodeError:
                tool_args = {}

            # Execute the matching Python function
            if tool_name in available_tools:
                tool_function = available_tools[tool_name]
                tool_output = tool_function(**tool_args)
                
                # Append the result of the tool back to the chat history
                chat_history.append({
                    'role': 'tool',
                    'tool_call_id': tool_call['id'],
                    'name': tool_name,
                    'content': str(tool_output)
                })
            else:
                chat_history.append({
                    'role': 'tool',
                    'tool_call_id': tool_call['id'],
                    'name': tool_name,
                    'content': "Error: Tool not found."
                })

        # Update payload with the new chat history (containing tool results)
        payload["messages"] = chat_history

        # Second API Call
        final_response = requests.post(API_URL, headers=headers, json=payload)
        final_response.raise_for_status()
        
        final_data = final_response.json()
        final_message = final_data['choices'][0]['message']
        
        chat_history.append(final_message)
        return final_message.get('content', '')

    # If no tools were called, append and return the standard response
    chat_history.append(message)
    return message.get('content', '')

# ---------------------------------------------------------
# 3. Run Loop
# ---------------------------------------------------------

def run_agent():
    print("🤖 Raw HTTP Agent Started (type 'exit' or 'bye' to quit)\n")
    
    # System prompt to give the agent its persona
    messages = [
        {
            'role': 'system', 
            'content': 'You are a helpful, conversational AI assistant. Use the provided tools when the user asks for the time, date, or to calculate math. Otherwise, just chat normally.'
        }
    ]

    while True:
        user_input = input("You: ")
        
        if user_input.lower() in ['exit', 'bye', 'goodbye']:
            print("Agent: Goodbye!")
            break
            
        response = agent(user_input, messages)
        print("Agent:", response)

# ---------------------------------------------------------
# 4. Entry Point
# ---------------------------------------------------------

if __name__ == "__main__":
    if not os.environ.get("OPENROUTER_API_KEY"):
        print("⚠️ Warning: OPENROUTER_API_KEY environment variable is not set!")
    else:
        run_agent()
