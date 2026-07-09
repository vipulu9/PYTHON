https://bedrock-agent.us-east-1.amazonaws.com/prompts/

{
  "name": "IncidentOrchestrator-Bruno",
  "description": "Testing prompt creation via REST API",
  "defaultVariant": "PrimaryVariant",
  "variants": [
    {
      "name": "PrimaryVariant",
      "templateType": "TEXT",
      "templateConfiguration": {
        "text": {
          "text": "You are an expert incident orchestration agent responsible for coordinating a multi-stage analysis pipeline. Route the request accordingly."
        }
      },
      "modelId": "anthropic.claude-3-sonnet-20240229-v1:0",
      "inferenceConfiguration": {
        "text": {
          "temperature": 0,
          "topP": 1,
          "maxTokens": 2000
        }
      }
    }
  ]
}

