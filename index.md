## PROJECT STRUCTURE


```markdown
├── environment.yml              <- The environment file
│
├── LICENSE
│
├── README.md                    <- The top-level README for developers using this project.
│
├── data			 <- IT DOES NOT CONTAIN ANY FILES, PLEASE CHECK THE REFERENCE 
│				    PAPER TO UPLOAD DATASET
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
```
------------------------
<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
=================================================

## PROJECT STEPS and USAGE EXAMPLES

In this part, the steps of the project is shared. 

### A. EXPORTING SIGNALING PATHWAY LIST

The project uses signaling pathway information. This information is obtaining from R via Hipathia package. To be able to obtain this required list, please applying following steps,

```
Note, the project is executing for two organisms, which are homo-sapiens(hsa) and mus-musculus(mmu). To be able to use correct pathway list, please specify **organism name** and **package** as followed,

- hsa, org.Hs.eg.db for homo-sapiend (human)
- mmu, org.Mm.eg.db for mus-musculus (mouse)
```

```sh
echo "1. Exporting signaling pathway information from hipathia --- scripts/pathway_layer_data/1.1-pg-pathway-from-hipathia.r executing"
Rscript scripts/pathway_layer_data/1.1-pg-pathway-from-hipathia.r -sp hsa -src hipathia

echo "2. Processing the pathway list, removing disease related pathways --- scripts/pathway_layer_data/1.2-pg-remove-disease-cancer.py executing..."
python scripts/pathway_layer_data/1.2-pg-remove-disease-cancer.py -sp {organism_name} -src hipathia

echo "3. Exporting gene list based on processed pathway list in 1.2-pg-remove-disease-cancer.py --> scripts/pathway_layer_data/1.3-pg-gene-from-hipathia.r executing..."
Rscript scripts/pathway_layer_data/1.3-pg-gene-from-hipathia.r -sp {organism_name} -src hipathia

echo "4. Converting entrex id value into gene symbol -->  scripts/pathway_layer_data/1.4-pg-gene-id-entrez-converter.r executed!!"
Rscript scripts/pathway_layer_data/1.4-pg-gene-id-entrez-converter.r -sp {organism_name} -src hipathia -ga {package}

echo "5. Creating prior biological knowledge information to include the nn design in first hidden layer --> scripts/pathway_layer_data/1.5-pg-creating-biological-layer.py executing..."
python scripts/pathway_layer_data/1.5-pg-creating-biological-layer.py -sp {organism_name} -src hipathia

```

### B. PREPROCESSING DATASET

Following steps export processed version of each dataset. The dataset information are shared in reference paper.

```sh
echo "PREPROCESSING of EXPERIMENT MELANOMA DATASET"
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_melanoma \
    -ds reference.pck \
    -sw False \
    -gw False \
    -sc log1p \
    -tci -1 \
    -ofn reference
    
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_melanoma \
    -ds query.pck \
    -sw False \
    -gw False \
    -sc log1p \
    -tci -1 \
    -ofn query

echo "PREPROCESSING of EXPERIMENT MOUSE DATASET"
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_mouse \
    -ds 1-3_integrated_NNtraining.pck \
    -sw True \
    -gw True \
    -sc None \
    -tci 0 \
    -ofn mouse_learning
    
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_mouse \
    -ds 3-33_integrated_retrieval_set.pck \
    -sw True \
    -gw True \
    -sc None \
    -tci 0 \
    -ofn mouse_retrieval
    
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_mouse \
    -ds 3-33_integrated_retrieval_set_signaling.pck \
    -sw True \
    -gw True \
    -sc None \
    -tci 0 \
    -ofn mouse_retrieval_signaling

echo "PREPROCESSING of EXPERIMENT PBMC DATASET"
python notebooks/2.0-pg-preprocessing-dataset.py \
    -exp exper_pbmc \
    -ds Immune.pck \
    -sw True \
    -gw False \
    -sc log1p \
    -tci -1 \
    -ofn pbmc
```

#### Usage example,
> To use required scRNA-seq dataset,

```sh
$ python notebooks/2.0-pg-preprocessing-dataset.py -exp {EXPERIMENT NAME}
                                                   -ds  {DATASET NAME} 
                                                   -sc  {SCALER, StandardScaler(ss), MinMaxScaker(mms), Log1p} 
                                                   -tci {TARGET COLUMN INDEX}
                                                   -ofn {OUTPUT FILE NAME}

```

### C. EXECUTING EXPERIMENT

The following steps are belong to PBMC experiment with 1- and 2-layer designs. The experiment uses **RepeatedStratifiedKFold** cross-validation technique to evaluate the performance of the proposed network.

```sh
############### a.IMMUNE EXPERIMENT (START) ###############
echo "IMMUNE EXPERIMENT"
analysis_var='evaluate_rskf'
ds_var='processed/exper_immune/immune_new.pck'

# 1-LAYER SIGNALING
python notebooks/4.0-pg-model.py \
    -design ${pbk_info}_1_layer \
    -first_hidden_layer_pbk $pbk_var \
    -first_hidden_layer_dense 0 \
    -second_hidden_layer False \
    -optimizer $optimizer_var \
    -activation $activation_var \
    -ds $ds_var \
    -analysis $analysis_var \
    -filter_gene_space $filter_space \
    -hp_tuning $tuning 
    
# 2-LAYER SIGNALING
python notebooks/4.0-pg-model.py \
    -design ${pbk_info}_2_layer \
    -first_hidden_layer_pbk $pbk_var \
    -first_hidden_layer_dense 0 \
    -second_hidden_layer True \
    -optimizer $optimizer_var \
    -activation $activation_var \
    -ds $ds_var \
    -analysis $analysis_var \
    -filter_gene_space $filter_space \
    -hp_tuning $tuning

```

#### Usage example,

```sh
$ python notebooks/04-pg-model-training.py  -design                   {DESIGN NAME}
                                            -first_hidden_layer_pbk   {BIOLOGICAL KNOWLEDGE}
                                            -first_hidden_layer_dense {NUMBER of DENSE LAYER}
                                            -second_hidden_layer      {SECOND HIDDEN LAYER}
                                            -optimizer                {OPTIMIZER}
                                            -activation               {ACTIVATION}
                                            -ds                       {DATASET PATH}
                                            -analysis                 {THE NAME of ANALYSIS}
                                            -filtering_gene_space     {GENE SPACE FILTERING}
                                            -hp_tuning                {HYPERPARAMETER TUNING}
```
