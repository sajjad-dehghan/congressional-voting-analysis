# Congressional Voting Patterns Analysis

Analyzing the 1984 U.S. House of Representatives voting records with **association rule mining written from scratch** and a **Decision Tree** that predicts party affiliation (Democrat / Republican) from votes.

## Dataset

The **1984 United States Congressional Voting Records** from the UCI Machine Learning Repository (`data/house-votes-84.data`; attribute descriptions in `data/house-votes-84.names`).

- 435 members: 267 Democrats and 168 Republicans
- 16 key votes, each recorded as `y` (yea), `n` (nay), or `?`. Per the dataset notes, `?` doesn't mean the value is unknown, only that the vote was neither yea nor nay.

The 16 votes are: `handicapped-infants`, `water-project-cost-sharing`, `adoption-of-the-budget-resolution`, `physician-fee-freeze`, `el-salvador-aid`, `religious-groups-in-schools`, `anti-satellite-test-ban`, `aid-to-nicaraguan-contras`, `mx-missile`, `immigration`, `synfuels-corporation-cutback`, `education-spending`, `superfund-right-to-sue`, `crime`, `duty-free-exports`, `export-administration-act-south-africa`.

## Approach

Everything is in `src/main.ipynb`.

### 1. Association rule mining (from scratch)

- Each member becomes a transaction made of their party, every issue they voted **yea** on, and an `unknown` item for `?` votes.
- **Frequent itemsets:** item supports are counted with plain Python, then itemsets of growing size are enumerated with `itertools.combinations`. Only itemsets with support >= `MIN_SUPPORT = 110` are kept, and the search stops when no itemset of the next size survives.
- **Rules:** rules are generated from the largest frequent itemsets and kept when their confidence is >= `MIN_CONFIDENCE = 60%`.

Rules found in the notebook output (confidence as printed):

| Rule | Confidence |
|------|-----------:|
| (physician_fee_freeze, el_salvador_aid, religious_groups_in_schools, education_spending, superfund_right_to_sue) -> republican | 95.65% |
| (physician_fee_freeze, el_salvador_aid, education_spending, superfund_right_to_sue, crime) -> republican | 95.76% |
| (republican, el_salvador_aid, religious_groups_in_schools, education_spending, superfund_right_to_sue) -> physician_fee_freeze | 100.0% |
| republican -> (physician_fee_freeze, el_salvador_aid, religious_groups_in_schools, superfund_right_to_sue, crime) | 70.83% |

In other words, a yea vote on this group of issues is a strong signal of a Republican member.

### 2. Decision Tree classification

- Votes are encoded as `n` = 0, `y` = 1, `?` = 2.
- 80% train / 20% test split (`random_state=42`).
- `DecisionTreeClassifier(random_state=42)`, evaluated with `classification_report` and accuracy.
- The tree is plotted with `plot_tree` and printed as text rules with `export_text`. Its root split is `physician_fee_freeze`.

Results on the 87-member test set, as printed:

| Class | Precision | Recall | F1 | Support |
|-------|----------:|-------:|---:|--------:|
| democrat | 0.95 | 0.96 | 0.96 | 56 |
| republican | 0.93 | 0.90 | 0.92 | 31 |

**Accuracy:** `0.9425287356321839`

![Association rules output](./photo/Association_rules.png)

![Decision Tree evaluation](./photo/Decision_Tree.png)

## Tech Stack

Python, Jupyter Notebook, pandas, scikit-learn, Matplotlib, `itertools`

## Project Structure

```
congressional-voting-analysis/
├── data/
│   ├── house-votes-84.data     # dataset
│   ├── house-votes-84.names    # dataset description
│   └── Index
├── photo/
│   ├── Association_rules.png
│   └── Decision_Tree.png
├── src/
│   └── main.ipynb              # association rules + decision tree
├── requirements.txt
└── README.md
```

## How to Run

```bash
git clone https://github.com/sedwna/congressional-voting-analysis.git
cd congressional-voting-analysis
pip install -r requirements.txt notebook
cd src
jupyter notebook main.ipynb
```

The notebook reads `../data/house-votes-84.data`, so start it from `src/`.

## Acknowledgments

Dataset: [UCI Machine Learning Repository, Congressional Voting Records](https://archive.ics.uci.edu/dataset/105/congressional+voting+records).

## Contact

- Email: sajaddehqan2002@gmail.com
- LinkedIn: [profile](https://www.linkedin.com/in/sajad-dehqan-189a0b258/)
