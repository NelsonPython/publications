# mcp_job_desc_server.py

## Description
This script is designed to process job descriptions using various natural language processing (NLP) tools. It includes tools for keyword clearance search, semantic clearance evaluation, and summarization of job requirements.

## Tools and Features

### keyword_clearance_search
This tool checks if the job description explicitly mentions any clearance terms such as "classified", "top secret", "secret clearance", or "dod". It returns a JSON response indicating whether the job requires a clearance and the reason for the determination.

```python
mcp.tool()
def keyword_clearance_search(job_text: str) -> str:
    clearance_terms = ["classified", "top secret", "secret clearance", "dod"]
    found_terms = [term for term in clearance_terms if term in job_text.lower()]
    
    if found_terms:
        return json.dumps({
            "pursue": False, 
            "method": "keyword", 
            "reason": f"Found explicit clearance keywords: {found_terms}"
        })
    
    return json.dumps({
        "pursue": True, 
        "method": "keyword", 
        "reason": "No explicit clearance keywords found via keyword search"
    })
```

### semantic_clearance_search
This tool uses an LLM from Ollama to evaluate if a security clearance is required based on the job description. It returns a JSON response indicating whether the job requires a clearance and the reason for the determination.

```python
mcp.tool()
def semantic_clearance_search(job_text: str, model: str = "llama3.2:latest") -> str:
    ollama_url = "http://localhost:11434/api/chat"
    
    prompt = f"""
SYSTEM INSTRUCTIONS:
You are a literal, factual parser. You check if a job requires a "Secret Clearance".

RULES:
1. "pursue": false ONLY if the text explicitly states "Secret Clearance" or "Secret Security Clearance".
2. "pursue": true for general requirements like "background check", "remote office", "citizenship", or "public trust". Do NOT assume or extrapolate.
3. In "reason", quote the exact phrase, if found.

EXAMPLES:
Text: "Must hold an active Secret Clearance."
Output: {{"pursue": false, "reason": "Requires active Secret Clearance"}}

Text: "Must have a dedicated home office and pass a background check."
Output: {{"pursue": true, "reason": "No secret clearance required"}}

NOW EVALUATE THIS JOB DESCRIPTION:
{job_text}
"""

    payload = {
        "model": model,
        "messages": [{"role": "user", "content": prompt}],
        "stream": False,
        "format": "json",
        "options": {"temperature": 0.0},
    }

    try:
        data = json.dumps(payload).encode("utf-8")
        req = urllib.request.Request(
            ollama_url, data=data, headers={"Content-Type": "application/json"}
        )
        with urllib.request.urlopen(req, timeout=30) as response:
            result = json.loads(response.read().decode("utf-8"))
            parsed_content = json.loads(result.get("message", {}).get("content", "{}"))
            parsed_content["method"] = "semantic"
            return json.dumps(parsed_content)
            
    except Exception as e:
        return json.dumps({"pursue": True, "method": "semantic", "reason": f"API Error during semantic check: {e}"})
```

### summarize_job_requirements
This tool summarizes key job requirements and skills into streamlined categories. It returns a JSON response containing the categorized requirements.

```python
mcp.tool()
def summarize_job_requirements(job_text: str, model: str = "qwen2.5-coder:1.5b") -> str:
    ollama_url = "http://localhost:11434/api/chat"
    
    target_categories = [
        "must_haves",
        "nice_to_haves",
        "core_responsibilities",
        "tech_stack"
    ]

    formatted_categories = "\n".join([f"- {cat}" for cat in target_categories])
    system_prompt = f"""
Read the job description provided. Identify and summarize key job requirements and skills.

Categorize each summarized requirement STRICTLY under one of the following exact category keys:
{formatted_categories}

Format requirements:
1. Output ONLY valid JSON where the keys are selected from the allowed categories listed above, and the values are lists of summarized requirement strings.
2. Do not invent new category keys outside of this list.
3. Keep summaries clear, concise, and deduplicated.
    """

    payload = {
        "model": model,
        "messages": [
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": job_text},
        ],
        "options": {
            "num_ctx": 2048,
            "num_predict": 2048,
            "num_thread": 4,
        },
        "format": "json",
        "stream": False,
        "keep_alive": 0,
    }

    try:
        data = json.dumps(payload).encode("utf-8")
        req = urllib.request.Request(
            ollama_url, data=data, headers={"Content-Type": "application/json"}
        )
        with urllib.request.urlopen(req, timeout=30) as response:
            res = json.loads(response.read().decode("utf-8"))
            raw_text = res.get("message", {}).get("content", "{}")
            return raw_text.strip()
            
    except Exception as e:
        return json.dumps({"error": f"Failed to summarize job description: {e}"})
```

## Running the Script

To run the script, execute the following command in your terminal:

```sh
python mcp_job_desc_server.py
```

This command will start the FastMCP server, and you can then send prompts to the server using the `mcp.client.send()` function. For example:

```python
response = mcp.client.send("keyword_clearance_search", "This is a job description with a secret clearance.")
print(response)
```

This will call the `keyword_clearance_search` tool with the provided job description and print the response.

## Example Response

```json
{
    "pursue": False,
    "method": "keyword",
    "reason": "Found explicit clearance keywords: ['secret clearance', 'classified']"
}
```

This output indicates that the job description explicitly mentions "secret clearance" and "classified", so the script returns `{"pursue": False, "method": "keyword", "reason": "Found explicit clearance keywords: ['secret clearance', 'classified']"}`.