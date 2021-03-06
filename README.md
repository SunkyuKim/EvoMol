# EvoMol

## Installation

EvoMol was designed on Ubuntu (18.04+). Some features might be missing on other systems. Especially, the drawing of exploration trees is currently unavailable on Windows.


To install EvoMol on your system, run the appropriate commands in your terminal. The installation depends on <a href='https://www.anaconda.com/products/individual'>Anaconda</a>.

### Linux

```shell script
$ git clone https://github.com/jules-leguy/EvoMol.git     # Clone EvoMol
$ cd EvoMol                                               # Move into EvoMol directory
$ conda env create -f evomol_env.yml                      # Create conda environment
$ conda activate evomolenv                                # Activate environment
$ python -m pip install .                                 # Install EvoMol
```

### Windows

```shell script
$ git clone https://github.com/jules-leguy/EvoMol.git     # Clone EvoMol
$ cd EvoMol                                               # Move into EvoMol directory
$ conda env create -f evomol_env_windows.yml              # Create conda environment
$ conda activate evomolenv                                # Activate environment
$ python -m pip install .                                 # Install EvoMol
```

## Quickstart

Launching a <a href="https://www.nature.com/articles/nchem.1243">QED</a> optimization for 500 steps. Beware, you need to activate the evomolenv conda environment when you use EvoMol.

```python
from evomol import run_model
run_model({
    "obj_function": "qed",
    "optimization_parameters": {
        "max_steps": 500
    },
    "io_parameters": {
        "model_path": "examples/1_qed"
    },
})
```

## Model parametrization

To run a model, you need to pass a dictionary describing the run to the run_model function. This dictionary can have up 
to 4 entries that are described in this section.

**Default values** are represented in bold.

### Objective function

The ```"obj_function"``` attribute can take the following values. Multi-objective functions can be nested to any depth. 
* Implemented functions: "<a href="https://www.nature.com/articles/nchem.1243">qed</a>", 
 "<a href="https://arxiv.org/abs/1610.02415v2">plogp</a>", 
 "<a href="https://www.nature.com/articles/s41598-019-47148-x">norm_plogp</a>", 
 "<a href="https://jcheminf.biomedcentral.com/articles/10.1186/1758-2946-1-8">sascore</a>", 
 "<a href="https://arxiv.org/abs/1705.10843">norm_sascore</a>", 
 "<a href="https://www.frontiersin.org/articles/10.3389/fchem.2020.00046/full">clscore</a>", "homo", "lumo" (see <a href=https://jcheminf.biomedcentral.com/articles/10.1186/s13321-020-00458-z>EvoMol article<a/>). "entropy_ifg", "entropy_gen_scaffolds", "entropy_shg_1" and "entropy_checkmol" can be used to maximize the entropy of descriptors, respectively using <a href="https://jcheminf.biomedcentral.com/articles/10.1186/s13321-017-0225-z">IFGs </a>, <a href="https://pubs.acs.org/doi/10.1021/jm9602928">Murcko generic scaffolds</a>, level 1 <a href="https://link.springer.com/article/10.1186/s13321-018-0321-8">shingles</a> and <a href="https://homepage.univie.ac.at/norbert.haider/cheminf/cmmm.html">checkmol</a>.
* A custom function evaluating a SMILES. It is also possible to give a tuple (function, string function name).
* A dictionary describing a multi-objective function containing the following entries.
    * ```"type"``` : 
        * "linear_combination" (linear combination of the properties)
        * "product" (product of properties)
        * "sigm_lin", (passing the values of a unique objective through a linear function and a sigmoid function)
        * "product_sigm_lin" (product of the properties after passing a linear function and a sigmoid function).
    * ```"functions"``` : list of functions (string keys describing implemented functions, custom functions or 
    multi-objective functions).
    * Specific to the linear combination.
        * ```"coef"``` : list of coefficients.
    * Specific to the use of sigmoid/linear functions
        * ```"a"``` list of *a* coefficients for the *ax+b* linear function definition.
        * ```"b"``` list of *b* coefficients for the *ax+b* linear function definition.
        * ```"lambda"``` list of *λ* coefficients for the sigmoid function definition.
* ```"guacamol_v2"``` for taking the goal directed <a href="https://pubs.acs.org/doi/10.1021/acs.jcim.8b00839">GuacaMol</a>
 benchmarks.


### Search space

The ```"action_space_parameters"``` attribute can be set with a dictionary containing the following entries.
* ```"atoms"``` : text list of available heavy atoms (**"C,N,O,F,P,S,Cl,Br"**).
* ```"max_heavy_atoms"```: maximum molecular size in terms of number of heavy atoms (**38**).
* ```"substitution"```: whether to use *substitute atom type* action (**True**).
* ```"cut_insert"```: whether to use *cut atom* and *insert carbon atom* actions (**True**).
* ```"move_group"```: whether to use *move group* action (**True**).
* ```"use_rd_filters"```: whether to use the <a href=https://github.com/PatWalters/rd_filters>rd_filter program</a> as a 
quality filter before inserting the mutated individuals in the population (**False**).

