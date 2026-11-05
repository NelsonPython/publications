# mcp_job_desc_server.py

## Overview
This script implements a FastMCP server for job description analysis, enabling the execution of various tools to assess and prioritize security clearance requirements within job descriptions.

## Dependencies
- **FastMCP**: A library for implementing machine learning models using FastAPI.
- **json**: Python's built-in JSON module for handling JSON data.
- **urllib.request**: Python's built-in library for making HTTP requests.

## Running the Script
To run the script, ensure you have the following dependencies installed:
```bash
pip install fastmcp
```

Then, execute the script using:
```bash
python mcp_job_desc_server.py
```

## Script Documentation
### `keyword_clearance_search`
**Description**: Performs a fast keyword search for explicit security clearance terms in the job text.

**Arguments**:
- `job_text`: The job description text to be analyzed.

**Returns**:
- A JSON object with:
  - `pursue`: A boolean indicating whether to pursue the clearance requirement (false if a keyword is found, true otherwise).
  - `method`: The method used to determine the clearance requirement.
  - `reason`: A string explaining the reason for pursuing the clearance requirement.

**Example**:
```json
{
  "pursue": false,
  "method": "keyword",
  "reason": "Found explicit clearance keywords: ['classified', 'secret clearance']"
}
```

### `semantic_clearance_search`
**Description**: Performs a semantic LLM-based evaluation using Ollama to check if a security clearance is required.

**Arguments**:
- `job_text`: The job description text to be analyzed.
- `model`: The name of the Ollama model to use (default is "llama3.2:latest").

**Returns**:
- A JSON object with:
  - `pursue`: A boolean indicating whether to pursue the clearance requirement (false if no explicit keyword is found, true otherwise).
  - `method`: The method used to determine the clearance requirement.
  - `reason`: A string explaining the reason for pursuing the clearance requirement.

**Example**:
```json
{
  "pursue": true,
  "method": "semantic",
  "reason": "No secret clearance keywords found via keyword search"
}
```

### `summarize_job_requirements`
**Description**: Summarizes key job requirements and skills into streamlined categories.

**Arguments**:
- `job_text`: The job description text to be analyzed.
- `model`: The name of the Qwen model to use (default is "qwen2.5-coder:1.5b").

**Returns**:
- A string containing the categorized requirements in JSON format.

**Example**:
```json
[
  "must_haves",
  "nice_to_haves",
  "core_responsibilities",
  "tech_stack"
]
```

### Running the Server
To run the server, execute the script using:
```bash
python mcp_job_desc_server.py
```

This server will listen on the default port and provide endpoints for each of the tools defined in the script.