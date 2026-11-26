<img src="images/agent_code_docs.jpg" width=1000 height=250>

## AI Prompt Engineering for Code-to-Docs

Models such as the Ollama model `qwen2.5-coder:1.5b` have been designed to generate code.  They can also write software user guides.  One may argue that software user guides are obsolete now that interactive AI chats that can guide developers with real-time feedback and even write code.  While this is true, in regulated environments where code can impact human safety, a software user guide may still be needed for audit and certification.  Even in non-regulated environments, software user guides may still be valuable for conveying best practices and coding standards.  This example demonstrates AI Prompt Engineering as part of the code-to-docs pipeline for the **[Agentic Job Search](overview_job_search_agentic.md)**.  Docs are processed locally by `qwen2.5-coder:1.5b` with this prompt:  

```
    prompt = (
        f"# Instructions\n"
        f"Read the Python script provided below and write comprehensive, step-by-step documentation in Markdown format.\n"
        f"- The very first line of your output must be the name of the script: {script_name}\n"
        f"- Include an overview, key functions, and code snippets describing how the script works, plus a one line instruction for running this script.\n\n"
        f"# Python Script: {script_name}\n"
        f"```python\n"
        f"{script_content}\n"
        f"```"
    )
```

Examples:

[formulate_cover_summary.py](formulate_cover_summary.md) - drafts a cover letter and summary

[formulate_job_desc.py](formulate_job_desc.md) - filters and summarizes a job description

## Agentic AI Code-to-Docs Pipeline

This code-to-docs pipeline leverages GitHub with the added benefit of auto-generated documentation.  Suppose you write a new Python script.  Before pushing to GitHub, run `docs_gen.py` to ingest your script and output a step-by-step user guide.  Having docs with your script, can make pull request review easier and faster.

## MCP Streamlines Generating Docs

Using the Model Context Protocol (MCP) streamlines the generation process. 

[mcp_job_desc_client.py](mcp_job_desc_client.md) - uses MCP to send a job description to mcp_job_desc_server.py for processing

[mcp_job_desc_server.py](mcp_job_desc_server.md) - filters and summarizes a job description


---

***Disclaimer:** This is demonstration code developed by Gemini for the purpose of discussing a code-to-docs pipeline leveraging Agentic AI.*

