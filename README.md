import os
from dotenv import load_dotenv
from strands.models.bedrock import BedrockModel, CacheToolsConfig
import logging
import psycopg2
from psycopg2.extras import DictCursor
from dotenv import load_dotenv

load_dotenv()
logger = logging.getLogger(__name__)

# Database configuration with your local setup as fallbacks
DB_HOST = os.getenv("DB_HOST", "localhost")
DB_PORT = os.getenv("DB_PORT", "5432")
DB_NAME = os.getenv("DB_NAME", "model_config_db")
DB_USER = os.getenv("DB_USER", "postgres")
DB_PASSWORD = os.getenv("DB_PASSWORD", "Db@123")

AWS_GUARDRAIL_IDENTIFIER = os.getenv("AWS_GUARDRAIL_IDENTIFIER", "")

def fetch_agent_config(agent_name: str = "agent1") -> dict:
    """
    Shared function to fetch agent configurations (model parameters and cache flags) 
    from the local PostgreSQL database.
    """
    conn = None
    try:
        # Establish the database connection
        conn = psycopg2.connect(
            host=DB_HOST,
            port=DB_PORT,
            database=DB_NAME,
            user=DB_USER,
            password=DB_PASSWORD
        )
        
        # DictCursor ensures the row is returned as a dictionary (e.g., {'temperature': 0.2})
        cursor = conn.cursor(cursor_factory=DictCursor)
        
        # Fetch the specific agent's configuration
        query = "SELECT * FROM agent_config WHERE agent_name = %s;"
        cursor.execute(query, (agent_name,))
        result = cursor.fetchone()
        
        cursor.close()
        
        if result:
            db_config = dict(result)
            
            if 'enable_tool_cache' not in db_config:
                db_config['enable_tool_cache'] = False
                
            logger.info(f"Successfully fetched DB config for {agent_name}: {db_config}")
            return db_config
            
        else:
            logger.warning(f"No config found in DB for agent: {agent_name}. Falling back to defaults.")

    except psycopg2.Error as e:
        logger.error(f"Database error while fetching config for {agent_name}: {e}. Falling back to defaults.")
        
    finally:
        # Ensure connection is closed even if an error occurs
        if conn is not None:
            conn.close()

    # Fallback configuration if DB fails or row is missing
    return {
        "model_id": "global.anthropic.claude-sonnet-4-5-20250929-v1:0",
        "temperature": 0.20,
        "additional_request_fields" : {
                    "top_k": 50
                },
        "enable_system_cache": True,
        "enable_tool_cache": False
    }

def load_model(agent_name: str = "agent1", db_config: dict = None) -> BedrockModel:
    """
    Instantiates the BedrockModel using DB values. 
    If db_config is not provided, it fetches it using the shared base library function.
    """
    if db_config is None:
        db_config = fetch_agent_config(agent_name)

    model_kwargs = {
        "model_id": db_config.get("model_id", "global.anthropic.claude-sonnet-4-5-20250929-v1:0"),
        "temperature": db_config.get("temperature", 0.20),
        "additional_request_fields" : {
            "top_k": db_config.get("top_k", 50)
        },
        "guardrail_id": AWS_GUARDRAIL_IDENTIFIER,
        "guardrail_version": "1",
        "guardrail_trace": "enabled"
    }
    
    # Enable tool caching if specified in DB config
    TTL = db_config.get("ttl", "5m")

    if db_config.get("enable_tool_cache"):
        model_kwargs["cache_tools"] = CacheToolsConfig(
            type="default",
            ttl=TTL
        )
        print("Tool caching dynamically enabled from config.")
        
    return BedrockModel(**model_kwargs)
