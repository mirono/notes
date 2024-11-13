```Python
import ollama  
  
model = "llama3.1"  
prompt = '''What the IDF did on September 28, 2024?'''  
  
response = ollama.chat(model=model,  
                  messages=[{"role":"system", "content":""},  
                            {"role":"user", "content":prompt}])  
print(response)
```

```json
{'model': 'llama3.1', 'created_at': '2024-10-06T10:17:19.129461Z', 'message': {'role': 'assistant', 'content': "I'm unable to tell you what the IDF (Israel Defense Forces) did on September 28, 2024."}, 'done_reason': 'stop', 'done': True, 'total_duration': 13067363667, 'load_duration': 12137086667, 'prompt_eval_count': 23, 'prompt_eval_duration': 165315000, 'eval_count': 25, 'eval_duration': 760911000}
```

```json
{'model': 'llama3.1', 'created_at': '2024-10-06T10:22:21.517198Z', 'message': {'role': 'assistant', 'content': 'I cannot provide real-time information or specifics regarding future events. Is there anything else I can help you with?'}, 'done_reason': 'stop', 'done': True, 'total_duration': 4411430583, 'load_duration': 29342417, 'prompt_eval_count': 23, 'prompt_eval_duration': 3690709000, 'eval_count': 23, 'eval_duration': 689544000}
```
`pip install duckduckgo-search`
`pip install -U duckduckgo_search==5.3.1b1
`

Smoke rises from the rubble of a building that was levelled in overnight Israeli strike on Beirut's southern suburbs, on September 28, 2024. (Photo by ANWAR AMRO / AFP) The Times of Israel is ... His death marks a devastating blow to Hezbollah as it reels from Israeli attacks. It is also a huge blow to Iran. ... [1/17] Beirut's southern suburbs, Lebanon September 28, 2024. Smoke rises from Israeli airstrikes in Beirut's southern suburbs, on Saturday, September 28. Hassan Ammar/AP The Israel Defense Forces is currently striking buildings in Beirut that it alleges are ... Hezbollah has confirmed the death of its leader Hassan Nasrallah, after Israel said he was killed in an airstrike in Beirut, Lebanon on Friday. Retired Colonel Cedric Leighton joins CNN to discuss. A man stands near damaged buildings in the aftermath of Israeli air strikes on Beirut's southern suburbs, Lebanon September 28, 2024. REUTERS/Ali Alloush Purchase Licensing Rights, opens new tab

https://freedium.cfd/https://towardsdatascience.com/genai-with-python-build-agents-from-scratch-complete-tutorial-4fc1e084e2ec

# GenAI with Python: Build Agents from Scratch (Complete Tutorial)

## with Ollama, LangChain, LangGraph (No GPU, No APIKEY)

My Posts:
https://blogsh.ophir.org.il/?p=705
https://www.linkedin.com/pulse/%D7%94%D7%90%D7%9D-%D7%94-llm-%D7%A9%D7%9C%D7%99-%D7%99%D7%95%D7%93%D7%A2-%D7%A9%D7%A0%D7%90%D7%A1%D7%A8%D7%90%D7%9C%D7%9C%D7%94-%D7%97%D7%95%D7%A1%D7%9C-miron-ophir-a97of/
