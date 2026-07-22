# AI Resume Scorer/Parser

An LLM-powered tool that automatically screens and ranks candidate resumes against a job description — extracting structured data from both, then scoring each candidate's fit.

## What it does

Instead of manually reading through a stack of resumes, this tool:

1. Parses a job description into structured fields (required skills, preferred skills, minimum experience, education requirements, responsibilities)
2. Parses each resume in a folder (PDF or DOCX) into structured candidate data (skills, experience, education, projects, certifications)
3. Compares each candidate against the job description using an LLM and produces a match score (0–100%) with details on matching skills, missing skills, and a short verdict
4. Ranks all candidates and prints the top 2 and bottom 2 matches

## How it works

- Uses **Groq** (`openai/gpt-oss-120b`) as the LLM backend for both parsing and scoring
- Uses **Pydantic** models to enforce structured JSON output at every step (job description, resume, match result) — this keeps the LLM's output predictable and easy to work with in code
- Reads resumes directly from PDF (`pypdf`) or Word (`python-docx`) files
- Adds short delays between API calls to stay within rate limits

## Requirements

- Python 3.10+
- [uv](https://docs.astral.sh/uv/) for dependency management
- A [Groq API key](https://console.groq.com)

### Install dependencies

This project uses `uv` with a `pyproject.toml` and `uv.lock` for reproducible installs:

```bash
uv sync
```

### Set up your API key

Create a `.env` file in the project root:

```
GROQ_API_KEY=your_api_key_here
```

## Usage

1. Put the resumes you want to screen (`.pdf` or `.docx`) into a folder named `resumes/` in the project directory
2. Update the `job_description` variable in `resume_parser.py` with the job you're hiring for
3. Run the script:

```bash
uv run resume_parser.py
```

> Note: `main.py` in this repo is just the default `uv init` boilerplate and isn't used — all the actual logic lives in `resume_parser.py`.

The script will print each candidate's match score as it processes them, then show the top 2 and bottom 2 candidates with full scoring details at the end.

## Example output

```
Processing: john_doe_resume.pdf
Score: 82.0

Processing: jane_smith_resume.docx
Score: 91.0

TOP 2 CANDIDATES
Jane Smith - 91.0 %
{...match details...}
John Doe - 82.0 %
{...match details...}

LOWEST 2 CANDIDATES
...
```

## Notes / Future improvements

- Currently reads job description from a variable in the script — could be extended to accept it as a file or CLI argument
- Resume folder path is hardcoded — could be made configurable via CLI args
- No retry/error handling yet for malformed LLM responses or API failures
