# MLFPM - ESR9 - Deliverable 1.16

Project Organization
------------

    ├── environment.yml              <- The environment file
    │
    ├── LICENSE
    │
    ├── README.md                    <- The top-level README for developers using this project.
    │
    ├── data
    │   ├── external                 <- Data from third party sources.
    │   ├── processed                <- The final, canonical data sets for modeling.
    │   └── raw                      <- The original, immutable data dump.
    │
    ├── models                       <- Trained models, model predictions, or model summaries
    │   ├── NN                       <- training and testing models
    │   └── CV                       <- cross-validation performance result
    │
    ├── notebooks                    <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                                   the creator's initials, and a short `-` delimited description, 
    │                                   e.g. `1.0-jqp-initial-data-exploration`
    │
    ├── references                   <- Data dictionaries, manuals, and all other explanatory materials
    │
    ├── reports                      <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   ├── activation               <- pathways activation results
    │   ├── CV                       <- the results of cross-validation performance
    │   ├── encoding                 <- the results of ecoding infomation
    │   ├── figures                  <- Generated graphics and figures to be used in reporting
    │   └── retrieval                <- the results of retrieval performance
    │
    └── scripts
        ├── data-pbk                 <- prior biological knowledge from hipathia
        ├── config.py                <- the main script file
        ├── dataset_scripts.py       <- scripts for dataset modificiation
        ├── model_scripts.py         <- scripts for getting tarined model
        ├── nn_desing_scripts.py     <- scripts for proposed NN
        ├── path_scripts.py          <- scripts for location 
        └── visualization_scripts.py <- scripts for visualization
    

--------

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
