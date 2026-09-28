# Student Exam Performance Predictor

A small end-to-end machine learning application that estimates a student's math score from their student profile, reading score, and writing score. It includes a Flask web interface, a preprocessing pipeline, model training code, and saved model artifacts.

## Features

- Collects student profile information and reading/writing scores through a web form.
- Applies a saved preprocessing pipeline to the submitted values.
- Predicts the student's math score using the trained regression model.
- Includes scripts to split the source data, fit preprocessing, compare models, and save updated artifacts.

## Requirements

- Python 3.8 is recommended for the provided model artifacts.
- The runtime dependency versions are listed in `requirements.txt`. In particular, scikit-learn is pinned to `1.2.1` to match the version used to serialize the model and preprocessor, and `dill` is required to load the saved artifacts.

Do not upgrade scikit-learn independently while continuing to use the existing artifacts. If you change the scikit-learn version, retrain and regenerate both the model and preprocessor in the same environment.

## Setup

Open a terminal in the project root. To create a fresh environment on Windows with the Python launcher:

```powershell
py -3.8 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If a Python environment is already active, skip creating and activating `.venv`, then install the dependencies with:

```powershell
python -m pip install -r requirements.txt
```

Run all commands from the project root so the relative dataset and artifact paths resolve correctly.

## Run The Web App

Start the Flask development server:

```powershell
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000). The home page links to the prediction form. Submit the form to receive an estimated math score. Stop the local server with `Ctrl+C`.

The application has two routes:

| Route | Method | Purpose |
| --- | --- | --- |
| `/` | GET | Displays the project home page. |
| `/predictdata` | GET | Displays the student prediction form. |
| `/predictdata` | POST | Runs preprocessing and prediction, then displays the result. |

The form accepts gender, race/ethnicity group, parental education, lunch type, test preparation status, reading score, and writing score. Scores must be between 0 and 100.

## Retrain The Model

The source dataset is `notebook/data/stud.csv`. From the project root, run:

```powershell
python -m src.components.data_ingestion
```

This command performs the train/test split, fits the preprocessing pipeline, evaluates candidate regressors with grid search, and saves the selected model. Training can take several minutes depending on the machine.

Generated files are written to `artifacts/`:

- `data.csv`: copy of the source dataset.
- `train.csv` and `test.csv`: 80/20 split using random seed 42.
- `preprocessor.pkl`: fitted imputation, scaling, and one-hot-encoding pipeline.
- `model.pkl`: selected regression model.

The training and prediction code must use the same scikit-learn version. After retraining, run the web app using the same environment that produced the artifacts.

## Project Structure

- `app.py`: Flask application and routes.
- `artifacts/`: saved model, preprocessor, and data splits.
- `notebook/data/stud.csv`: source student performance dataset.
- `notebook/`: exploratory analysis and model-training notebooks.
- `src/components/data_ingestion.py`: data split and training entry point.
- `src/components/data_transformation.py`: feature preprocessing pipeline.
- `src/components/model_trainer.py`: candidate model evaluation and training.
- `src/pipeline/predict_pipeline.py`: prediction input and inference.
- `templates/index.html`: home page.
- `templates/home.html`: prediction form and result display.
- `requirements.txt`: Python dependencies.

## Troubleshooting

- **`ModuleNotFoundError: dill`**: activate the project environment and run `python -m pip install -r requirements.txt`.
- **`OneHotEncoder` attribute errors or scikit-learn version warnings**: use scikit-learn `1.2.1` with the existing artifacts, or retrain both artifacts under the version you intend to use.
- **Dataset file not found during training**: run the training command from the project root and confirm `notebook/data/stud.csv` exists.
- **Model or preprocessor file not found during prediction**: run the training command to generate `artifacts/model.pkl` and `artifacts/preprocessor.pkl`.