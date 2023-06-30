# Project overview

# Task Details

> There are 30 satellite pictures of houses and 25 corresponding labels that indicate the roofs. Take those 25 data points and train a neural network on them - you are completely free about the architecture and are of course allowed to use any predefined version of networks, however, you should be able to explain what you are doing - in terms of code as well as in terms of why certain steps are good choices. The preferred language is Python, but you can also use other languages. Please evaluate your network on the 5 remaining test images by making predictions of the roofs - send us the predictions and ideally some comments on what you have been doing. Everything else we will discuss from there. The data can be found at https://dida.do/downloads/dida-test-task.

# Workflow

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
├── docs
│   └── overview.md
├── notebooks
│   ├── data-modelling.ipynb
│   └── data-wrangling-preprocessing.ipynb
├── opt
│   └── conda_environment.yml
└── src
.gitignore
```

## Contributors
Abhirup Ghosh, <abhirup.ghosh.184098@gmail.com>



## License
This project is licensed under the [MIT License](./LICENSE).

# Environment

```
./opt/conda_environment.yml
```

# Action Items 🚨🚨🚨🚨
* make a final version of environment.yml when everything's done
* Modelling:
    * model documentation
    * model steps
* final directory structure