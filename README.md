# Natural Language to SQL Query Generator

Ask questions about your data in plain English and get back SQL, executed against a local SQLite database. Built with **prompt engineering** on Google's **Gemini 2.5 Flash** (no fine-tuning required).

Notebook: [`Text2SQL_Prompt_Engineering.ipynb`](Text2SQL_Prompt_Engineering.ipynb)

## How it works

1. **Generate data**: mock e-commerce data (`customers`, `products`, `orders`) is pulled from [Mockaroo](https://mockaroo.com).
2. **Build the database**: CSVs are loaded into SQLite (`ecommerce.db`) with explicit schemas and enforced data types.
3. **Prompt**: a structured prompt tells Gemini the role, context (full schema), task, constraints (SQLite syntax, `SELECT` only, no invented columns) and few-shot examples.
4. **Generate + execute**: `text2sql()` sends the question to Gemini, strips the code fences from the reply, runs the SQL, and returns a pandas DataFrame.

```
question --> prompt (role + schema + rules + examples) --> Gemini --> SQL --> SQLite --> DataFrame
```

## Example questions

- Show me the order count by country
- What are my top 10 most popular products
- Which country ranks in the middle by total sales?
- What is the 2nd highest sold product for each country?
- On which day of the week do I get the most orders?

## Setup

```bash
pip install -r requirements.txt
```

1. Get a free Gemini API key from [Google AI Studio](https://aistudio.google.com/).
2. In Colab, add it under **Secrets** as `GOOGLE_API_KEY`. Locally, replace `userdata.get('GOOGLE_API_KEY')` with `os.environ["GOOGLE_API_KEY"]`.
3. Get a Mockaroo API key and replace `YOUR_MOCKAROO_API_KEY` in the data-download cell (or use your own CSVs).
4. Run the notebook top to bottom.

> The notebook was written for Google Colab (it uses `google.colab.userdata` and `/content/` paths). Adjust those if you run it elsewhere.

## Project structure

```
.
├── Text2SQL_Prompt_Engineering.ipynb
├── requirements.txt
├── .gitignore
└── README.md
```

## Possible next steps

- Add a second dataset (e.g. an `employees` / `departments` schema) to test generalisation
- Retrieval-Augmented Generation for large schemas
- Validate / retry generated SQL on error
- Agent-based multi-step reasoning
