# Script Name: formulate_job_desc.py

## Step-by-Step Documentation

### 1. Script Overview
This script processes job descriptions saved in the `job_desc/` folder. It evaluates whether the job requires a security clearance, uses both keyword searching and LLM via Ollama, summarizes key requirements into target categories, and saves the summarized job description to an output file.

### 2. Dependencies
- Python 3.x
- Requests
- Ollama
- `argparse` for command-line arguments

### 3. Installation
1. Install the required Python packages using pip:
   ```bash
   pip install requests
   ```

2. Download the Ollama model files from the [official Ollama repository](https://github.com/ollama/ollama).

### 4. Running the Script
To run the script, use the following command:
```bash
python formulate_job_desc.py -f job_desc/job_description.txt -e llama3.2:latest -m qwen2.5-coder:1.5b
```
- `-f job_desc/job_description.txt`: Specifies the path to the job description file.
- `-e llama3.2:latest`: Specifies the name of the evaluation Ollama model.
- `-m qwen2.5-coder:1.5b`: Specifies the name of the summarization Ollama model.

### 5. Explanation of the Script

#### 5.1 Argument Parsing
The script uses `argparse` to accept command-line arguments for the job description file path, evaluation model, and summarization model.

```python
ap = argparse.ArgumentParser(description="Get raw job descriptions from job_desc/filename.txt.  Output logs/formulate_job_desc.log and job_desc/filename.job")
ap.add_argument(
    "-f", "--filepath",
    required=True,
    help="job_desc/filename.txt"
)
ap.add_argument(
    "-e", "--evaluation_model",
    default="llama3.2:latest",
    help="name of the Ollama model to use"
)
ap.add_argument(
    "-m", "--summarization_model",
    default="qwen2.5-coder:1.5b",
    help="name of the Ollama model to use"
)
args = vars(ap.parse_args())
```

#### 5.2 Logging Setup
The script sets up logging to write detailed logs to a file named `logs/formulate_job_desc.log`.

```python
log_timestamp = datetime.datetime.now().strftime("%Y-%m-%d-%H-%M")
log_filename = f"logs/formulate_job_desc_{log_timestamp}.log"
logging.basicConfig(
    filename=log_filename,
    encoding="utf-8"
)
```

#### 5.3 Ollama Model Setup
The script defines the Ollama model URLs for the evaluation and summarization models.

```python
evaluation_model = args["evaluation_model"]
summarization_model = args["summarization_model"]
ollama_url = "http://localhost:11434/api/chat"
```

#### 5.4 Target Categories
The script defines the target categories for summarizing job requirements.

```python
target_categories = [
    "agentic ai",
    "ai agent",
    "ai prompt",
    "core requirement",
    "document creation skill",
    "experience",
    "job description",
    "job requirement",
    "must have",
    "preferred qualification",
    "requirement",
    "required_skills",
    "responsibilities",
    "technical translator",
    "technical writing experience"
]
```

#### 5.5 Outpath Setup
The script sets the output path for the summarized job description.

```python
filepath = args["filepath"]
outpath = filepath.replace(".txt",".job")
```

#### 5.6 Reading Job Description
The script reads the job description from the specified file and removes HTML tags and special characters.

```python
with open(filepath, "r") as f:
    job_text = f.read()

remove_html(job_text)
remove_special_char(job_text)
```

#### 5.7 Evaluating Clearance Requirement
The script uses an LLM to determine whether the job requires a security clearance.

```python
prompt = f"""
SYSTEM INSTRUCTIONS:
You are a literal, factual parser. You check if a job requires a "Secret Clearance".

RULES:
1. "pursue": false ONLY if the text explicitly states "Secret Clearance" or "Secret Security Clearance".

2. "pursue": true for general requirements like "background check", "remote office", "citizenship", or "public trust". Do NOT assume or extrapolate.
3. In "reason", quote the exact phrase, if found.

EXAMPLES:
Text: "Must hold an active Secret Clearance."
Output: {{"pursue": false, "reason": "Requires active Secret Clearance"}}

Text: "Must have a dedicated home office and pass a background check."
Output: {{"pursue": true, "reason": "No secret clearance required"}}

NOW EVALUATE THIS JOB DESCRIPTION:
{job_text}
                """

payload = {
    "model": evaluation_model,
    "messages": [{"role": "user", "content": prompt}],
    "stream": False,
    "format": "json",
    "options": {"temperature": 0.0},
}

req = urllib.request.Request(
    ollama_url,
    data=json.dumps(payload).encode("utf-8"),
    headers={"Content-Type": "application/json"},
)

try:
    with urllib.request.urlopen(req) as response:
        result = json.loads(response.read().decode("utf-8"))
        clearance_eval = json.loads(result.get("message", {}).get("content", "{}"))
        
except Exception as e:
    clearance_eval = {"pursue": False, "reason": f"API Error: {e}"}

print(f'\n[LLM EVAL] clearance_eval {clearance_eval}')
```

#### 5.8 Summarizing Job Description
The script uses the LLM to summarize the job description into target categories.

```python
formatted_categories = "\n".join([f"- {cat}" for cat in target_categories])
system_prompt = f"""
Read the job description provided. Identify and summarize key job requirements and skills.

Categorize each summarized requirement STRICTLY under one of the following exact category keys:
{formatted_categories}

Format requirements:
1. Output ONLY valid JSON where the keys are selected from the allowed categories listed above, and the values are lists of summarized requirement strings.
2. Do not invent new category keys outside of this list.
3. Keep summaries clear, concise, and deduplicated.
            """

payload = {
    "model": summarization_model,
    "messages": [
        {
            "role": "system",
            "content": system_prompt,
        },
        {   "role": "user",
            "content": job_text
        },
    ],
    "options": {
        "num_ctx": 2048,        # Limit context window to reduce RAM allocation
        "num_predict": 2048,    # Cap max output tokens 256, 512, 1024, 2048
        "num_thread": 4,        # Limit CPU threads
        "low_vram": False,      # Set to True if running on low GPU VRAM
    },
    "format": "json",           # Forces structured JSON output
    "stream": False,            # Unload from memory immediately after execution (0s)
    "keep_alive": 0,
}

data = json.dumps(payload).encode("utf-8")
req = urllib.request.Request(
    ollama_url, data=data, headers={"Content-Type": "application/json"}
)

succinct_job_desc = None

try:
    with urllib.request.urlopen(req, timeout=30) as response:
        res = json.loads(response.read().decode("utf-8"))
        message_data = res.get("message", {})
        raw_text = message_data.get("content", "")
            
        if not raw_text:
            logging.warning("[NO SUMMARY ln 227]")
        else:
            raw_text = raw_text.strip()
            try:
                succinct_job_desc = json.loads(raw_text)
                if succinct_job_desc is None:
                    logging.warning("[CANNOT LOAD] succinct_job_desc ln 233")

            except json.JSONDecodeError as jde:
                logging.error(f"[PARSE JSON FAILED] {jde}")
                logging.error(f"{raw_text}")
                succinct_job_desc = None

except urllib.error.URLError as e:
    # Check for timeouts
    import socket
    if isinstance(e.reason, socket.timeout) or isinstance(
        e.reason, TimeoutError
    ):
        logging.error("\n[TIMEOUT ERROR] The request exceeded the 30-second time limit and timed out.")
    else:
        logging.error(f"\n[REQUEST FAILURE] {e}")

except TimeoutError:
    logging.error("\n[TIMEOUT ERROR] Operation timed out.")
```

#### 5.9 Parsing Job Description
The script parses the summarized job description into key requirements.

```python
requirements = []
if succinct_job_desc:
    seen = set()
    requirements = []
    if isinstance(succinct_job_desc, dict):
        for val in succinct_job_desc.values():
            for item in val:
                if item not in seen:
                    seen.add(item)
                    requirements.append(item) 

    # REPORT
    if isinstance(requirements, str):
        requirements = [requirements]
        
    print(f'\n[LOG] {log_filename}')
    print(f'\n[JOB DESCRIPTION] {outpath}')
```

### 6. Output
The script outputs the log file path and the summarized job description path.

```python
print(f'\n[LOG] {log_filename}')
print(f'\n[JOB DESCRIPTION] {outpath}')
```

### 7. Conclusion
The `formulate_job_desc.py` script provides a comprehensive approach to processing job descriptions, evaluating clearance requirements, summarizing key requirements, and saving the results.