### Optimization parameters

The ```"optimization_parameters"``` attribute can be set with a dictionary containing the following entries.
* ```"pop_max_size"``` : maximum population size (**1000**).
* ```"max_steps"``` : number of steps to be run (**1500**).
* ```"k_to_replace"``` : number of individuals replaced at each step (**2**).
* ```"selection"``` : whether the best individuals are selected to be mutated (**"best"**) or they are selected randomly ("random").
* ```"problem_type"``` : whether it is a maximization (**"max"**) or minimization ("min") problem.
* ```"mutation_max_depth"``` : maximum number of successive actions on the molecular graph during a single mutation (**2**).
* ```"mutation_find_improver_tries"``` : maximum number of mutations to find an improver (**50**).
* ```"guacamol_init_top_100"``` : whether to initialize the population with the 100 best scoring individuals of the GuacaMol <a href="https://academic.oup.com/nar/article/45/D1/D945/2605707">ChEMBL</a> subset in case of taking the GuacaMol benchmarks (**False**). The list of SMILES must be given as initial population.
* ```"mutable_init_pop"``` : if True, the individuals of the initial population can be freely mutated. If False, they can be branched but their atoms and bonds cannot be modified (**True**).
* ```"n_max_desc"```: max number of descriptors to be possibly handled when using an evaluator relying on a vector of descriptors such as entropy contribution (**3.000.000**).
* ```"shuffle_init_pop"```: whether to shuffle the smiles at initialization

### Input/Output parameters

The ```"io_parameters"``` attribute can be set with a dictionary containing the following entries.
* ```"model_path"``` : path where to save model's output data (**"EvoMol_model"**).
* ```"smiles_list_init"```: list of SMILES describing the initial population (**None**: interpreting the ```"smiles_list_init_path"``` attribute). Note : not available when taking GuacaMol benchmarks.
* ```"smiles_list_init_path"``` : path where to find the SMILES list text file describing the initial population (**None**: initialization of the population with a single methane molecule).
* ```"external_tabu_list"```: list of SMILES that won't be generated by EvoMol.
* ```"record_history"``` : whether to save exploration tree data. Must be set to True to later draw the exploration tree (**False**).
* ```"record_all_generated_individuals"``` : whether to record the list of all individuals that are generated during the entire execution (not necessarily inserted in the population). Also recording the number of calls to the objective function at the time of insertion.
* ```"save_n_steps"``` : frequency (steps) of saving the data (**100**).
* ```"print_n_steps"``` : frequency (steps) of printing current population statistics (**1**).
* ```"dft_working_dir"``` : path where to save DFT optimization related files (**"/tmp"**).
* ```"dft_cache_files"``` : list of json files containing a cache of previously computed HOMO or LUMO values (**[]**).

## Examples

### Drawing exploration trees

#### Large exploration tree

Performing a <a href="https://www.nature.com/articles/nchem.1243">QED</a> optimization run of 500 steps, while 
recording the exploration data. 

```python
from evomol import run_model

model_path = "examples/2_large_tree"

run_model({
    "obj_function": "qed",
    "optimization_parameters": {
        "max_steps": 500},
    "io_parameters": {
        "model_path": model_path,
        "record_history": True
    }
})
```

Plotting the exploration tree with solutions colored according to their score. Nodes represent solutions. Edges 
represent mutations that lead to an improvement in the population.

```python
from evomol.plot_exploration import exploration_graph
exploration_graph(model_path=model_path, layout="neato")
```

![Large exploration tree](examples/figures/large_expl_tree.png)

#### Detailed exploration tree

Performing the experiment of mutating a fixed core of acetylsalicylic acid to increase its 
<a href="https://www.nature.com/articles/nchem.1243">QED</a> value. 

```python
from evomol import run_model

model_path = "examples/3_detailed_tree"

run_model({
    "obj_function": "qed",
    "optimization_parameters": {
        "max_steps": 10,
        "pop_max_size": 10,
        "k_to_replace": 2,
        "mutable_init_pop": False
    },
    "io_parameters": {
        "model_path": model_path,
        "record_history": True,
        "smiles_list_init_path": "examples/acetylsalicylic_acid.smi"
    }
})
```

Plotting the exploration tree including molecular drawings, scores and action types performed during mutations.
Also plotting a table of molecular drawings.

