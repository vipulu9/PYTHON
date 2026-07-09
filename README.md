{
  "name": "IncidentOrchestrator-BrunoTest",
  "description": "Testing prompt creation via REST API",
  "defaultVariant": "PrimaryVariant",
  "variants": [
    {
      "name": "PrimaryVariant",
      "templateType": "TEXT",
      "templateConfiguration": {
        "text": {
          "text": "You are an expert incident orchestration agent responsible for coordinating a multi-stage analysis pipeline.\n(Insert your system prompt text here, including {{environment}} variables)"
        }
      },
      "modelId": "anthropic.claude-3-sonnet-20240229-v1:0",
      "inferenceConfiguration": {
        "text": {
          "temperature": 0.0,
          "topP": 1.0,
          "maxTokens": 2000,
          "stopSequences": []
        }
      }
    }
  ]
}


curl -X POST "https://bedrock-agent.us-east-1.amazonaws.com/prompts/" \
  --aws-sigv4 "aws:amz:us-east-1:bedrock" \
  --user "$AWS_ACCESS_KEY_ID:$AWS_SECRET_ACCESS_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "name": "IncidentOrchestrator-CurlTest",
        "defaultVariant": "PrimaryVariant",
        "variants": [{
          "name": "PrimaryVariant",
          "templateType": "TEXT",
          "templateConfiguration": {
            "text": { "text": "Testing via cURL" }
          },
          "modelId": "anthropic.claude-3-sonnet-20240229-v1:0"
        }]
      }'
      
