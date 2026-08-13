CREATE EXTENSION IF NOT EXISTS pgcrypto;
CREATE TABLE agent_llm_config (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    agent_id VARCHAR(255) NOT NULL,
    agent_name VARCHAR(255) NOT NULL,

    model_id VARCHAR(255) NOT NULL,
    provider_name VARCHAR(128) NOT NULL,
    model_name VARCHAR(255) NOT NULL,

    temperature NUMERIC(4,3) NOT NULL
        CHECK (temperature >= 0 AND temperature <= 2),

    top_k INTEGER NOT NULL
        CHECK (top_k >= 0 AND top_k <= 500),

    max_tokens INTEGER NOT NULL
        CHECK (max_tokens >= 1 AND max_tokens <= 200000),

    prompt_caching_option VARCHAR(32) NOT NULL DEFAULT 'none'
        CHECK (
            prompt_caching_option IN (
                'none',
                'system',
                'system_and_tools'
            )
        ),

    prompt_cache_ttl VARCHAR(8)
        CHECK (
            prompt_cache_ttl IS NULL
            OR prompt_cache_ttl IN ('5m', '1h')
        ),

    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),

    CHECK (
        prompt_caching_option <> 'none'
        OR prompt_cache_ttl IS NULL
    )
);
CREATE TABLE agent_prompt_config (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),

    agent_id VARCHAR(255) NOT NULL,
    agent_name VARCHAR(255) NOT NULL,

    prompt_id VARCHAR(255) NOT NULL,
    prompt_name VARCHAR(255) NOT NULL,
    prompt_version VARCHAR(64) NOT NULL,

    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_agent_llm_config_agent_id
ON agent_llm_config(agent_id);

CREATE INDEX idx_agent_prompt_config_agent_id
ON agent_prompt_config(agent_id);

INSERT INTO agent_llm_config (
    agent_id,
    agent_name,
    model_id,
    provider_name,
    model_name,
    temperature,
    top_k,
    max_tokens,
    prompt_caching_option,
    prompt_cache_ttl
)
VALUES (
    'agent-001',
    'SummarizerAgent',
    'global.anthropic.claude-sonnet-4-5-20250929-v1:0',
    'Anthropic',
    'Claude 3.5 Sonnet',
    0.7,
    100,
    4096,
    'system',
    '1h'
);

INSERT INTO agent_prompt_config (
    agent_id,
    agent_name,
    prompt_id,
    prompt_name,
    prompt_version
)
VALUES (
    'agent-001',
    'SummarizerAgent',
    'FK0MKCLEEV',
    'summarizer_agent_system_prompt',
    '3'
);

output

                  id                  |   agent_id   |     agent_name      | prompt_id  |          prompt_name           | prompt_version |          created_at           |          updated_at           
--------------------------------------+--------------+---------------------+------------+--------------------------------+----------------+-------------------------------+-------------------------------
 c4f0ed25-04a8-4168-a4fa-08319bc46fb4 | agent-001    | SummarizerAgent     | FK0MKCLEEV | summarizer_agent_system_prompt | 3              | 2026-08-12 06:46:14.200779+00 | 2026-08-12 06:46:14.200779+00
 ea154357-0428-40b2-b1ba-5fc8dd496438 | 7ZuK4LvvoFJM | AnalyzerAgentRecord | 4YMRN8HBVE | analyzer_agent_system_prompt   | 2              | 2026-08-12 09:45:24.68221+00  | 2026-08-12 09:45:24.68221+00
