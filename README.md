import boto3
from strands.models.bedrock import BedrockModel

class CacheInterceptingClient:
    """
    A wrapper class that intercepts the Boto3 Bedrock Runtime client.
    It silently injects Bedrock Prompt Caching markers into the final payload 
    after the Strands library has finished formatting it.
    """
    def __init__(self):
        # Initialize the real Bedrock Runtime client using default session/credentials
        self._real_client = boto3.client("bedrock-runtime")
        
    def converse(self, **kwargs):
        # 1. Inject Cache Point into the System Instructions
        if "system" in kwargs and isinstance(kwargs["system"], list):
            if kwargs["system"]:  # Ensure the system array is not empty
                kwargs["system"].append({"cachePoint": {"type": "default"}})
                
        # 2. Inject Cache Point into the Tools 
        # (Caching your execute_incident_analysis_pipeline tool saves massive tokens)
        if "toolConfig" in kwargs and "tools" in kwargs["toolConfig"]:
            if kwargs["toolConfig"]["tools"]:
                kwargs["toolConfig"]["tools"].append({"cachePoint": {"type": "default"}})
                
        # Execute the actual API call with the dynamically injected cache points
        return self._real_client.converse(**kwargs)
        
    def __getattr__(self, name):
        # Forward all other standard boto3 methods to the real client seamlessly
        return getattr(self._real_client, name)


def load_model() -> BedrockModel:
    """Get Bedrock model client using IAM credentials and inject the caching interceptor."""
    caching_client = CacheInterceptingClient()
    
    # Initialize your Strands model exactly as you had it
    model = BedrockModel(model_id="global.anthropic.claude-sonnet-4-5-20250929-v1:0")
    
    # Forcefully override the library's internal boto3 client with our interceptor.
    # We check the most common Boto3 client attribute names used by abstractions.
    if hasattr(model, 'client'):
        model.client = caching_client
    elif hasattr(model, '_client'):
        model._client = caching_client
    elif hasattr(model, 'bedrock_client'):
        model.bedrock_client = caching_client
        
    return model
