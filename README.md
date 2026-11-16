# Natural-Language-to-SQL Query Interface (Spider)

Converts plain-English questions into SQLite queries over the [Spider](https://yale-lily.github.io/spider) benchmark, validates each query by running it, and explains the result in natural language. Five generation methods are compared on the same 100 questions.

## Methods compared
1. Zero-shot
2. Few-shot (4 examples from a different database)
3. Chain-of-thought with a query plan
4. Self-correction chain (execute, review, fix; up to 3 rounds)
5. LangChain SQL agent with database tools

## Results (100 Spider dev questions, 25 each: simple, aggregation, join, nested)

| Method | Execution acc. | Exact match | Avg tokens | Avg time (s) | Avg corrections |
|---|---|---|---|---|---|
| Zero-shot | 86% | 23% | 621 | 3.70 | 0 |
| Few-shot | 85% | 33% | 833 | 4.47 | 0 |
| Chain-of-thought | 88% | 21% | 918 | 5.81 | 0 |
| Self-correction | 86% | 20% | 1717 | 10.64 | 0.05 |
| SQL agent | 81% | 23% | 3666 | 19.86 | 0.25 |

Exact match is a normalised text comparison, stricter than the official Spider metric. Joins and nested queries are the hardest categories. Self-correction and the agent cost far more tokens and time without beating plain prompting.

See [docs/architecture.md](docs/architecture.md) for the design.

## Setup
1. Get a free API key from [Groq](https://console.groq.com).
2. Open `notebooks/NL2SQL_Project.ipynb` in Google Colab.
3. Add the key as a Colab Secret named `GROQ_API_KEY`.
4. Run the notebook top to bottom. The Spider dev set is downloaded inside the notebook.

To run locally instead: `pip install -r requirements.txt`, set `GROQ_API_KEY` as an environment variable, and open the notebook in Jupyter.

## Repository layout
```
notebooks/NL2SQL_Project.ipynb   full project: generators, evaluation, self-correction, agent, demo
docs/architecture.md             architecture and design decisions
requirements.txt                 pinned dependencies
```

## Limitations
- Groq free tier limits (daily and per-minute tokens) force resumable runs; one very large agent request (q76) exceeded the per-minute cap and is counted as wrong.
- The agent fails on some Spider tables with date-typed columns when reading sample rows, so it falls back to schema-only.
- Only prompting methods were compared, not different models.
- Based on a 100-question sample, so small differences between methods are not significant.

## Reference
Huang et al., 2024, *Large Language Models Cannot Self-Correct Reasoning Yet*.
