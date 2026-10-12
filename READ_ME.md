# Code-Mixed / Hinglish Part-of-Speech (POS) Tagger

A Natural Language Processing (NLP) project to assign a Part-of-Speech
(POS) label to each token in Hindi-English code-mixed text. This README
covers the initial setup: locating the COMI-LINGUA POS data, creating
the project locally, and publishing the repository on GitHub. Model
training and evaluation will be added in later phases.

> **Important dataset check:** COMI-LINGUA contains code-mixed
> Hindi-English data, but not every version necessarily uses
> Roman-script Hinglish. Before training, verify the script, annotation
> format, tagset, dataset license, and official split.

## 1. Project goal

Given a sentence such as:

``` text
Mujhe assignment submit karna hai
```

the intended system predicts a POS tag for each token, for example:

  Token        Illustrative POS tag
  ------------ ----------------------
  Mujhe        PRON
  assignment   NOUN
  submit       VERB
  karna        VERB
  hai          AUX

These labels are **illustrative only**. The project must use the exact
labels and annotation guidelines of the selected dataset; do not treat
this example as ground truth.

## 2. Dataset: COMI-LINGUA

Start with the COMI-LINGUA dataset pages below:

-   **Dataset card:**
    <https://huggingface.co/datasets/LingoIITGN/COMI-LINGUA>
-   **POS training CSV (reported location; verify it still exists):**
    <https://huggingface.co/datasets/Tanushreeeeee/COMI-LINGUA/blob/main/POS_train.csv>
-   **POS test CSV (reported location; verify it still exists):**
    <https://huggingface.co/datasets/Tanushreeeeee/COMI-LINGUA/blob/main/POS_test.csv>

The files `POS_train.csv` and `POS_test.csv` have been identified as
possible POS-specific files. Dataset repositories can change, so confirm
that the links open and that the files really contain token-level POS
annotations before proceeding.

### What to verify before using the data

1.  **Task and labels:** The file must contain POS tags, not only
    language-identification, sentiment, or translation labels.
2.  **Data format:** Identify the columns for tokens/sentences and POS
    labels. Check whether each row is a token, a complete sentence, or a
    serialized list.
3.  **Script:** Check whether Hindi words are in Devanagari or Roman
    script. If the assignment specifically requires Romanized Hinglish,
    document any mismatch before deciding whether to use the dataset.
4.  **Tagset:** Record the tag names and their definitions from the
    dataset documentation.
5.  **Splits:** Prefer the dataset's official train/test split. Create a
    validation split from training data if required; do not tune on the
    test set.
6.  **License and citation:** Read the dataset card's license and
    citation instructions. Follow its terms when using or redistributing
    the data.

### Download the CSV files

Open the POS CSV links above and use Hugging Face's download option if
the files are available. Save the files locally under `data/raw/`:

``` text
data/raw/POS_train.csv
data/raw/POS_test.csv
```

If the dataset uses a different filename or format, keep the original
name and update your notes accordingly.

### Inspect the files with Python

Install the initial data-inspection dependencies:

``` bash
python -m pip install pandas
```

Create a temporary inspection script or run this in a Python session
from the repository root:

``` python
from pathlib import Path
import pandas as pd

train_path = Path("data/raw/POS_train.csv")
test_path = Path("data/raw/POS_test.csv")

train = pd.read_csv(train_path)
test = pd.read_csv(test_path)

print("Train shape:", train.shape)
print("Train columns:", train.columns.tolist())
print(train.head())
print("\nTest shape:", test.shape)
print("Test columns:", test.columns.tolist())
print(test.head())
print("\nMissing values in train:\n", train.isna().sum())
```

This code only inspects the files. It does **not** assume which columns
contain tokens or tags, and it does not train a model. Inspect the
output and dataset documentation before implementing preprocessing.

## 3. Create the local project

### Requirements

-   Python 3.10 or newer (use a version supported by the libraries you
    install)
-   Git
-   A GitHub account
-   VS Code or Jupyter Notebook

### Create the folder structure

In a terminal:

``` bash
mkdir hinglish-pos-tagger
cd hinglish-pos-tagger

mkdir -p data/raw data/processed notebooks src results
touch README.md requirements.txt .gitignore
```

Suggested initial structure:

