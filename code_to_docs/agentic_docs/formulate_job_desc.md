# formulate_job_desc.py

## Description
This script processes job descriptions saved in the `job_desc/` folder. It evaluates whether the job requires a security clearance using both keyword searching and LLM via Ollama. It then summarizes key requirements into specific categories and saves the summarized job description to an output file while logging the execution process.

## Usage
```
python formulate_job_desc.py -f job_desc/filename.txt -e llama3.2:latest -m qwen2.5-coder:1.5b
```

## Input Parameters
- `-f` or `--filepath`: The path to the job description file in the `job_desc/` folder.
- `-e` or `--evaluation_model`: The name of the Ollama model to use for clearance evaluation. Default is `llama3.2:latest`.
- `-m` or `--summarization_model`: The name of the Ollama model to use for summarization. Default is `qwen2.5-coder:1.5b`.

## Output
- Logs are generated in `logs/formulate_job_desc.log`.
- The summarized job description is saved in `job_desc/filename.job`.

## Example
If the job description file `job_desc/sample.txt` contains the text:
```
We are seeking a highly skilled AI agent with a top secret clearance.
```

After running the script:
- The script will check if a clearance is required using keyword searching and will output the result.
- It will then use LLM to summarize the key requirements into specific categories and save the summarized job description to `job_desc/sample.job`.

The script will log the entire process, including any errors, in the `logs/formulate_job_desc.log` file.