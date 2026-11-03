# Agentic Job Search Experiments

Goal:  Build an AI-powered resume & cover letter pipeline.

## 1. Job Description Ingestion & Filtering

The pipeline begins by ingesting a target job description—sourced via a job board API or a quick copy-paste. A local Python script then launches an AI prompt to evaluate and filter the position, summarizes lengthy postings, drafts a custom resume headline, and generates a tailored cover letter.

## 2. Modular Prompt Templates

#### Approach 1:  

Rather than treating a resume as a single monolithic block of text, the system uses dedicated prompt templates for each individual section (such as **Core Competencies**, **Education**, and **Professional Experience**). A Python script automates prompt generation by seamlessly merging these templates with specific job requirements and the corresponding resume data.

#### Approach 2: The Gemini Prompt

Because cloud models like Gemini significantly outperform local Ollama models running on a virtual machine, the pipeline compiles a specialized prompt for Gemini’s input box. The resulting output is pasted into the `Gemini Prompt Parser` and parsed cleanly into structured sections.

#### Approach 3:  Vector database

See [Part 2: Validating AI Prompts](https://github.com/NelsonPython/ai_prompts/blob/main/articles/part_2_validating_ai_prompt.md) for details.

## 3. Gathering, Sorting, & JSON Validation

#### Approach 1:

Outputs from each prompt are matched against sections of the master resume to ensure validated content. The new resume data is stored in **JSON**.  Professional experience dates are automatically sorted and any missing dates are flagged. The JSON data can be compiled into text, Markdown, or Word formats.

## 4. Review & Custom Management Tools

While human review remains essential, traditional manual editing in Word is time-consuming and error-prone. To streamline this, a specialized tool allows for adding or removing resume sections interactively and saving changes directly back into the JSON structure.

## 5. Submission

Currently, the final, polished cover letter and resume are submitted manually to ensure complete oversight before applications go out.

## 6. Current Horizon: Conquering Hallucinations

For the pipeline to achieve full autonomy, AI prompts must consistently deliver clear, validated results. 

Results achieved using Approach 1 have been disappointing.  Models often hallucinate skills that match requirements rather than accurately matching skills from the resume.  [Part 1: Validating AI Prompts](https://github.com/NelsonPython/ai_prompts/blob/main/articles/part_1_validating_ai_prompt.md) for details.

Approach 3 aims to improve accuracy by transitioning to more structured prompts. See [Part 2: Validating AI Prompts](https://github.com/NelsonPython/ai_prompts/blob/main/articles/part_2_validating_ai_prompt.md) for details.




