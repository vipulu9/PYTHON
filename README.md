def converse(self, **kwargs):
        # 1. Inject Cache Point into the System Instructions
        if "system" in kwargs and isinstance(kwargs["system"], list):
            if kwargs["system"]:
                kwargs["system"].append({"cachePoint": {"type": "default"}})
                
        # 2. Inject Cache Point into the Tools 
        if "toolConfig" in kwargs and "tools" in kwargs["toolConfig"]:
            if kwargs["toolConfig"]["tools"]:
                kwargs["toolConfig"]["tools"].append({"cachePoint": {"type": "default"}})
                
        # 3. Execute the actual API call
        response = self._real_client.converse(**kwargs)
        
        # 4. === NEW: INTERCEPT & PRINT METRICS ===
        try:
            usage = response.get('usage', {})
            print("\n" + "="*45)
            print("💰 BEDROCK TOKEN USAGE REPORT")
            print("="*45)
            # Standard tokens
            print(f"Total Input Tokens:  {usage.get('inputTokens', 0)}")
            print(f"Total Output Tokens: {usage.get('outputTokens', 0)}")
            # Cache-specific tokens (supported in recent boto3 versions)
            print(f"Tokens Cached (Write): {usage.get('cacheCreationInputTokenCount', 0)}  <- You pay standard price for these")
            print(f"Tokens Read (Cache):   {usage.get('cacheReadInputTokenCount', 0)}  <- You get a massive discount here!")
            print("="*45 + "\n")
        except Exception as e:
            pass # Failsafe so we don't break your app if logging fails
            
        return response
