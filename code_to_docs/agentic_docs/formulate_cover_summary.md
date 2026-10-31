# Script Name: formulate_cover_summary.py

This Python script is designed to automate the generation of a cover letter and summary for job applications.  It utilizes the Ollama API to provide human-like responses based on provided prompts, allowing for the creation of polished and professional documents tailored to each job position.

### Step-by-Step Documentation

#### 1. Importing Required Libraries

```python
import argparse
import datetime
import json
import logging
import socket
import urllib.error
import urllib.request
```

- `argparse`: Handles command-line argument parsing.
- `datetime`: For handling date and time.
- `json`: For JSON data handling.
- `logging`: For logging error messages.
- `socket`: For making network requests.
- `urllib.error`: For handling errors from the urllib library.
- `urllib.request`: For making HTTP requests to the Ollama API.

#### 2. Ollama API Call Function

```python
def call_ollama(model, system_prompt, user_content, url="http://localhost:11434/api/chat"):
    """ Send requests to the Ollama API and handle errors. """
    
    payload = {
        "model": model,
        "messages": [
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_content},
        ],
        "options": {
            "num_ctx": 2048,
            "num_predict": 2048,
            "num_thread": 4,
            "low_vram": False,
        },
        "format": "json",
        "stream": False,
        "keep_alive": 0,
    }
    
    data = json.dumps(payload).encode("utf-8")
    req = urllib.request.Request(
        url, data=data, headers={"Content-Type": "application/json"}
    )

    try:
        with urllib.request.urlopen(req, timeout=30) as response:
            res = json.loads(response.read().decode("utf-8"))
            message_data = res.get("message", {})
            raw_text = message_data.get("content", "")
            
            if not raw_text:
                logging.warning("[NO CONTENT RETURNED]")
                return ""
            return raw_text.strip()

    except urllib.error.URLError as e:
        if isinstance(e.reason, socket.timeout) or isinstance(e.reason, TimeoutError):
            logging.error("[TIMEOUT ERROR] The request exceeded the 30-second time limit and timed out.")
        else:
            logging.error(f"[REQUEST FAILURE] {e}")
    except TimeoutError:
        logging.error("[TIMEOUT ERROR] Operation timed out.")
    
    return ""
```

- **Function Definition**: `call_ollama` takes four parameters: `model`, `system_prompt`, `user_content`, and `url`.
- **Payload Construction**: The `payload` dictionary contains the model name, messages, options, format, stream, and keep_alive settings.
- **Data Encoding**: The payload is encoded into JSON format.
- **Request**: A `Request` object is created with the URL, data, and content type.
- **Response Handling**: The response is read and parsed into JSON, extracting the message content.
- **Error Handling**: Exceptions for timeout errors are caught and logged appropriately.

#### 3. Main Script Execution

