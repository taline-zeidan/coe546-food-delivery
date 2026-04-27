# User Behavior Prediction

## What is this?

This is our machine learning project for COE 546.
The goal is to predict whether a user will place a food order during an app session.

---

## What You Need Installed

Before anything, make sure you have these on your computer:

**Python** — download from https://www.python.org/downloads/ (get version 3.11 or higher)

Also install these two VS Code extensions (click the Extensions icon on the left sidebar in VS Code and search for them):

- **Python** (by Microsoft)
- **Jupyter** (by Microsoft)

---

## Setup — Do This Once

### Step 1 — Open the project in VS Code

1. Unzip the folder somewhere on your computer (e.g. your Desktop)
2. Open VS Code
3. Click **File → Open Folder** and select the `user_prediction_app` folder

### Step 2 — Open the terminal inside VS Code

Click **Terminal → New Terminal** from the top menu.
A terminal panel will open at the bottom. All the next commands go here.

### Step 3 — Create a virtual environment

```
python -m venv venv
```

### Step 4 — Activate the virtual environment

```
venv\Scripts\activate
```

You will see `(venv)` at the start of the line. That means it worked.

> ⚠️ You need to do this every time you open a new terminal in VS Code.

### Step 5 — Install all required libraries

```
pip install -r requirements.txt
```

This takes a few minutes. Wait for it to finish.

### Step 6 — Select the Python interpreter

1. Press `Ctrl + Shift + P`
2. Type: `Python: Select Interpreter`
3. Choose the option that says `venv` in the path

---

## Opening and Running a Notebook

1. In the left sidebar, open the `notebooks/` folder
2. Click any `.ipynb` file to open it
3. Check the kernel selector in the top right corner — it should say `venv`. If not, click it and select the venv option
4. Run cells one by one with `Shift + Enter`, or click **Run All** at the top

---

## Every Time You Come Back to Work

1. Open VS Code → File → Open Folder → select `user_prediction_app`
2. Open the terminal: Terminal → New Terminal
3. Activate the environment: `venv\Scripts\activate`
4. Open a notebook from the sidebar and run it

---

## Project Structure

```
user_prediction_app/
│
├── data/               ← Put train.csv and test.csv here
├── notebooks/          ← All Jupyter notebooks go here
├── outputs/            ← Saved plots and results land here automatically
├── requirements.txt    ← List of libraries
└── README.md           ← This file
```

---

## Notebook Naming Convension

Please follow this naming convension when creating new notebooks:
number of the notebook in sequence
_
what it does(cleaning, whatever)
_
your name

eg: 01_EDA_taline.ipynb
02_whatever_farah.ipynb
