Explainable Global Fairness Verification of Tree-Based Classifiers
===============================

This code is for the SaTML 2023 full paper [Explainable Global Fairness Verification of Tree-Based Classifiers](https://doi.org/10.1109/SaTML54575.2023.00011).

Abstract
---
We present a new approach to the global fairness verification of tree-based classifiers. Given a tree-based classifier and a set of sensitive features potentially leading to discrimination, our analysis synthesizes sufficient conditions for fairness, expressed as a set of traditional propositional logic formulas, which are readily understandable by human experts. The verified fairness guarantees are global, in that the formulas predicate over all the possible inputs of the classifier, rather than just a few specific test instances. Our analysis is formally proved both sound and complete. Experimental results on public datasets show that the analysis is precise, explainable to human experts and efficient enough for practical adoption.


Installation
---

Download the repository. Remember to compile using the flags <em>-Iinclude</em> and <em>-lpthread</em> to use the synthesiser.

Requirements
---

### Libraries
To use the analyzer, you need the following requirements: specific versions of the libraries for reproducibility purposes.
- Python3
- C++14 compiler
- sklearn (version 0.23.2 used for the experiments)
- pandas (version 1.1.3 used for the experiments)
- numpy (version 1.19.1 used for the experiments)
- boost C++ libraries
  
### Data-Independent Stability Analysis
The resilience-verification repository at [https://github.com/FedericoMarcuzzi/resilience-verification](https://github.com/FedericoMarcuzzi/resilience-verification) contains the instructions to execute the data-independent stability analysis on the tree-based classifier.
This repo currently contains the essential code to perform the data-independent stability analysis. 

Usage
---

### Apply pre-processing to datasets

The synthesizer requires the dataset to be pre-proc by applying 0-1 normalization and one-hot encoding to the categorical features.
If you want to use datasets not provided by this repo, have a look at the pre-processing functions contained in <em>./data_gen/misc.py</em> and in the datasets folder to understand which files must be generated (names are self-explanatory).

For what concerns the datasets used in the experimental evaluation in the paper, execute the command inside the <em>data_gen</em> folder:
```bash
python3 preproc_<dataset_name>.py
```

### Generate decision tree ensembles with the supported format
After the dataset pre-processing, execute the following command inside the <em>data_gen</em> folder:
```bash
python3 generate_data.py <dataset_abbreviation> 1 <random_seed>
```
By using this command, the pre-proc dataset is split into training and test sets.

Supported dataset abbreviations are "ad" (adult), "gm" (german), and "ht" (health). Add our abbreviations in <em>generate_data.py</em> if you want to support other datasets.

Then, execute the following command from the data_gen folder to generate a decision tree-based ensemble in the supported format:

```bash
python3 generate_data.py <dataset_abbreviation> 0 <random_seed> <n_trees> <max_depth>
```

### Use the synthesizer

Compile the synthesiser using the following string:
```bash
g++ -o synthesizer ./src/exec_analyser_synthesiser.cpp -Iinclude -lpthread
```

The program requires these arguments:
```bash
./synthesizer <ensemble_filename.json> <columns_filename.json> <categorical_columns_names.json> <numerical_columns_indexes.json> <categorical_columns_indexes.json> <protected_attribute> <n_iter_analyze> <n_threads> <n_threads_filtering> <filename_dump_hyperrects.json> <test_set_filename.json> <n_iter_synthesiser> <output_conditions_filename_base.json> <normalizations_columns_conditions.json>
```
The specific parameters are:
- <em>ensemble_filename.json</em>: the JSON file containing the structure of the tree-based classifier.
- <em>columns_filename.json</em>: the JSON file containing all the names of the features.
- <em>categorical_columns_names.json</em>: the JSON file containing the names of the categorical features.
- <em>numerical_columns_indexes.json</em>: the JSON file containing the indexes of the numerical features.
- <em>categorical_columns_indexes.json</em>: the JSON file containing the indexes of the categorical features.
- <em>protected_attribute</em>: name of the sensitive attribute.
- <em>n_iter_analyze > 0</em>: number of iterations performed by the data-independent stability analyzer.
- <em>n_threads > 1</em>: number of threads used by the data-independent stability analyzer.
- <em>filename_dump_hyperrects.json</em>: name of the file in which the hyper-rectangles generated by the DISA will be dumped.
- <em>n_iter_synthesiser > 0</em>: number of iterations performed by the synthesizer.
- <em>output_conditions_filename_base</em>: prefix of the name of the files that will contain the generated conditions separated by their complexity.
- <em>normalizations_columns_conditions.json</em>: a file containing minimum, maximum, mean, and std for any feature.

#### Example
```bash
./synthesizer ./adult/models/rf_ad_5_6_7.json ./adult/ad_column_names.json ./adult/ad_categorical_column_names.json ./adult/ad_numerical_binary_column_index.json ./adult/ad_categorical_column_index.json sex_male 40 1 ./res/adult/hypers/hypers_ad_5_6_7_100iterA 4 ./res/adult/fair_conditions/fair_conditions_ad_5_6_7_100iterA_1threadF ./adult/ad_normalization_info.json
```

## Credits

If you use this implementation in your work, please add a reference/citation to our paper. You can use the following BibTeX entry:

```
@inproceedings{10136173,
  author={Calzavara, Stefano and Cazzaro, Lorenzo and Lucchese, Claudio and Marcuzzi, Federico},
  booktitle={2023 IEEE Conference on Secure and Trustworthy Machine Learning (SaTML)}, 
  title={Explainable Global Fairness Verification of Tree-Based Classifiers}, 
  year={2023},
  volume={},
  number={},
  pages={1-17},
  keywords={Sufficient conditions;Machine learning;Testing;n/a},
  doi={10.1109/SaTML54575.2023.00011}}
```

Support
---
If you have questions about the code or how to use it, feel free to email us at lorenzo.cazzaro@unive.it or federico.marcuzzi@unive.it.
