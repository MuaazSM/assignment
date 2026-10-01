# Expense to account classifier (Peakflo take-home)

Predicts the accounting `accountName` for each bill line item in `accounts-bills.json`.

Result: **88.9% accuracy** on 5-fold cross-validation grouped by bill (95% interval 88.0 to 89.7%),
above the 85% target. The correct account is in the top three suggestions 95.3% of the time.
Full write-up in `docs/report.pdf`.

## Files

| File | What it is |
|---|---|
| `expense_classifier.ipynb` | Main deliverable. Data analysis, model, evaluation, all outputs and charts included. |
| `docs/report.pdf` | Written description: executive summary, data analysis, methodology, results, discussion. |
| `figures/` | Charts and the summary table produced by the notebook. |
| `accounts-bills.json` | The dataset. |
| `requirements.txt` | Pinned package versions. |

## Run it

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook expense_classifier.ipynb
```

Run all cells from the top. It takes about five minutes on a laptop CPU. Python 3.12 was used.
All seeds are fixed (`SEED = 42`), so the numbers reproduce exactly.

## How it works in one paragraph

Each line is cleaned (period prefix removed, name and description merged) and dated from the record ID.
If the same vendor has billed the same line before with one consistent account, that account is reused.
Otherwise a logistic regression predicts the account from word and character TF-IDF of the text, the
vendor, the quarter, the amount bucket, how many months the line covers, and how many lines the bill has.
Evaluation uses cross-validation grouped by bill so lines of one bill never leak across folds, plus a
train-on-the-past, test-on-the-future split.

## Headline numbers

| Evaluation | Accuracy | Macro F1 | Top-3 accuracy |
|---|---|---|---|
| Grouped 5-fold CV | 0.889 | 0.664 | 0.953 |
| Time split, Sep 2025 to Feb 2026 | 0.704 | 0.584 | 0.779 |

With a confidence gate, about 71% of lines can be auto-coded at 98% accuracy and the rest sent to a
reviewer with three suggestions.
