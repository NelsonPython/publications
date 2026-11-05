<img src="images/agent_code_docs.jpg" width=1000 height=250>

# AI Prompt Engineering for Code to Docs

Software documentation for the **[Agentic Job Search](overview_job_search_agentic.md)** app is generated automatically using a Python script that sends a structured prompt to the local Ollama model `qwen2.5-coder:1.5b`. The target `script_name` is supplied dynamically at runtime.

```
    prompt = (
        f"Read the python script and write step-by-step documentation in markdown format. "
        f"The first line of your output must be the name of the script.\n\n"
        f"Script Name: {script_name}\n\n"
        f"Include code snippets for running prompts and the prompt definition"
        f"```python\n{script_content}\n```"
    )
```

## Software User Guides

[formulate_job_desc.py](formulate_job_desc.md)

[formulate_cover_summary.py](formulate_cover_summary.md)

[mcp_job_desc_client.md](mcp_job_desc_client.md)

[mcp_job_desc_server.md](mcp_job_desc_server.md)



