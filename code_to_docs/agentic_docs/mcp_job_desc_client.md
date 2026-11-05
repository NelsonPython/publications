## mcp_job_desc_client.py

### Step-by-Step Documentation

#### 1. Import Required Libraries

```python
import asyncio
import json
import sys
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client
```

#### 2. Define the `run_pipeline` Async Function

```python
async def run_pipeline():
```

#### 3. Configure MCP Server Subprocess

[mcp_job_desc_server.py](mcp_job_desc_server.md) runs as a subprocess.

```python
    # 1. Configure the MCP server subprocess
    server_params = StdioServerParameters(
        command=sys.executable,
        args=["mcp_job_desc_server.py"],
    )
```

#### 4. Establish a Connection to the MCP Server

```python
    async with stdio_client(server_params) as (read, write):
```

#### 5. Initialize the MCP Session

```python
        async with ClientSession(read, write) as session:
```

#### 6. Step 1: Explicitly Call Keyword Clearance Search

```python
            print("-> Running keyword clearance search...")
            kw_result = await session.call_tool("keyword_clearance_search", {"job_text": job_text})
            kw_data = json.loads(kw_result.content[0].text)
```

#### 7. Step 2: Explicitly Call Semantic Clearance Search

```python
            print("-> Running semantic clearance search...")
            sem_result = await session.call_tool("semantic_clearance_search", {"job_text": job_text})
            sem_data = json.loads(sem_result.content[0].text)
```

#### 8. Step 3: Explicitly Call Summarization Tool

```python
            print("-> Running requirement summarization...")
            summary_result = await session.call_tool("summarize_job_requirements", {"job_text": job_text})
            summary_output = summary_result.content[0].text
```

#### 9. Report with the Requested SUMMARY: Prefix

```python
            # Report with the requested SUMMARY: prefix
            print(f"\nSUMMARY:\n{summary_output}")

            # Optional: Save to a .job file
            outpath = "job_desc/sample.job"
            with open(outpath, "w", encoding="utf-8") as fo:
                fo.write(summary_output)
            print(f"\n[SAVED]: Summary written to {outpath}")
```

#### 10. End the Main Block

```python
if __name__ == "__main__":
    asyncio.run(run_pipeline())
```

### Example - No Secret Clearance Required

job_text = "We are looking for a Senior AI Engineer to develop an AI agent for summarizing Slack channel threads.  Must have Python.  MCP and RAG, a plus."

```

 ╭──────────────────────────────────────────────────────────────────────────────╮ 
 │                                                                              │ 
 │                                                                              │ 
 │                         ▄▀▀ ▄▀█ █▀▀ ▀█▀ █▀▄▀█ █▀▀ █▀█                        │ 
 │                         █▀  █▀█ ▄▄█  █  █ ▀ █ █▄▄ █▀▀                        │ 
 │                                                                              │ 
 │                                                                              │ 
 │                                                                              │ 
 │                                FastMCP 4.1.6                                 │ 
 │                            https://gofastmcp.com                             │ 
 │                                                                              │ 
 │                 🖥  Server:      Job Processing Server, 4.1.6                 │ 
 │                 🚀 Deploy free: https://horizon.prefect.io                   │ 
 │                                                                              │ 
 ╰──────────────────────────────────────────────────────────────────────────────╯ 


[11/05/26 04:03:03] INFO     Starting MCP server 'Job Processing  transport.py:241
                             Server' with transport 'stdio'                       
Processing job description...

-> Running keyword clearance search...
-> Running semantic clearance search...
[APPROVED]: Clearance check passed.

-> Running requirement summarization...

SUMMARY:
{
  "core_responsibilities": [
    "Develop an AI agent for summarizing Slack channel threads",
    "Contribute to the development and optimization of AI models",
    "Utilize Python and other relevant technologies"
  ],
  "must_haves": [
    "Python proficiency",
    "Knowledge of MCP and RAG (Machine-Centric Querying and Retrieval)",
    "Experience with AI development frameworks and libraries"
  ],
  "nice_to_haves": [
    "Knowledge of machine learning and natural language processing",
    "Experience with AI ethical guidelines and considerations",
    "Ability to work with distributed and scalable systems"
  ]
}

[SAVED]: Summary written to job_desc/sample.job
```
### Example - Secret Clearance Required

job_text = "We are looking for a Senior AI Engineer for a confidential government project.  Must have Python."

```

 ╭──────────────────────────────────────────────────────────────────────────────╮ 
 │                                                                              │ 
 │                                                                              │ 
 │                         ▄▀▀ ▄▀█ █▀▀ ▀█▀ █▀▄▀█ █▀▀ █▀█                        │ 
 │                         █▀  █▀█ ▄▄█  █  █ ▀ █ █▄▄ █▀▀                        │ 
 │                                                                              │ 
 │                                                                              │ 
 │                                                                              │ 
 │                                FastMCP 4.1.6                                 │ 
 │                            https://gofastmcp.com                             │ 
 │                                                                              │ 
 │                 🖥  Server:      Job Processing Server, 4.1.6                 │ 
 │                 🚀 Deploy free: https://horizon.prefect.io                   │ 
 │                                                                              │ 
 ╰──────────────────────────────────────────────────────────────────────────────╯ 


[11/05/26 04:01:15] INFO     Starting MCP server 'Job Processing  transport.py:241
                             Server' with transport 'stdio'                       
Processing job description...

-> Running keyword clearance search...
-> Running semantic clearance search...
[REJECTED via Semantic]: Requires active Secret Clearance

```





