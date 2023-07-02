# Project: Image segmentation

## **Top-level summary**
We were provided 30 satellite pictures of houses. 25 of them had corresponding labels indicating the roofs, and the task was to predict the labels for the other 5. We trained a U-Net architecture on an heavily augmented training set (150 images; the initial sample of 25 images was limiting for the U-Net) using T4 GPUs on Colab, and reached a validation accuracy of 94%. Below, we provide details of the workflow, which are contained in the two notebooks [here](./notebooks/). Using this model, we make predictions for 5 images constituting the test set, and store them [here](./data/predictions/).

## Task Details

> There are 30 satellite pictures of houses and 25 corresponding labels that indicate the roofs. Take those 25 data points and train a neural network on them - you are completely free about the architecture and are of course allowed to use any predefined version of networks, however, you should be able to explain what you are doing - in terms of code as well as in terms of why certain steps are good choices. The preferred language is Python, but you can also use other languages. Please evaluate your network on the 5 remaining test images by making predictions of the roofs - send us the predictions and ideally some comments on what you have been doing. Everything else we will discuss from there. The data can be found at https://dida.do/downloads/dida-test-task.

## Environment

**GPU Training** Google Colab (T4)  
**Conda environment:** For everything else, build the environment file in `./opt` using:
```
conda env create -f ./opt/conda_environment.yml
```

## Workflow

1. **Data wrangling and preprocessing:** 

    - 1.1. **Data loading:** Load training images and labels, and test images  

    - 1.2. **Data Exploration:**  
        - 1.2.1 Visualising the data: look for inconsistencies [some corrupted (?) training images with black spots]  
        - 1.2.2. Data distribution: imbalanced label set [light:dark pixed ~= 1:5]  

    - 1.3. **Data Preprocessing:**  
        - 1.3.1. Resizing images to a consistent resolution: not needed; all images (256x256)
        - 1.3.2. Normalise Pixel values
        - 1.3.3. Data augmentation: This step is important since we have only 25 training samples. This will increase the training size and improve generalisation. The augmentation techniques we use including, flipping, rotation, scaling, translation, shearing and zooming. We also apply them identically to the image and it's corresponding label.  

    - 1.4. **Train-validation split:** 70-30 split  

2. **Data modelling:**  
    - 2.1 **Network Architecture**: We choose a U-Net architecture, a CNN architechture suitable for image segmentation task; takes satellite image as input and generates binary mask where the roofs' boundaries are represented as white pixels (255) and the rest of the image as black (0).
    
    - 2.2. **Model Training**:

        - 2.2.1. Model Details:

            * Optimizer: [Adam](https://keras.io/api/optimizers/adam/), a stochastic gradient descent method that is considered good for boundary detection. Other options are: RMSprop, SGD.
            * Loss: [BinaryCrossentropy](https://keras.io/api/losses/probabilistic_losses/#binarycrossentropy-class), a pixel-wise loss function, which compares the predicted and ground truth boundary masks on a pixel level; useful for boundary detection.
            * Metrics: [BinaryAccuracy](https://keras.io/api/metrics/accuracy_metrics/#binaryaccuracy-class) [or BinaryCrossentropy], since we are comparing against labels which have binary values.


        - 2.2.2 Training Details:

            * Batch size: 32. We started from 2 and increased till be hit memory errors. Statistical fluctuations in the learning cuves might suggest that 32 isn't enough.
            * Epochs: 100. We did not encounter overfitting until this point. Could have gone higher, but seemed enough for this case study.
            - **Insights from learning curves:** 
                * **Validation curve initially flat:** The binary accuracy curve remains flat for the first few epochs before growing, which could indicate that the model is initially struggling to learn and make meaningful predictions, but gradually improves its performance as training progresses. This can be because of several reasons, and we add this to our list of possible follow-up investigations.
                * **Cyclic behaviour:** Some of the reasons might be learning rate, model architecture, data characteristics, insufficient regularisation or overfitting.
    
    - 2.3. **Model Evaluation**: We evaluate the model using the binary accuracy metric on our validation set. Our validation metrics are:
            
            Validation Loss: 0.1360
            Validation Accuracy: 0.9439
    
    - 2.4. **Testing & Post-processing**: We use the final model to predict the boundaries of the 5 test images. We need to perform some post-processing, in order to convert the predictions into binary images


## Directory structure


```bash
>> tree . --gitignore .gitignore

dida-case-study/
├── LICENSE
├── README.md
├── data
│   └── raw
│       ├── dida_test_task.zip
│       ├── test_images
│       ├── train_images
│       └── train_labels
│   ├── predictions
│       ├── test_images
│       ├── test_labels
│   ├── preprocessed
├── notebooks
│   ├── data-modelling.ipynb
│   └── data-wrangling-preprocessing.ipynb
├── opt
│   └── conda_environment.yml
└── .gitignore
```

## Contributors
Abhirup Ghosh | <abhirup.ghosh.184098@gmail.com>

## License
This project is licensed under the [MIT License](./LICENSE).

## Possible Follow-up:
* More data exploration: 
    * calculate statistics that provide insights into pixel intensity distribution [useful for normalised/preprocessing]
    * analyse variations in dataset [differences in roofstyles, building sizes, environmental conditions] that may affect boundary detction; 
    * explore label quality, inconsistencies in labelling, alignment of bounding boxes with the actual roofs, etc.
    * explore effects of black spots in a few training images
    * explore effects of image brightness and roof symmetry: since satellite images are from directly overhead, but for most of the images, the sun is not, one side of the roof is brighter than the other, leading to contrast issues. As a basic approximation, one could use a higher threshold for detecting the bright half of the roof, and use basic spatial inversion to obtain the other half.

* Modelling fine-tuning:
    * Explore why the accuracy curve on the validation set only starts changing after a few epochs when it remains flat. Various factors, like, the initial learning phase, model complexity, lack of appropriate opimisation/regularisation, training set size or learning rate scheduling, can be responsible for such behaviour.

    * Explore cyclic behaviour and initially flat validation curve.

    * Explore higher batch_size/epoches to smooth out the learning curves/when validation/training curves start diverging.

    * Explore more achitectures and/or transfer learning.