```python
if __name__ == "__main__":
    ap = argparse.ArgumentParser(description="Get job descriptions and write a cover/cover_letter.txt and cover/summary.txt")
    ap.add_argument("-f", "--filepath", required=True, help="job_desc/raw_filename.txt")
    ap.add_argument("-e", "--evaluation_model", default="llama3.2:latest", help="name of the Ollama model to use")
    ap.add_argument("-m", "--summarization_model", default="qwen2.5-coder:1.5b", help="name of the Ollama model to use")
    args = vars(ap.parse_args())

    evaluation_model = args["evaluation_model"]
    summarization_model = args["summarization_model"]
    print(f'\n>>>>>>> formulate_cover_summary.py -e {evaluation_model} -m {summarization_model}')

    # SETUP LOGGING
    log_timestamp = datetime_module.datetime.now().strftime("%Y-%m-%d-%H-%M")
    log_filename = f"logs/cover_summary_{log_timestamp}.log"
    logging.basicConfig(filename=log_filename, encoding="utf-8")

    # SETUP OUTPUT PATHS 
    # tkinter passes /home/jugar/job_search_agentic/job_desc/raw_syllo_staff_technical_writer.txt, use fp[-1] to get the last item in the list, raw_syllo...
    filepath = args["filepath"]
    print(filepath)
    
    fp = filepath.split("/")
    print(fp)
    
    outletter = "cover_letters/" + fp[-1][4:-4] + ".cover"
    print(f'\n[OUTLETTER] {outletter}')
    outsum = "cover_letters/" + fp[-1][4:-4] + ".summary"
    print(f'[OUTSUM] {outsum}')

    with open(filepath, "r", encoding="utf-8") as f:
        job_text = f.read()

    # 1. WRITE COVER LETTER
    cover_system_prompt = """
Act as a professional career advisor and expert resume cover letter writer.  Draft a polished, professional cover letter.

Tone: Confident, formal, and objective.

Forbidden Words: ["excited", "thrilled", "passionate", "dream job", "humbled"]

User phrases like ["strong interest", "well-prepared to contribute", "confident in my ability to"]

Opening: Directly state the position being applied for and state key years and areas of experience.

Body Paragraph: Highlight collaboration with cross-functional teams, practical achievements, and validation of work (aligning with real-world workflows).

Closing: Express willingness to discuss how skills align with company goals, followed by a professional sign-off.

Formatting: Standard business letter format

Applicant Years of Experience: 15+ years

Applicant Key Skills: Technical Writing, Instructional Design, AI Systems

Return ONLY a valid JSON object matching this exact schema:

{
    "cover_letter": "string"
}

Sample output:
{
    "cover_letter": "I am writing to express my strong interest in the Technical Writer position.
I take ownership, independently researching until I fully master complex domains, while respectfully collaborating with teams to find innovative ways to
communicate technical concepts.  From working as an AI Subject Matter Expert at NASA to writing technical documentation at PromptMelodic,
I have consistently used AI as a professional tool. This background gives me the editing judgment needed to spot AI hallucinations and
maintain a human-AI pipeline that delivers validated guides, API references, and tutorials.
You can view my portfolio, blog, and examples of my work at https://github.com/NelsonPython.
I look forward to discussing how my technical background and documentation experience can support your goals.
"
}
"""
    print(f'\n[COVER LETTER PROMPT]\n{cover_system_prompt}\n')
    cover_result = call_ollama(summarization_model, cover_system_prompt, job_text)
    print(f'[RAW COVER LETTER TEXT] {cover_result}')
    try:
        json_cover = json.loads(cover_result)
        with open(outletter, "w") as fl:
            print(json_cover["cover_letter"], file=fl)

    except Exception as e:
        print(f'[EXCEPTION] {e}\nWriting raw cover_result')
        with open(outletter, "w") as fl:
            print(cover_result, file=fl)


    # 2. WRITE SUMMARY
    summary_system_prompt = """
You are a technical documentation specialist. Read the provided job description and summarize key responsibilities into a single concise sentence output as JSON.

Follow these instructions strictly:
1. Do not invent or extrapolate information outside of the job description, but assume you have past experience performing these responsibilities.
2. Forget salary, location, and job work mode.
3. Forget company name. Use company information only to inform the tone of your summary.
4. Keep the summary clear, concise, and deduplicated. Strictly output one sentence.
5. Return ONLY a valid JSON object matching this exact schema:

{
    "responsibilities": "One-sentence summary here."
}

Sample Output:
{
    "responsibilities": "Technical writer with experience developing high-quality technical documentation for software developers including user guides, tutorials, API reference documentation, and regulatory compliance documentation."
}
"""
    print(f'\n[SUMMARY PROMPT]\n{summary_system_prompt}\n')
    summary_result = call_ollama(summarization_model, summary_system_prompt, job_text)
    print(f'[RAW SUMMARY TEXT] {summary_result}')
    try:
        json_summary = json.loads(summary_result)
        with open(outsum, "w") as fl:
            print(json_summary["responsibilities"], file=fl)

    except Exception as e:
        print(f'[EXCEPTION] {e}\nWriting raw summary_result')
        with open(outsum, "w") as fl:
            print(summary_result, file=fl)

    print(f'\n[LOG] {log_filename}')
    print(f'[COVER LETTER] {outletter}')
    print(f'[SUMMARY] {outsum}')
```

- **Argument Parser**: Sets up command-line arguments for specifying the file path, evaluation model, and summarization model.
- **Model Selection**: Uses the provided models for cover letter and summary generation.
- **Logging Setup**: Initializes a log file to capture script execution details.
- **Output Paths**: Constructs output file paths based on the input file name.
- **Reading Input File**: Reads the job description text.
- **Generating Cover Letter**: Uses the Ollama API to generate a cover letter based on the provided system prompt and job text.
- **Generating Summary**: Uses the Ollama API to generate a summary based on the provided system prompt and job text.
- **Error Handling**: Logs exceptions and writes raw responses to output files.
- **Log Writing**: Logs the final output files and summary.

### Conclusion

The `formulate_cover_summary.py` script automates the process of generating cover letters and summaries for job applications using the Ollama API. It leverages command-line arguments to specify the input file and Ollama models, handles errors, and logs the results. This script is particularly useful for job seekers who need to quickly generate polished documents that highlight their qualifications and match the job description.
