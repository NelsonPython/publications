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

[mcp_job_desc_server.py](mcp_job_desc_server.py) runs as a subprocess.

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

### Summary

The `mcp_job_desc_client.py` script is designed to process a job description using an MCP server. It explicitly calls keyword and semantic clearance searches to filter out non-applicable job descriptions. After passing the clearance checks, it uses a summarization tool to generate a concise summary of the job requirements. The summary is then optionally saved to a file. The script handles the MCP server subprocess and ensures that the MCP session is properly initialized and closed.
