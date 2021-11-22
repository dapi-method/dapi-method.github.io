# DAPI
This website holds data and information to reproduce the results presented in the publication `Discriminative Attribution from Paired Images` (DAPI). For the projects repository, click [here](https://github.com/dapi-method/dapi.git). 

# Installation
```
git clone https://github.com/dapi-method/dapi.git
cd dapi
conda create --name dapi --file conda_requirements.txt
conda activate dapi
pip install -r pip_requirements.txt
pip install .
```

# DAPI Data
Download DAPI Data [here](https://www.dropbox.com/sh/gpcjilkwlybp3qn/AACRqQbVolS1g56GwAWLVtlpa?dl=0).

## Contents:
  - Raw datasets, including the three novel ones: SYNAPSES, DISC_A & DISC_B.
    ```
    data/raw/<experiment>
    ```
  - Cycle GAN translated and filtered datasets.
    ```
    data/translated/<experiment>
    ```
  - Checkpoints for all networks.
    ```
    checkpoints/<experiment>
    ```
  - Results from attribution runs.
    ```
    results/<experiment>_211121
    ```

## Instructions
### Run attribution & mask extraction on already translated images

```
python dapi/scripts/run_dapi.py --config experiments/configs/<experiment>.ini --net <net>
```

For experiment in {synapses, mnist, disc_a, disc_b, horses_zebras, apples_oranges, summer_winter}, net in {VGG, RES}. Results will be stored in 
```
experiments/results/<experiment>
```

Default behaviour is to run the script locally using 20 workers. This can be changed via editing 
the submit command and num_workers in the respective experiment config file at:
```
dapi/configs/<experiment>.ini
```

### Visualize and calculate DAPI scores

```
python dapi/scripts/plot_dapi.py --result_dir dapi_data/results/<experiment>/<net> --experiment <experiment> --net <net>
```
Plots will be stored at 
```
./dapi_plot_<experiment>_<net>.png
```
AUC scores for each method shown in console.
   

### Train cycle GANs and generate translated datasets from scratch
- Create paired dataset:
```
cd dapi
python cycle_gan/prepare_dataset.py --data_dir ~/dapi_data/data/raw/<experiment> --classes <class names>
```
E.g. for disc_a:
```
python cycle_gan/prepare_dataset.py --data_dir ~/dapi_data/data/raw/disc_a --classes 0 1
```
- Start training:
```
python cycle_gan/start_training.py --experiment <experiment> --data_root ~/dapi_data/data/raw/<experiment>/cycle_gan
```

- Translate images:
```
python cycle_gan/start_testing.py --experiment <experiment> --data_root ~/dapi_data/data/raw/<experiment>/cycle_gan --aux_net <vgg/res> --aux_checkpoint ~/dapi_data/checkpoints/<experiment>/classifiers/<vgg/res>_checkpoint --gan_checkpoint_dir <path to gan_checkpoint_dir>
```

