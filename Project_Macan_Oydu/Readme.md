Efficient Model-Driven Network for Shadow Removal

This readme file is an outcome of the [CENG501 (Spring 2022)](https://ceng.metu.edu.tr/~skalkan/DL/) project for reproducing a paper without an implementation. See [CENG501 (Spring 2022) Project List](https://github.com/CENG501-Projects/CENG501-Spring2022) for a complete list of all paper reproduction projects.

# 1. Introduction

Shadow removol is one of the most important preprocessing application for the computer vision studies.  It brings up challanges for tracking and object detection. Recently developed shadow removel methodologies has problems of ignoring the spatially varient property of shadow images, lacking interpretability of CNN structures and not using masking information in the dataset efficiently resulting in ligthing the nonshadow dark albedo material areas. Our project paper, Efficient Model-Driven Network for Shadow Removal by Zhu et al., proposes deep network combines both model-driven and data-driven CNN-based approaches for shadow removal to overcome aforementioned problems. 


## 1.1. Paper summary

In the paper, drawbacks of currently available shadow removel approaches are shown such as taking shadow effects uniform. To overcome this problem, an illumination transformation matrix is proposed such that non-shadow pıxel values are equal to its value. By this way spatially variant property of shadow images are considered. Apart from that, by introducing model-driven neural network, inerpreability of the network is increased.  Different from using Residual Block proposed, Dynamic Mapping Residual Block is designed as a basic module for the introduced network which increases performance of the model without introducing any parameters.

# 2. The method and my interpretation

## 2.1. The original method

@TODO: Explain the original method.

Shadow removing model constructed by using masking information and proposed illumination transfomation mapping. By introducing constraints on this mapping, shadow regions' information is recovered and information of non-shadow regions are preserved. Then, by bayesian recursion, where image with shadow is prior and no shadow image is posterior, variational model is constructed. Illustration of the overall model is provided in the paper as down below.

![image](https://user-images.githubusercontent.com/108632459/177399131-59faaa8a-0ba9-429c-93ef-af0e550836a9.png)


There are two CNN's which are Na and Ninit. This networks have a structure of modified U-net inspired from ResNet and Mobilenet-v2. Basic blocks for both networks presented in paper as such.

![image](https://user-images.githubusercontent.com/108632459/177399425-6d4317f2-ada4-4fe8-9df4-ed69ca6e5bb7.png)

Iterative algorithm for the overall model provided in the paper as down below.

![image](https://user-images.githubusercontent.com/108632459/177399514-a443c4ba-003e-4042-8164-fe47fa4f100b.png)

For the loss function, MSE is used.

## 2.2. My interpretation 

As explained in the paper, scales of the original UNet are replaced with depth convolution blocks given in the figure 4 of the paper. Since expansion coefficients of the depth convolutions are not given in the paper, we took expansion coefficients as 4 and 6 (same as in ResNet and Mobilenetv2 respectively) in "NetworkA" and "NetworkInit" respectively. Size of these convolutions are taken as 3x3 same as in ResNet and Mobilenetv2. Also, it is mentioned that "NetworkA" and "NetworkInit" involves 4 scales and have channels from the 1st to 4th scale 32, 64, 28, 256 respectively. We assumed that after 4th scale, there are 512 channels. 

Same hyperparameters are used for our model. Mean squared error is used as loss function and implemented as given in the paper. Same as in the paper, Adam optimizer and Cosine Annealing scheduler are used during training. However, maximum number of iterations (T_max) for Cosine Annealing scheduler is not mentioned in the paper. Therefore, we took it same as number of epochs (150).

# 3. Experiments and results

## 3.1. Experimental setup

In the paper, it is denoted that training is conducted by using a single NVIDIA GTX 1080Ti graphics card which has 11 GB VRAM. Since we had NVIDIA RTX 3070Ti graphics card which has only 8 GB VRAM, instead of training with images has a resolution of 256x256 as it has been done in the paper, we trained our network with images with resolution of 128x128. Then we streched the outputs to 256x256. Apart from that, it is mentioned that model converges well after 150 epochs and we took that number as reference for our training stage. It is stated that maximum number of iteration is set to 4 as is it trade-off between accuracy and speed. In that respect we took that value as reference and used that value in our implementation. We used ISTD dataset for training the model.

## 3.2. Running the code

Our trained model, structure of the network and other script that are used for training and testing are provided in the "codes" file. As explained in the  paper, "ShadowRemoverNetwork" consists of 2 subnetworks which are "NetworkA" and "NetworkInit". Implementation of these networks are given in "NetworkInit.py" and "NetworkA.py" scripts. The final model is given in the "ShadowRemoverNetwork.py" script. To make our lifes easier, we constructed another subnetwork named "NetworkA_iter" given in the "NetworkA.py" script. "NetworkA_iter" is written for constructing the "for" loop in the "ShadowRemoverNetwork" in a more understandable way. "dataset.py" contains "dataset" class that is used for reading images. Training algorithm and loss function are implemented in the "train.py". One can use "test.py" to test our trained model. "errorCalculator.py" is used to calculate root mean square error and peak signal noise ratio of the test images.

## 3.3. Results

@TODO: Present your results and compare them to the original paper. Please number your figures & tables as if this is a paper.

# 4. Conclusion

@TODO: Discuss the paper in relation to the results in the paper and your results.

It is observed that, with proposed illumination transformation mappping, shadow are removed with more realistic modelling. Obtained shadow free image results shows that this statement is true. Since we did not implement other shadow removel approaches, we cannot make any comment on relative performance of the model presented in the paper compared to other. However, as shown in the paper, ONUR YAPACAK

However, we observed low performance while model tries to remove shadows which are located in colourful areas such as this one. 

### INSERT RESULT HERE ####

This situation is not presented in the paper, so we dont know whether it is caused by our implementation or model itself.

# 5. References

Zhu, Y., Xiao, Z., Fang, Y., Fu, X., Xiong, Z., & Zha, Z.-J. (2022). Efficient Model-Driven Network for Shadow Removal. Proceedings of the AAAI Conference on Artificial Intelligence, 36(3), 3635-3643.

Ronneberger, O.; Fischer, P.; and Brox, T. 2015. U-net: Convolutional networks for biomedical image segmentation. In MICCAI. Springer.

He, K.; Zhang, X.; Ren, S.; and Sun, J. 2016. Deep residual learning for image recognition. In CVPR.


Sandler, M.; Howard, A.; Zhu, M.; Zhmoginov, A.; and Chen, L.-C. 2018. Mobilenetv2: Inverted residuals and linear bottlenecks. In CVPR.


# Contact

Onuralp Maçan - onuralp.macan@metu.edu.tr

Onur Oydu - onur.oydu@metu.edu.tr