# `formulate_job_desc.py`

This script processes job descriptions saved in the `job_desc/` folder. It evaluates whether the job requires a security clearance using both keyword searching and an LLM via Ollama, summarizes key requirements into target categories, and saves the summarized job description to an output file. The script logs what happens during execution.

## Step-by-Step Documentation in Markdown

### 1. Script Name
The script is named `formulate_job_desc.py`.

### 2. Script Overview
The script processes job descriptions saved in the `job_desc/` folder. It evaluates whether the job requires a security clearance, summarizes key requirements into target categories, and saves the summarized job description to an output file. The script logs what happens during execution.

### 3. Dependencies
The script requires the following Python packages:
- `argparse`
- `datetime`
- `logging`
- `json`
- `requests`
- `re`

### 4. Main Function
The script is structured as follows:
- **Argument Parsing**: The script uses `argparse` to parse command-line arguments for the file path and the evaluation and summarization models.
- **Logging Setup**: The script sets up basic logging to a file named `formulate_job_desc.log`. It includes a timestamp in the log filename.
- **Model Setup**: The script sets up the Ollama evaluation and summarization models.
- **Input File Reading**: The script reads the job description from a file specified by the user.
- **Clearance Evaluation**: The script checks if the job requires a security clearance using keyword searching and an LLM via Ollama.
- **Summary Generation**: The script generates a summary of the job description into target categories using an LLM via Ollama.
- **Output File Writing**: The script writes the summarized job description to an output file in the `job_desc/` folder.

### 5. Additional Notes
- The script handles potential timeouts during the API request.
- The script includes error handling to manage exceptions such as timeouts and JSON parsing errors.
- The script logs various stages of the execution to help with debugging and understanding the script's behavior.

### 6. Usage
To use the script, run the following command:
```sh
python formulate_job_desc.py -f job_desc/sample_job.txt -e llama3.2:latest -m qwen2.5-coder:1.5b
```

This command will process the job description in `job_desc/sample_job.txt` using the specified evaluation and summarization models and save the results in the `job_desc/` folder.