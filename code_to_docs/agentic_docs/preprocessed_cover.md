# preprocessed_cover.py

This script processes a job description and outputs JSON resume snippets for new resumes based on the job description's content. It reads a job description file from a specified directory, identifies relevant expertise based on the job description's core responsibilities, and then generates cover letters based on predefined templates.

## Step-by-Step Documentation

### 1. Import Necessary Libraries

```python
import argparse
import json
```

- `argparse`: Module for parsing command-line arguments.
- `json`: Module for parsing and generating JSON data.

### 2. Set Up Argument Parser

```python
if __name__ == "__main__":
    ap = argparse.ArgumentParser(
        description="Evaluate job description and output json snippets for new resume"
    )
    ap.add_argument(
        "-j", "--job_desc",
        required=True,
        help="path to job description in the job_desc/ folder ending with *.job"
    )
    args = vars(ap.parse_args())
```

- Define an argument parser `ap` with a description and a required argument `--job_desc` that specifies the path to the job description file.
- Convert the parsed arguments into a dictionary `args`.

### 3. Read the Job Description

```python
    # JOB DESCRIPTION
    job_desc_path = args["job_desc"]
    jd = job_desc_path.split("/")
    outpath = f"/home/jugar/job_search_agentic/cover_letters/{jd[-1][:-8]}_pcover.txt"

    print(f"\nexp_cover_gen.py processing cover letter {outpath} for {job_desc_path}\n")
```

- Extract the job description file path from the command-line argument.
- Split the file path into its components and construct the output path.
- Print a message indicating the process is starting.

```python
    with open(job_desc_path, "r", encoding="utf-8") as j:
        job_description = j.read()
```

- Read the contents of the job description file and store it in `job_description`.

### 4. Parse the JSON Job Description

```python
    try:
        job_desc = json.loads(job_description)

    except json.JSONDecodeError as e:
        print(f"Error decoding JSON: {e}")
        raise SystemExit(1)
```

- Try to parse the JSON string `job_description`. If the JSON is malformed, raise an exception with the error message.

### 5. Identify Relevant Expertise

```python
    filenames = []
    cover_letter_nos = []
    for core in job_desc.get("core_responsibilities", []):
        c_lower = core.lower()

        if " ai " in c_lower:
            filenames.append("dynamic_prompts/cover_ai.txt")
            cover_letter_nos.append(0)
            print(f"[EXPERTISE FOUND] ai")

        elif "compliance" in c_lower:
            filenames.append("dynamic_prompts/cover_compliance.txt")
            cover_letter_nos.append(0)
            print(f"[EXPERTISE FOUND] compliance")

        elif "cyber" in c_lower:
            filenames.append("dynamic_prompts/cover_cybersecurity.txt")
            cover_letter_nos.append(0)
            print(f"[EXPERTISE FOUND] cyber")

        elif "software" in c_lower:
            filenames.append("dynamic_prompts/cover_sw.txt")
            cover_letter_nos.append(0)
            print(f"[EXPERTISE FOUND] software")

        elif "pmp" in c_lower:
            filenames.append("dynamic_prompts/cover_sw.txt")
            cover_letter_nos.append(1)     
            print(f"[EXPERTISE] pmp")

        else:
            print(f"[EXPERTISE NOT FOUND] {core}")

```

- Iterate through the `core_responsibilities` in the JSON job description.
- Convert the core responsibility to lowercase to make case-insensitive comparisons.
- If the core responsibility contains specific keywords like "ai", "compliance", "cyber", "software", or "pmp", append the corresponding template filename and cover letter number to `filenames` and `cover_letter_nos`, respectively.
- Print a message indicating the expertise found.

### 6. Generate Cover Letters

```python
    draft = []
    for cover_letter_no, filename in zip(cover_letter_nos, filenames):
        with open(filename, "r") as f:
            cover_letters = json.load(f)
        for letter in cover_letters:
            if int(letter['cover_letter_no']) == cover_letter_no:
                draft.append(letter['content'])
            
    with open(outpath,"w") as fo:
        for d in draft:
            fo.write(d)

    print(f"\n{outpath}")
```

- Iterate through the `cover_letter_nos` and `filenames` to load the corresponding cover letter templates.
- Find the cover letter that matches the specified cover letter number and append its content to `draft`.
- Write the draft content to the output file specified by `outpath`.

### 7. Exit with Success

```python
    print(f"\n{outpath}")
```

- Print a message indicating the completion of the process.

### Summary

The script reads a job description file, parses it, identifies relevant expertise based on the job description's content, and generates cover letters based on predefined templates. The output is saved to a specified directory. The script is designed to be flexible and can be easily extended with additional expertise templates or job descriptions.

