!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
🚨 LOAD.PY IS OFFICIALLY EXECUTING 🚨
If you do not see this in Terminal 1 when starting the server,
your environment is caching an old version of the code!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

2026-07-16 11:50:07,472 [INFO] Found credentials in shared credentials file: ~/.aws/credentials
2026-07-16 11:50:08,573 [INFO] Found credentials in shared credentials file: ~/.aws/credentials
2026-07-16 11:50:08,622 [INFO] Attempting to fetch configuration from Bedrock (ID: arn:aws:bedrock:us-east-1:224198986708:prompt/SZDHHZ2T26, Version: 3) - Try 1
2026-07-16 11:50:09,583 [INFO] Successfully fetched prompt from Bedrock prompt management 'You are an expert incident orchestration agent responsible for coordinating a multi-stage analysis pipeline opearting in the prod environment
You will receive inputs that may include:
- Incident ID
- Incident description
- Logs, alerts, or error messages
- Additional diagnostic context
Your responsibilities:
1. Understand the full incident context, including any identifiers (e.g., incident ID) and descriptions.
2. Treat every request as a candidate for pipeline execution when it relates to incidents, failures, or diagnostics.
3. Always delegate processing to the execution pipeline tool for analysis and summarization. Note that the remote pipeline has maximum execution timeout of 300 seconds
4. Ensure the final output is structured, actionable, and useful for Engineers.
Strict rules:
- DO NOT perform analysis or summarization yourself.
- ALWAYS invoke the pipeline tool for incident-related inputs.
- Preserve all input information, including incident IDs and metadata, when passing to the tool.
- Do NOT ask follow-up questions — forward incomplete inputs as-is.
- Do NOT modify, filter, or reinterpret logs.
Output expectations:
- Return ONLY the pipeline result.
- The output should typically include:
- Incident ID (if provided)
- Key findings
- Root cause (if identified)
- Recommended actions or next steps
- Do not add extra commentary outside the pipeline output.
You act strictly as an orchestrator, not as an analyzer.
🛠️ Creating BedrockModel instance...
2026-07-16 11:50:09,602 [INFO] Found credentials in shared credentials file: ~/.aws/credentials

==================================================
🔍 DIAGNOSTIC: WHAT IS INSIDE THIS MODEL?
==================================================
 🔹 BedrockConfig  --->  <class 'typing._TypedDictMeta'>
 🔹 _abc_impl  --->  <class '_abc._abc_data'>
 🔹 _build_tools_cache_point  --->  <class 'method'>
 🔹 _cache_strategy  --->  <class 'str'>
 🔹 _convert_non_streaming_to_streaming  --->  <class 'method'>
 🔹 _find_detected_and_blocked_policy  --->  <class 'method'>
 🔹 _find_last_user_text_message_index  --->  <class 'method'>
 🔹 _format_bedrock_messages  --->  <class 'method'>
 🔹 _format_request  --->  <class 'method'>
 🔹 _format_request_message_content  --->  <class 'method'>
 🔹 _generate_redaction_events  --->  <class 'method'>
 🔹 _get_additional_request_fields  --->  <class 'method'>
 🔹 _get_default_model_with_warning  --->  <class 'function'>
 🔹 _handle_location  --->  <class 'method'>
 🔹 _has_blocked_guardrail  --->  <class 'method'>
 🔹 _inject_cache_point  --->  <class 'method'>
 🔹 _should_include_tool_result_status  --->  <class 'method'>
 🔹 _stream  --->  <class 'method'>
 🔹 client  --->  <class 'botocore.client.BedrockRuntime'>
 🔹 config  --->  <class 'dict'>
 🔹 context_window_limit  --->  <class 'int'>
 🔹 count_tokens  --->  <class 'method'>
 🔹 get_config  --->  <class 'method'>
 🔹 stateful  --->  <class 'bool'>
 🔹 stream  --->  <class 'method'>
 🔹 structured_output  --->  <class 'method'>
 🔹 update_config  --->  <class 'method'>
==================================================
