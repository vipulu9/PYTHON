# Example: Updating an existing prompt's draft
client.update_prompt(
    promptIdentifier="YOUR_PROMPT_ID",
    name="IncidentOrchestrator",
    defaultVariant="PrimaryVariant",
    variants=[
        {
            "name": "PrimaryVariant",
            "templateType": "TEXT",
            "templateConfiguration": {
                "text": {
                    "text": "These are my updated instructions for the orchestrator..."
                }
            },
            "modelId": "anthropic.claude-3-5-sonnet-20240620-v1:0"
        }
    ]
)


import boto3
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def deploy_orchestrator_prompt():
    """
    Creates a new Prompt in AWS Bedrock via API and immediately publishes 
    a locked version for production use.
    """
    # Use the bedrock-agent client for management tasks
    client = boto3.client('bedrock-agent', region_name='us-east-1')

    prompt_name = "IncidentOrchestrator-Automated"
    
    # The actual system instructions
    system_text = """You are an expert incident orchestration agent...
(Insert your full system prompt text here, including {{environment}} variables)
"""

    try:
        logger.info(f"Creating new Bedrock prompt: {prompt_name}...")

        # Step 1: Create the prompt (This creates the "DRAFT" version)
        create_response = client.create_prompt(
            name=prompt_name,
            description="Automated prompt deployment for Incident Orchestration pipeline",
            defaultVariant="PrimaryVariant",
            variants=[
                {
                    "name": "PrimaryVariant",
                    "templateType": "TEXT",
                    "templateConfiguration": {
                        "text": {
                            "text": system_text
                        }
                    },
                    "modelId": "anthropic.claude-3-sonnet-20240229-v1:0", # Change to 3.5 if available in your region
                    "inferenceConfiguration": {
                        "text": {
                            "temperature": 0.0,  # Locked down for strict routing
                            "topP": 1.0,
                            "maxTokens": 2000,
                            "stopSequences": []
                        }
                    }
                }
            ]
        )

        prompt_id = create_response['prompt']['id']
        logger.info(f"Successfully created DRAFT prompt. ID: {prompt_id}")

        # Step 2: Publish a locked version (e.g., Version 1)
        # You cannot use a DRAFT safely in production; you must version it.
        logger.info("Publishing locked version for production...")
        version_response = client.create_prompt_version(
            promptIdentifier=prompt_id,
            description="Initial automated production deployment"
        )
        
        locked_version = version_response['promptVersion']
        
        logger.info("=========================================")
        logger.info("DEPLOYMENT SUCCESSFUL")
        logger.info(f"PROMPT ID: {prompt_id}")
        logger.info(f"VERSION:   {locked_version}")
        logger.info("Update these values in your AWS Systems Manager (SSM) Parameter Store.")
        logger.info("=========================================")

        return prompt_id, locked_version

    except Exception as e:
        logger.error(f"Failed to deploy prompt: {e}")
        return None, None

if __name__ == "__main__":
    deploy_orchestrator_prompt()
