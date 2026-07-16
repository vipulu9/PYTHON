import sys
from strands.models.bedrock import BedrockModel

# 1. Loud sanity check to prove this file is actually running in Terminal 1
print("\n" + "!"*50)
print("🚨 LOAD.PY IS OFFICIALLY EXECUTING 🚨")
print("If you do not see this in Terminal 1 when starting the server,")
print("your environment is caching an old version of the code!")
print("!"*50 + "\n")

def load_model() -> BedrockModel:
    print("🛠️ Creating BedrockModel instance...")
    
    # Initialize the model as normal
    model = BedrockModel(model_id="global.anthropic.claude-sonnet-4-5-20250929-v1:0")
    
    # 2. The X-Ray Scanner: Print every hidden property inside the model
    print("\n" + "="*50)
    print("🔍 DIAGNOSTIC: WHAT IS INSIDE THIS MODEL?")
    print("="*50)
    
    try:
        # Loop through all properties of the model
        for attribute_name in dir(model):
            # Skip built-in python junk like __class__
            if not attribute_name.startswith('__'): 
                attribute_value = getattr(model, attribute_name)
                print(f" 🔹 {attribute_name}  --->  {type(attribute_value)}")
    except Exception as e:
        print(f"Could not scan model: {e}")
        
    print("="*50 + "\n")
    
    return model
