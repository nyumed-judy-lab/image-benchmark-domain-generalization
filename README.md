# Heckman-domain-generalization
<!-- 
## References
Please refer to the following papers to set hyperparameters and reproduce experiments on the WILDS benchmark.
- [WILDS](https://proceedings.mlr.press/v139/koh21a) paper: Koh, P. W., Sagawa, S., Marklund, H., Xie, S. M., Zhang, M., Balsubramani, A., ... & Liang, P. (2021, July). Wilds: A benchmark of in-the-wild distribution shifts. In International Conference on Machine Learning (pp. 5637-5664). PMLR.
- [HeckmanDG](https://openreview.net/forum?id=fk7RbGibe1) paper: Kahng, H., Do, H., & Zhong, J. Domain Generalization via Heckman-type Selection Models. In The Eleventh International Conference on Learning Representations.

* ONE FILE for all datasets (and the readme.md file)
* What is the input and output of each step?
* What is the shape of the data (image, tabular)
* what functions for what
-->

This repository provides the PyTorch implementation of Heckman DG. We use the one-step optimization to train the Heckman DG model. In the Heckman DG model, the selection (g) and outcome (f) networks are composed of neural networks structures. For tabular datasets, we use the multi-layer NN. For image datasets, we use data-specific convolutional neural networks (CNN) structures recommended by [WILDS](https://proceedings.mlr.press/v139/koh21a) paper. In addition, we follow the hyperparameters of each data and model recommended by [HeckmanDG](https://openreview.net/forum?id=fk7RbGibe1) paper.  

## 1. Installation
Please see [requirements.txt](requirements.txt). It presents the names and versions of all libraries that we have to install before the implementation of this repository. Please note that we mainly use the Pytorch backend libraries as follows:
- torch==1.10.0
- torchaudio==0.10.0
- torchmetrics==0.11.1
- torchvision==0.11.0

```bash
# pip
pip install -r requirements.txt

# conda
conda install --file requirements.txt
```

## 2. Data Preparation
Please put your data in [data](data). If you want to apply **structured (tabular)** data, please put your data in [data](data). If you want to use **WILDS** benchmark, please run the following code to download it on [wilds](data/benchmark/wilds). 

``` bash
# Run download_wilds_data.py
python download_wilds_data.py --root_dir ./data/benchmark/wilds
```

## 3.Experiments
Please go to [main_heckmandg.py](main_heckmandg.py) and run it as follows:

```bash
# Run Heckman DG on your data

python main_heckmandg.py --data_name [your data]
```

The experiment is composed of the following 4 steps; (1) Experiment Settings, (2) Data Preprocessing, (3) Heckman DG, (4) Result Analysis.

**1. Experiment Settings**
- Here, we set the **data_name** (e.g. insight or camelyon17), **data_shape** (tabular or image), and hyperparameters. You can set hyperparameters with arguments named **args** consisting of the learning rate, weight decay, and optimizer. Please note that recommended data-specific hyperparameters are already set for the WILDS benchmark, so if you want to see results with other settings, please modify the **args** variable in the [main_heckmandg.py](main_heckmandg.py). 


 ```python
def args_cameloyn17(args, experiment_name):
    args.data = 'camelyon17'
    args.backbone = 'densenet121'
    args.experiment_name = experiment_name
    args.batch_size = 32
    args.eval_batch_size = 32
    args.epochs = 5
    args.optimizer = 'sgd'
    args.lr = 0.001
    args.weight_decay = 0.00001 
    args.pretrained = True 
    args.device = 'cuda'
    return args
```

**2. Data Preprocessing**
This repository provides HeckmanDG for two data types including (1) tabular, (2) image data.
**Tabular data** re

**Shape of the structured data (tabular): (# observations, # variables)**
```bash
# This is the example of tabular data (# observation: 3, # variables: 3)
[[10, 10, 10],
[10, 10, 10],
[10, 10, 10],]
```



**Image data** refers to damage data refers to data that is represented in the form of images or pictures. Images are made up of pixels, each of which has a numerical value that represents its color or intensity. Image data is structured in a 2D or 3D format, with pixels arranged in rows and columns. The **2D format** refers to an image that has two dimensions: (height and width). These dimensions define the size and shape of the image, and the pixels that make up the image are arranged in a rectangular grid with rows and columns. The **3D format**, on the other hand, refers to an image that has three dimensions: height, width, and depth (the number of channels; e.g. 3 if the channels are composed of Red, Green, and Blue). 

Shape of the unstructured data (image): (# observations, # channels, width, height) and each image has the shape of the (#channels, width, height). We use Pytorch **data_loader** that can put subset (minibatch) of data to the model in the training process, so the data shape would be (# batch_size, # channels, width, height).


**IMAGE**: The WILDS data basically require a large computing memory for the training step. If you want to test this code with the smaller size of data (subsets of the original data), please add (or uncomment) the following code at lines 50 to 54.

The **args** contains the name of data, backbone, and hyperparameters (learning rate, etc.). For the WILDS data, the data-specific arguments are already set.

### **Preprocessing of Tabular data**
This repository provides the functions that can perform the standardization and the imputation. Standardization makes the mean and standard deviation of data to 0 and 1. The missing value imputation replance 

- standard normalization: '
- missing value imputation:

* In the experiment of this reprository, for the normalization and imputatation, the **Scaler** and **Imputer** is fitted on training data, and transform training, validatoin, and testing data. 

- To input the your own tabular data, please call the function **StandardScaler** to normlize the data and call the function **SimpleImputer(strategy='mean')** to missing value imputation (you can change the stratefy 'median', 'most_frequent', 'constant'). 
    - If “mean”, then replace missing values using the mean along each column. Can only be used with numeric data.
    - If “median”, then replace missing values using the median along each column. Can only be used with numeric data.
    - If “most_frequent”, then replace missing using the most frequent value along each column. Can be used with strings or numeric data. If there is more than one such value, only the smallest is returned.
    - If “constant”, then replace missing values with fill_value. Can be used with strings or numeric data.




### **Preprocessing of Image (WILDS benchmark) data** 

**Data Normalizstion**
ImageNet

**Data Augmentation**

**Three prediction tasks**: (1) binary classification (Camelyon17), (2) multicalss classification(iWildCam, RxRx1), and regression (PovertyMap), on WILDS benchmark data as follows:
- Camelyon17: Binary (tumor) classification.
- iWildCam: multiclass (animal species) classification. (In preparaion)
- RxRx1: multiclass (genetic treatments) classification. (In preparaion)
- PovertyMap: Regression (wealth index prediction). (In preparaion)

In addition, this repository provides the data-specific normalization and augmentation functions as follows:
- Camelyon17: N/A
- PovertyMap: Color jittering
- iWildCam: RandAugment
- RxRx1: RandAugment

### WILDS benchmark

Summary on the four datasets from WILDS benchmark. 
![image](https://user-images.githubusercontent.com/36376255/226856940-2cca2f56-abee-46fa-9ec9-f187c6ac290b.png)

<img width="837" alt="hyperparameters" src="https://user-images.githubusercontent.com/36376255/229375372-3b3bd721-b5f2-405a-9f5e-02966dc20cd6.png">

This figure represent hyperparameters of two-step optimization of the Heckman DG. Cells with two entries denote that we used different values for training domain selection and outcome models. In this repository, for the one-step optimization, we followed the hyperparameters of the outcome model.

#### With your own data

- To input your own image data, you need to customize the preprocessing code in the 


**3. HeckmanDG**
- Here, we initialize the neural networks (NNs) and run the Heckman DG model. We use deep neural networks (DNNs; a.k.a. multi-layer perceptron) and convolutional nerual netoworks (CNNs) for the tabular data and image data, respectively.

##### Tabular Data: **HeckmanDNN** 
For the tabular data, you need to import the **HeckmanDNN** function. The **HeckmanDNN** contains selection model (g_layers) and outcome model (f_layers), so please put the number of node and layers to construct them. This is an example of the **HeckmanDNN**.

```python
network = HeckmanDNN([tr_x.shape[1], 128, 64, 32, 1], 
                     [tr_x.shape[1], 64, 32, 16, args.num_domains], 
                     dropout=0.5, 
                     batchnorm=True, 
                     activation='ReLU')
```

 - **Tabular data** is a specific form of structured data that is organized into rows and columns, like a spreadsheet. Each row in a table represents a single record, while each column represents a specific attribute or variable associated with that record.



##### Image Data: **HeckmanCNN**. 
For the image data, you need to import the **HeckmanCNN**. The function of **HeckmanCNN** contains various CNN structures. The input of this **HeckmanCNN** is the arguments named **args**. 


**4. Result Analysis**
- The results of this code are as follows:
  - plots of the training loss [learning curve](results/plots/HeckmanDG_camelyon17_loss.pdf)
  - plots of the probits [histogram](results/plots/HeckmanDG_camelyon17_probits.pdf)
  - AUC scores [prediction results](results/prediction/HeckmanDG_camelyon17.csv)
 
 ##### what is the training, valid data, loss axis, etc.
 
- From the function of **plots_loss**, we can see the [learning curve](results/plots/HeckmanDG_camelyon17_loss.pdf) as follows:

![image](https://user-images.githubusercontent.com/36376255/229378713-62511fcb-be4a-4973-a6e5-21029765d3fa.png)

The x-axis represent the epoch and y-axes are loss, auroc, and rho trajectory in the training process.

<!-- [loss_curves](https://user-images.githubu![loss_curves](https://user-images.githubusercontent.com/36376255/229378641-c3c8804e-da57-469e-8a57-8478ad4e6af2.png)
![auroc_curves](https://user-images.githubusercontent.com/36376255/229378658-9153dd42-6985-430e-a09b-b1d36d920c08.png)
![rho_curves](https://user-images.githubusercontent.com/36376255/229378644-0e9a3a00-9bde-4de4-a2ff-b173e163e8f2.png)
sercontent.com/36376255/229377704-2cca889b-b7d0-4a81-ad7f-4d7654853fb0.png) -->

- From the function of **plots_probit**, we can see the [histogram](results/plots/HeckmanDG_camelyon17_probits.pdf) that represents the distribution of probits for each domain. To yeild the probits, we first need to model.get_selection_probit!

![image](https://user-images.githubusercontent.com/36376255/229378704-24477849-d9ce-49c7-bf0a-97724fcd7c81.png)


plots_loss(model, args, domain_color_map, path=f"./results/plots/HeckmanDG_{args.data}_loss.pdf")
probits, labels = model.get_selection_probit(train_loader)
plots_probit(probits, labels, args, domain_color_map, path=f"./results/plots/HeckmanDG_{args.data}_probits.pdf")

