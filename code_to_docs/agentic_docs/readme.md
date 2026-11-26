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

[formulate_cover_summary.py](formulate_cover_summary.md) - first attempt to draft a cover letter and summary

[formulate_job_desc.py](formulate_job_desc.md) - first attempt to filter and summarize a job description

## Code-to-Docs Pipeline for GitHub

This prompt can be wrapped in a Python script called `docs_gen.py` so that it runs when you add a new script to your local copy of a GitHub repo.  It ingests your code and outputs a step-by-step developer guide.  When you issue a pull request, it includes both code and user guide.

## Model Context Protocol (MCP)

MCP standardizes how AI models and local or remote data sources and tools talk to each other.  

[mcp_job_desc_client.py](mcp_job_desc_client.md) - uses MCP to send a job description to mcp_job_desc_server.py for processing

[mcp_job_desc_server.py](mcp_job_desc_server.md) - filters and summarizes a job description


---

***Disclaimer:** This is demonstration code developed by Gemini for the purpose of discussing a code-to-docs pipeline leveraging Agentic AI.*

