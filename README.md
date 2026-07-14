{ 
  name="prompt_name1",
  description="Automated prompt creation",
  defaultVariant="PrimaryVariant",
  variants={
    "name": "PrimaryVariant",
    "templateType": "TEXT",
    "templateConfiguration": {
      "text": {
        "text": "you are helpful agent"
      }
    },
    "modelId": "global.anthropic.claude-sonnet-4-5-20250929-v1:0",
    "inferenceConfiguration": {
      "text": {
        "temperature": 0.0,
                "topP": 1.0,
        "maxTokens": 2000,
        "stopSequences": []
      }
    }
  }
}
