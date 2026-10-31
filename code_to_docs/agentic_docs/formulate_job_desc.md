### Step-by-Step Documentation for `formulate_job_desc.py`

#### Name of the Script
**formulate_job_desc.py**

#### Summary
This Python script processes job descriptions located in the `job_desc/` folder. It evaluates whether the job requires a security clearance, uses both keyword searching and language models (LLMs) via Ollama, summarizes key requirements into predefined categories, and saves the summarized job description to an output file. It logs every step of the process to a log file named `formulate_job_desc.log` in the `logs/` directory.

#### Required Arguments
- **-f, --filepath**: The path to the job description file in `job_desc/` (e.g., `job_desc/example_job.txt`).
- **-e, --evaluation_model**: The name of the Ollama model to use for evaluating whether a clearance is required (default: `llama3.2:latest`).
- **-m, --summarization_model**: The name of the Ollama model to use for summarizing the job description (default: `qwen2.5-coder:1.5b`).

#### Log and Output Paths
- The script generates a timestamp for the log filename (e.g., `logs/formulate_job_desc_2026-10-25-06-13.log`).
- The output job description is saved to an `.job` file in the same directory as the input file, with the same filename but `.job` extension.

#### Input and Output
- **Input**:
  - The script reads the job description from the specified file (`job_desc/`).
- **Output**:
  - The script writes the summarized job description to an `.job` file in the same directory.
  - A log file named `formulate_job_desc.log` in the `logs/` directory records the output of every step, including the clearance evaluation and summarization.

#### Key Functions
- **remove_HTML(raw_html: str)**: Strips HTML tags from the job description.
- **remove_special_char(text)**: Replaces space with underscore in the job description.
- **find_formulate_job_desc.py**: Main function that orchestrates the entire process.

#### Additional Notes
- The script uses Ollama, a free and open-source LLM, to evaluate whether a clearance is required and to summarize the job description.
- The script logs every step of the process to `formulate_job_desc.log`, providing detailed information about the execution.
- The script is designed to be self-contained and can be run independently.