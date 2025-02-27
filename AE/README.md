# Shared Task 1: Acronym Extraction

# Dataset

The data folder contains data for English (Legal domain and Scientific domain). The corresponding folder for each language contains two original data files:

- **train.json**: The training samples for acronym extraction task. Each sample has three attributes:
  - text: The string value of the sample text
  - acronyms: A list of tuples representing the acronyms in the text. Each tuple has two integer value representing the index of the first and last character of the acronym in the text, respectively.
  - long-forms: A list of tuples representing the long-forms in the text. Each tuple has two integer value representing the index of the first and last character of the long-form in the text, respectively.
  - id: The unique ID of the sample
- **dev.json**: The development set for acronym extraction task. The samples in `dev.json` have the same attributes as the samples in `train.json`.

## BIO Format

The data is converted to BIO format for the token classification task. We use 5 tokens to reprent each token which have beed described as follows:

| token   | meaning                                      |
| ------- | -------------------------------------------- |
| B-long  | Token at the start of the long form          |
| B-short | Token at the start of the short form         |
| I-long  | Intermediate tokens in long form             |
| I-short | Intermediate tokens in short form            |
| O       | Tokens not part of either short or long form |

To convert the provided data from the original format into the BIO format, use the `data_to_BIO.py` script in the utils folder.

```bash
$ python3 utils/data_to_BIO.py data/english/legal/train.json
```

## Cleaning

The data in BIO format can be cleaned to remove certain symbols and numbers depending on the regex used for pattern matching. This can be used by providing the final directory where the train and dev data for each domain needs to be stored in the final CoNLL format to the `cleaner.py` file the utils folder.

```bash
$ python3 utils/cleaner.py <output_dir>
```

This inherenlty calls the `dataset_generator.py` and the `dataset_reformatter.py` files to convert the data to CSV files and then finally store the data in the scibert_sduai folder in the files train.txt and dev.txt which contain the data in the CoNLL format which is provided to the model for running.

# Code

## Baseline

In order to familiarize the participants with this task, the organizers of the shared task provide a rule-based model in the `code` directory called `baseline.py`. In this baseline, the words that more than 60% of their characters are uppercased are selected as acronym. To select long-forms, if the initial characters of the preceeding words before an acronym can form the acronym they are selected as long-form. To run this model, use the following command:

```bash
$ python3 code/baseline.py -input <path/to/input.json> -output <path/to/output.json>
```

For the original baseline, the original data format needs to be provided as mentioned in the Dataset section. As most of the work is being done in the BIO format for token classification, we have also provided the baseline used in the last years shared task which is called `baseline_prev.py`.

```bash
$ python3 code/baseline_prev.py -input <path/to/bio_format_input.json> -output <path/to/bio_format_output.json>
```

## Our Model

# Evaluation

To evaluate and score the predictions on the original data format, run the following command:

```bash
$ python3 scorer.py -g <path/to/original_gold.json> -p <path/to/predictions.json>
```

Here the gold path contains the true values such as `data/english/dev.json` and the predictions contain the predictions generated by the model.  
The official evaluation metrics are the macro-averaged precision, recall and F1 for acronym and long-form predictions. For verbose evaluation (including the micro-averaged precision, recall and F1 and also short form and long form scores seperatedly), use the following command:

```bash
$ python3 scorer.py -g <path/to/original_gold.json> -p <path/to/predictions.json> - v
```

If the predictions have to be evaluated using the BIO format then use the `scorer_prev.py` directly. Both the scorers evaluate on the same metric and prevent the added error when converting from BIO format back to the original data format in index format. The command to run

```bash
$ python3 scorer_prev.py -g <path/to/bio_gold.json> -p <path/to/predictions.json> - v
```

Here the gold path contains the true values such as `data/english/scidr_dev.json`

## Different Model Precictions Visualizer

To see the difference in the predictions b/w 2 models and for predictions analysis and evaluation, we use the `models_visualizer.py` file. It takes 2 input csv files which formed by the merging the input_data.json file with the predictions.json file. This merging can be done using the `data_output_merger.py` file as follows:

```bash
$ python3 utils/data_output_merger.py <dir_name>
```

`dir_name` is the directory where the input and output resides. It is generally the folder created using the `cleaner.py` file.

After creating the csv files for the models we wish to compare we can visualize the difference in the predictions of each model as follows:

```bash
$ python3 utils/models_visualizer.py <model_1_csv> <model_2_csv>
```