``` text
hinglish-pos-tagger/
├── data/
│   ├── raw/          # Original downloaded data (usually not committed)
│   └── processed/    # Cleaned/converted data (usually not committed)
├── notebooks/        # Dataset inspection and experiments
├── src/              # Reusable Python code (later phase)
├── results/          # Metrics and generated outputs (later phase)
├── .gitignore
├── requirements.txt
└── README.md
```

### Create a virtual environment

**Linux/macOS:**

``` bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install pandas
```

**Windows PowerShell:**

``` powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install pandas
```

Record the initial dependency:

``` text
pandas
```

in `requirements.txt`. Add the model-specific packages later when the
dataset format and model approach are decided.

## 4. Protect the dataset and environment files

Add the following to `.gitignore`:

``` gitignore
# Python environments and cache
.venv/
venv/
__pycache__/
*.py[cod]

# Secrets and local environment configuration
.env

# Raw and processed data: keep large/licensed datasets out of Git by default
data/raw/*
data/processed/*

# Keep directory placeholders
!data/raw/.gitkeep
!data/processed/.gitkeep

# Generated outputs
results/
.ipynb_checkpoints/
```

If you want Git to retain the empty data directories, create
placeholders:

``` bash
touch data/raw/.gitkeep data/processed/.gitkeep
```

**Do not upload dataset files to a public repository unless the dataset
license explicitly permits redistribution.** Even if redistribution is
permitted, large CSV files are usually better downloaded separately or
managed with an appropriate data-hosting solution. Do not commit
credentials, access tokens, or private information.

## 5. Initialize Git locally

From inside `hinglish-pos-tagger/`:

``` bash
git init
git add README.md requirements.txt .gitignore data/raw/.gitkeep data/processed/.gitkeep
git status
git commit -m "Initialize Hinglish POS tagger project"
```

If Git asks you to configure your identity, set the name and email you
want associated with your commits:

``` bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Then repeat the commit command if needed.

## 6. Create the GitHub repository

1.  Go to <https://github.com/new>.
2.  Set the repository name to `hinglish-pos-tagger` (or choose your
    own).
3.  Add a short description, such as:
    `POS tagging for Hindi-English code-mixed text`.
4.  Choose **Public** or **Private** according to your needs.
5.  If you already created `README.md` locally, leave **Add a README
    file** unchecked to avoid an unnecessary initial merge.
6.  Click **Create repository**.

## 7. Upload the local project to GitHub

GitHub will show a repository URL. Copy your own URL and run the
following commands from the local project directory, replacing the
example URL with your repository URL:

``` bash
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/hinglish-pos-tagger.git
git push -u origin main
```

If Git says `origin` already exists, check it with:

``` bash
git remote -v
```

If necessary, correct it with:

``` bash
git remote set-url origin https://github.com/YOUR-USERNAME/hinglish-pos-tagger.git
```

Then push again:

``` bash
git push -u origin main
```

GitHub may prompt you to authenticate through a browser or credential
manager. Use GitHub's supported sign-in flow; a normal account password
is not accepted for Git-over-HTTPS authentication.

## 8. Verify the repository

After pushing, refresh your GitHub repository page and check that it
contains:

-   `README.md`
-   `requirements.txt`
-   `.gitignore`
-   `data/raw/.gitkeep`
-   `data/processed/.gitkeep`

The CSV files should normally remain local and ignored. If you
accidentally staged a dataset file before committing, unstage it with
`git rm --cached path/to/file` and check `.gitignore` before pushing.

## 9. Current milestone checklist

-   [ ] Open the COMI-LINGUA dataset card.
-   [ ] Verify the POS training and test CSV links.
-   [ ] Confirm that the files contain token-level POS annotations.
-   [ ] Check script, tagset, license, citation, and official splits.
-   [ ] Download the files to `data/raw/` locally.
-   [ ] Inspect columns, rows, and missing values with pandas.
-   [ ] Create the local project structure and virtual environment.
-   [ ] Add `.gitignore` so data and environment files are not
    committed.
-   [ ] Initialize Git and make the first commit.
-   [ ] Create the GitHub repository.
-   [ ] Push the initial project and verify it online.

## Next phase

Once the dataset has been verified, the next phase is to document its
schema, convert it into sentence/token/tag sequences, perform
exploratory data analysis, and establish a baseline POS tagger. Do not
begin model training until the annotation format and label scheme are
understood.

## Acknowledgements

Dataset authors and maintainers should be credited according to the
citation and license instructions on the official dataset card. Add the
exact citation here after confirming the dataset version used.
