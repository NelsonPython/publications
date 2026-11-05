# mcp_job_desc_server.py

## Overview

This Python script provides a FastMCP instance for processing job descriptions using various tools and techniques. The FastMCP is designed to handle complex tasks such as keyword clearance search, semantic LLM-based evaluation, and job requirement summarization.

## Installation

To run this script, you need to have the FastMCP library installed. You can install it using pip:

```bash
pip install fastmcp
```

## Usage

### Setting Up the FastMCP Server

First, you need to set up the FastMCP server. You can do this by running the following command:

```bash
mcp --setup
```

This command initializes the FastMCP server and sets up the necessary components.

### Running the Script

To run the script, you can use the following command:

```bash
python mcp_job_desc_server.py
```

This command starts the FastMCP server, which will listen for incoming requests and process the job descriptions accordingly.

### Tools and Techniques

#### Keyword Clearance Search

The `keyword_clearance_search` tool performs a fast keyword search for explicit security clearance terms in the job text. It checks for terms like "classified", "top secret", "secret clearance", and "dod" and returns whether a security clearance is required.

#### Semantic Clearance Search

The `semantic_clearance_search` tool performs a semantic LLM-based evaluation using Ollama to check if a security clearance is required. It uses the specified model to analyze the job text and determine if a security clearance is necessary.

#### Summarize Job Requirements

The `summarize_job_requirements` tool summarizes key job requirements and skills into streamlined categories. It categorizes each requirement under one of the specified category keys and formats the output as JSON.

### Example Usage

Here's an example of how to use the `summarize_job_requirements` tool:

```python
from fastmcp import FastMCP

mcp = FastMCP("Job Processing Server")

job_description = """
Must hold an active Secret Clearance.
Must have a dedicated home office and pass a background check.
Citizenship required.
Public trust is preferred.
"""

summary = mcp.summarize_job_requirements(job_description)

print(summary)
```

This will output the summarized job requirements and skills as JSON.

## API Endpoints

The FastMCP server provides the following API endpoints:

- **GET /api/prompt**: Get a prompt definition for a specific tool.
- **POST /api/job_desc**: Process a job description using the FastMCP server.

### Prompt Definitions

The FastMCP server provides prompt definitions for each tool using the `keyword_clearance_search`, `semantic_clearance_search`, and `summarize_job_requirements` functions. These prompt definitions are defined in the script and can be customized as needed.

## Conclusion

The `mcp_job_desc_server.py` script provides a comprehensive solution for processing job descriptions using various tools and techniques. With its fast keyword search, semantic LLM-based evaluation, and job requirement summarization capabilities, it can be used to automate the job description processing process and improve efficiency.