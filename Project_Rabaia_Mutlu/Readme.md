# From Label Smoothing to Label Relaxation

This readme file is an outcome of the [CENG501 (Spring 2022)](https://ceng.metu.edu.tr/~skalkan/DL/) project for reproducing a paper without an implementation. See [CENG501 (Spring 2022) Project List](https://github.com/CENG501-Projects/CENG501-Spring2022) for a complete list of all paper reproduction projects.

# 1. Introduction

In this report, the implementation of the study "From Label Smoothing to Label Relaxation, AAAI 2021" is presented. In the study, a new loss function called "Label Relaxation" is proposed as an alternative to the conventional "Label Smoothing" loss, where it is performed on several networks using different datasets. Our goal is to create our own algorithm and reproduce the presented results.   

## 1.1. Paper summary

This paper summerizes the derivation of the explicit form of the new proposed loss function. It is fundamantally based on comparing probabilistic predictions of targets with a group of possible distributions. In other words, the learner is set free to select the most appropriate probabilistic distribution of the targets among of a likelihood of probabilistics (based the Kullback-Leibler divergence) unlike label smoothing (LS) which restrects the trainer to one probability as shown below (left-side: Label smoothing, right-side: Label Relaxation).  

![image](https://user-images.githubusercontent.com/108774445/177706624-7dc35b52-3a3b-4495-8121-ec999b5912a6.png)

Therfore, a closed form of the introduced loss function is proposed as:

![image](https://user-images.githubusercontent.com/108774445/177691233-4ebdbea1-a33c-4f26-a68e-3e4dfcc015c3.png)

To prove the accuracy of the mentioned loss, a comparison with other loss functions such as label smoothing, cross entrophy (CE), confidence penalizing (CP), and focal loss (FL) is made. Hence, various datasets and learning architectures were also employed within the scope of the study.


# 2. The method and our interpretation

## 2.1. The original method

The original method is mainly based on classification problem in which the trainer optimizes the best probabilistic distribution represented by a parameter (alpha), which is usually considered fixed in label smoothing. Therefore, it is included finetuned besides other hyperparameters such as learning rate, regularization, and weight decay, which are determined based on both cross-validation accuracy and error.    

## 2.2. Our interpretation 

To evaluate the degree of calibration of the networks, the study adapts techniques such as the expected calibration error (ECE) as done by (Guo et al. 2017) and temperature scaling for comparing it with label smoothing. However, as the core idea of these concepts were difficult to understand, it required further reading and research.   

# 3. Experiments and results

## 3.1. Experimental setup

MNIST, Fashion-MNIST, CIFAR10, and CIFAR100 datasets are used in this study. MNIST and Fashion-MNIST datasets are used for 2-layer dense architecture, and the rest is used for VGG16, ResNet56(v2) and DenseNet-BC(100-12) networks.
We imported the datasets using “Keras” and 1/6 of the training set was seperately hold out as a validation set for finetuning using "train_test_split". Then, we performed preprocessing by subtracting the mean from the inputs. The batch size is taken as 64 as stated in the study. We used Pytorch's Cross Entropy loss and implemented the Label Smoothing and Label Relaxation losses. In training phase, a code piece is taken from PyTorch examples and modified for our case.
We obtained results for 2-layer architecture using MNIST and Fashion-MNIST datasets. Moreover, architecture for VGG16 is also prepared but the results could not be presented due to lack of time and computational power.

The finetuning of hyperparameters are done in accordance with initial values and ranges specified in the study considering explicit calibration methods, non-calibration, and temperature scaling as baselines of our comparisons. Subsequently, the final results are obtained by training the model for the best hyperparameters using the full training set and then tested the final trained model with the test set.   

## 3.2. Running the code

In the uploaded file, a comprehensive google-colab script containing all the used packages and libraries to perform the analyses is provided. Nevertheless, we divided the script into seperate sequential blocks containing the created and used functions with explicit step-by-step explanations for each one. Moreover, we specify the sources of some parts that are adapted from other examples/codes.   

## 3.3. Results

The results of the implementation and the original results are presented in the following tables. Table 1 and Table 2 contain results that are optimized for the accuracy, whereas Table 3 and Table 4 are optimized for ECE.


Table 1: Implemented Results on MNIST and Fashion-MNIST (Opt. for acc.)
|                    |  **MNIST**  |  **MNIST**   |**Fashion-MNIST**|**Fashion-MNIST**|
|--------------------|-------------|--------------|-----------------|-----------------|
|     **Loss**       |  **Acc.**   |   **ECE**    |    **Acc.**     |     **ECE**     |
|     CE (α=0)       |   0.983     |    0.016     |      0.875      |      0.094      |
|LS (α opt. for acc.)|   0.984     |    0.036     |      0.898      |      0.014      |
|LR (α opt. for acc.)|   0.977     |    0.009     |      0.901      |      0.029      |


Table 2: Original Results on MNIST and Fashion-MNIST (Opt. for acc.)
|                    |  **MNIST**  |  **MNIST**   |**Fashion-MNIST**|**Fashion-MNIST**|
|--------------------|-------------|--------------|-----------------|-----------------|
|     **Loss**       |  **Acc.**   |   **ECE**    |    **Acc.**     |     **ECE**     |
|     CE (α=0)       |   0.985     |    0.010     |      0.912      |      0.129      |
|LS (α opt. for acc.)|   0.988     |    0.106     |      0.915      |      0.155      |
|LR (α opt. for acc.)|   0.985     |    0.007     |      0.912      |      0.059      |
--------------------------------------------------

Table 3: Implemented Results on MNIST and Fashion-MNIST (Opt. for ECE)
|                   |  **MNIST**  |  **MNIST**   |**Fashion-MNIST**|**Fashion-MNIST**|
|-------------------|-------------|--------------|-----------------|-----------------|
|     **Loss**      |  **Acc.**   |   **ECE**    |    **Acc.**     |     **ECE**     |
|  CE (α=0, T opt.) |   0.976     |    0.024     |      0.894      |      0.095      |
|LS (α opt. for ECE)|   0.985     |    0.016     |      0.875      |      0.024      |
|LR (α opt. for ECE)|   0.980     |    0.003     |      0.887      |      0.111      |


Table 4: Original Results on MNIST and Fashion-MNIST (Opt. for ECE)
|                   |  **MNIST**  |  **MNIST**   |**Fashion-MNIST**|**Fashion-MNIST**|
|-------------------|-------------|--------------|-----------------|-----------------|
|     **Loss**      |  **Acc.**   |   **ECE**    |    **Acc.**     |     **ECE**     |
|  CE (α=0, T opt.) |   0.983     |    0.003     |     0.908       |      0.030      |
|LS (α opt. for ECE)|   0.987     |    0.014     |     0.915       |      0.016      |
|LR (α opt. for ECE)|   0.985     |    0.003     |     0.911       |      0.015      |

# 4. Conclusion

In this report, the implementation of the abovementioned study was done using two layer dense network trained on two different datasets (MNIST and Fashion-MNIST), where various calibration techniques were employed. Consequently, two tables of the presented original results where reproduced. As it could be noticed from the above tables, the reproduced results are very close to the original ones. Especially, for MNIST dataset. Moreover, the same trend as that of the original results is observed. However, the ensued slight difference could be attributed to the fact that the original results where produced by repeating the analyses for many times whereas our results represent a single run. Indeed, different adaption of some parameters such as weight decay, regularization, and learning rate schedule might affect the results. Therefore, it can be said that the method has been applied successfully. However, all analyses could not be performed due to the lack of time and the long duration of the analyses.

# 5. References

- Guo, C.; Pleiss, G.; Sun, Y.; and Weinberger, K. Q. 2017. On calibration of modern neural networks. In Proceedings of the 34th International Conference on Machine Learning, ICML 2017, Sydney, NSW, Australia, August 6-11, 2017, volume 70 of Proceedings of Machine Learning Research, 1321–1330. PMLR.

- Lienen, J.; Hüllermeier, E. 2021. From Label Smoothing to Label Relaxation. Proceedings of the AAAI Conference on Artificial Intelligence, 35(10), 8583-8591. Retrieved from https://ojs.aaai.org/index.php/AAAI/article/view/17041

# Contact

- Sezer Mutlu: szrmutlu@gmail.com
- Tareq Rabaia: tareqrabai3a@gmail.com
