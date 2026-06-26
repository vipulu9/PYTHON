from strands import Agent, tool

@tool
def get_time():
    from datetime import datetime
    return datetime.now().strftime('%H:%M:%S')

@tool
def get_date():
    from datetime import date
    return str(date.today())

@tool
def calculate(expression: str):
    try:
        return str(eval(expression))
    except:
        return "Invalid calculation"

agent = Agent(
    tools=[get_time, get_date, calculate],
    system_prompt="You are a helpful assistant"
)

while True:
    user_input = input("You: ")
    if user_input.lower() in ["exit", "bye"]:
        break

    response = agent(user_input)
    # print("Agent:", response)
    print()
