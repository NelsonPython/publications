<img src="images/agent_code_docs.jpg" width=1000 height=250>

## AI Prompt Engineering for Code to Docs

Software documentation for **[Agentic Job Search](overview_job_search_agentic.md)** is generated automatically via this structured prompt processed by a local Ollama model, `qwen2.5-coder:1.5b`.  Using the Model Context Protocol (MCP) streamlines the workflow.

```
    prompt = (
        f"Read the python script and write step-by-step documentation in markdown format. "
        f"The first line of your output must be the name of the script.\n\n"
        f"Script Name: {script_name}\n\n"
        f"Include code snippets for running prompts and the prompt definition"
        f"```python\n{script_content}\n```"
    )
```

[formulate_cover_summary.py](formulate_cover_summary.md) - writes a summary for the resume header and a cover letter

[formulate_job_desc.py](formulate_job_desc.md) - filters and summarizes a job description

[mcp_job_desc_client.md](mcp_job_desc_client.md) - uses MCP to send a job description to mcp_job_desc_server.py for processing

[mcp_job_desc_server.md](mcp_job_desc_server.md) - filters and summarizes a job description


## Agentic AI Code to Docs Pipeline Overview

When a Python script is checked into Github, the documentation for the script is generated and stored in the repo docs folder as markdown so the commit uploads both the code and the documentation in a single step.  Reviewers have immediate access to the documentation when reviewing the code before approving the pull request.  

**Disclaimer:**  Sample code designed to show an Agentic AI code to docs pipeline.

