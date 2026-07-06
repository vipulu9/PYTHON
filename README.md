INFO:__main__:Attempting to fetch prompt from Bedrock (ID: arn:aws:bedrock:us-east-1:224198986708:prompt/SZDHHZ2T26:1, Version: 1)
ERROR:__main__:Failed to fetch prompt from Bedrock due to error: An error occurred (AccessDeniedException) when calling the GetPrompt operation: User: arn:aws:iam::224198986708:user/cdk is not authorized to perform: bedrock:GetPrompt on resource: arn:aws:bedrock:us-east-1:224198986708:prompt/SZDHHZ2T26:1 because no identity-based policy allows the bedrock:GetPrompt action. Going for fallback prompt.
INFO:botocore.credentials:Found credentials in shared credentials file: ~/.aws/credentials
INFO:__main__:Running Master orchestrator in LOCAL mode via Strands A2AServer on port 9001...
INFO:strands.multiagent.a2a.server:Strands' integration with A2A is experimental. Be aware of frequent breaking changes.