```python
from evomol.plot_exploration import exploration_graph

exploration_graph(model_path=model_path, layout="dot", draw_actions=True, plot_images=True, draw_scores=True,
                  root_node="O=C(C)Oc1ccccc1C(=O)O", legend_scores_keys_strat=["total"], mol_size=0.3,
                  legend_offset=(-0.007, -0.05), figsize=(20, 20/1.5), legends_font_size=13)
```

![Detailed exploration tree](examples/figures/detailed_expl_tree.png)

<p align="center">
<img src="examples/figures/detailed_mol_table.png" alt="Detailed molecular drawings table" width="600"/>
</p>

### Entropy and multi-objective optimization

Optimizing jointly the <a href="https://www.nature.com/articles/nchem.1243">QED</a> and the entropy of 
<a href="https://jcheminf.biomedcentral.com/articles/10.1186/s13321-017-0225-z">IFGs</a> using a linear combination. The
weights are set respectively to 1 and 10.

```python
from evomol import run_model

model_path = "examples/4_entropy_optimization"

run_model({
    "obj_function": {
        "type": "linear_combination",
        "functions": ["qed", "entropy_ifg"],
        "coef": [1, 10]
    },
    "optimization_parameters": {
        "max_steps": 500,
        "pop_max_size": 1000
    },
    "io_parameters": {
        "model_path": model_path,
        "record_history": True
    },
})
```

Plotting the exploration trees representing the QED values.

```python
from evomol.plot_exploration import exploration_graph
exploration_graph(model_path=model_path, layout="neato", prop_to_study_key="qed")
```

![Detailed exploration tree](examples/figures/large_expl_tree_entropy_multiobj.png)



## Environment variables and data requirements

### CLscore

As the <a href="https://www.frontiersin.org/articles/10.3389/fchem.2020.00046/full"> CLscore </a> is dependent of prior 
data to be computed, EvoMol needs to be given the data location.
To do so, the ```$SHINGLE_LIBS``` environment variable must be set to the location of the shingle_libs folder that can
be downloaded <a href="https://github.com/reymond-group/GDBChEMBL"> here</a>.
 
### DFT and Molecular Mechanics optimization

To perform DFT and Molecular Mechanics computation (necessary for HOMO and LUMO optimization), you need to bind 
<a href="https://gaussian.com/glossary/g09/">Gaussian09</a> and 
<a href="https://jcheminf.biomedcentral.com/articles/10.1186/s13321-019-0372-5">OpenBabel</a> with EvoMol.

To do so, the ```$OPT_LIBS``` variable must point to a folder containing : 
* run.sh : a script launching a DFT optimization with Gaussian09 of the input filepath given as parameter.
* obabel/openbabel-2.4.1 : directory containing an installation of OpenBabel 2.4.1. Make sure to also set OpenBabel's
```$BABEL_DATADIR``` environment variable to ```$OPT_LIBS/obabel/openbabel-2.4.1/data```.

To install OpenBabel, you should compile <a href='https://github.com/openbabel/openbabel/releases/tag/openbabel-2-4-1'> the sources </a> using the <a href='https://open-babel.readthedocs.io/en/latest/Installation/install.html#compiling-open-babel'>official instructions</a>.

### Checkmol descriptor

In order to use the checkmol descriptor for entropy evaluation, the ```$CHECKMOL_EXE``` environment variable must point 
to the executable of the <a href="https://homepage.univie.ac.at/norbert.haider/download/chemistry/checkmol/checkmol.pas">checkmol program</a>.

OpenBabel must also be installed (see above section).

### GuacaMol initial population

To use EvoMol for <a href="https://pubs.acs.org/doi/10.1021/acs.jcim.8b00839">GuacaMol</a> goal directed benchmarks 
optimization using the best scoring molecules from their subset of 
<a href="https://academic.oup.com/nar/article/45/D1/D945/2605707">ChEMBL</a> as initial population, you need to :
* Download the <a href="https://figshare.com/projects/GuacaMol/56639">ChEMBL subset</a>.
* Give the path of the data using the ```"smiles_list_init_path"``` attribute.
* Insure that the ```"guacamol_init_top_100"``` attribute is set to True.

### rd_filters
To use the <a href=https://github.com/PatWalters/rd_filters>rd_filter program</a> as a filter of solutions that can be 
inserted in the population, the ```$FILTER_RULES_DATA``` environment variable must point to a folder containing the 
```rules.json``` and ```alert_collection.csv``` files.

## Citing EvoMol

To reference EvoMol, please cite the following article.

Leguy, J., Cauchy, T., Glavatskikh, M., Duval, B., Da Mota, B. EvoMol: a flexible and interpretable evolutionary 
algorithm for unbiased de novo molecular generation. J Cheminform 12, 55 (2020). 
https://doi.org/10.1186/s13321-020-00458-z
