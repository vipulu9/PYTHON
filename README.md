import boto3
import logging
import json

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)

def deploy_or_update_prompt():
    """
    Checks if a specific prompt exists in AWS Bedrock.
    Updates it if found, creates it if not, and publishes a locked version.
    """
    client = boto3.client('bedrock-agent', region_name='us-east-1')

    prompt_name = "IncidentOrchestrator-Automated"
    system_text = """You are an expert incident orchestration agent responsible for coordinating a multi-stage analysis pipeline in {{environment}} environment"""

    # Define the core variant payload (reusable for both Create and Update)
    prompt_variant = {
        "name": "PrimaryVariant",
        "templateType": "TEXT",
        "templateConfiguration": {
            "text": {
                "text": system_text
            }
        },
        "modelId": "anthropic.claude-3-sonnet-20240229-v1:0",
        "inferenceConfiguration": {
            "text": {
                "temperature": 0.0,  # Locked down for strict routing
                "topP": 1.0,
                "maxTokens": 2000,
                "stopSequences": []
            }
        }
    }

    try:
        # ==========================================
        # STEP 1: Search for existing prompt by name
        # ==========================================
        logger.info(f"Checking if prompt '{prompt_name}' already exists in AWS...")
        existing_prompt_id = None

        # We use a paginator in case you have hundreds of prompts in your account
        paginator = client.get_paginator('list_prompts')
        for page in paginator.paginate():
            for summary in page.get('promptSummaries', []):
                if summary['name'] == prompt_name:
                    existing_prompt_id = summary['id']
                    break
            if existing_prompt_id:
                break

        # ==========================================
        # STEP 2: Execute Update or Create
        # ==========================================
        if existing_prompt_id:
            logger.info(f"Match found! Updating existing prompt (ID: {existing_prompt_id})...")
            # Update the existing DRAFT
            client.update_prompt(
                promptIdentifier=existing_prompt_id,
                name=prompt_name,
                description="Automated prompt update",
                defaultVariant="PrimaryVariant",
                variants=[prompt_variant]
            )
            final_prompt_id = existing_prompt_id
            logger.info("Successfully updated the DRAFT version.")
        else:
            logger.info(f"No match found. Creating new prompt '{prompt_name}'...")

            # Create a brand new DRAFT
            create_response = client.create_prompt(
                name=prompt_name,
                description="Automated prompt creation",
                defaultVariant="PrimaryVariant",
                variants=[prompt_variant]
            )

            logger.info("Create Prompt Response:")
            logger.info(json.dumps(create_response, indent=2, default=str))

            # CHANGED: Access ID directly from response
            final_prompt_id = create_response['id']

            logger.info(f"Successfully created a new prompt. ID: {final_prompt_id}")

        # ==========================================
        # STEP 3: Lock and Publish a New Version
        # ==========================================
        logger.info("Publishing a new locked version for production...")

        version_response = client.create_prompt_version(
            promptIdentifier=final_prompt_id,
            description="Automated deployment publish"
        )

        logger.info("Create Prompt Version Response:")
        logger.info(json.dumps(version_response, indent=2, default=str))

        locked_version = version_response['version']

        logger.info("DEPLOYMENT PIPELINE SUCCESSFUL")
        logger.info(f"PROMPT ID: {final_prompt_id}")
        logger.info(f"VERSION:   {locked_version}")

        return final_prompt_id, locked_version

    except Exception:
        logger.exception("Deployment failed")
        return None, None

if __name__ == "__main__":
    deploy_or_update_prompt()
