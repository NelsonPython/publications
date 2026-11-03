# Agentic Job Search: Building an AI-Powered Resume & Cover Letter Pipeline

## 1. Job Description Ingestion & Filtering

The pipeline begins by ingesting a target job description—sourced via a job board API or a quick copy-paste. A local Python script then launches an AI prompt to evaluate and filter the position, summarizes lengthy postings, drafts a custom resume headline, and generates a tailored cover letter.

## 2. Modular Prompt Templates

Rather than treating a resume as a single monolithic block of text, the system uses dedicated prompt templates for each individual section (such as **Core Competencies**, **Education**, and **Professional Experience**). A Python script automates prompt generation by seamlessly merging these templates with specific job requirements and the corresponding resume data.

## 3. The Gemini Engine

Because cloud models like Gemini significantly outperform local Ollama models running on a virtual machine, the pipeline compiles a specialized prompt for Gemini’s input box. The resulting output is pasted back into a full resume model and parsed cleanly into structured sections.

## 4. Gathering, Sorting, & JSON Validation

Outputs from each prompt are matched against sections of the master resume to ensure validated content. The new resume data is stored in **JSON**, where professional experience dates are automatically sorted and any missing dates are flagged. Finally, the JSON data can be compiled into text, Markdown, or Word formats.

## 5. Review & Custom Management Tools

While human review remains essential, traditional manual editing in Word is time-consuming and error-prone. To streamline this, a specialized tool allows for adding or removing resume sections interactively and saving changes directly back into the JSON structure.

## 6. Submission

Currently, the final, polished cover letter and resume are submitted manually to ensure complete oversight before applications go out.

## 7. Current Horizon: Conquering Hallucinations

For the pipeline to achieve full autonomy, AI prompts must consistently deliver clear, validated results. Right now, plain text prompts occasionally cause models to blur the line between job requirements and resume text, leading to hallucinations. Transitioning to more structured prompt formats is the key to solving this—as detailed in [Part 1: Validating AI Prompts](https://github.com/NelsonPython/ai_prompts/blob/main/articles/validating_prompt_output.md).



