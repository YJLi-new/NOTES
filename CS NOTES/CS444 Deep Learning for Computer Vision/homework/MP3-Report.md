This submission is with an approved extension

Assignment 3

Name(s): Yanjin Li

NetID(s): yanjin2

Team name on Kaggle leaderboard: Yanjin Li

For each of the sections below, your reported test accuracy should approximately match the accuracy reported on Kaggle. It is fine to have a small deviation. Report both accuracies if they are different.

## Self-supervised Learning on CIFAR10

**1) Rotation training**

*Report the hyperparameters you used to train your model. Discuss any particular implementation choices which caused significant performance increases.*

lr=0.001, 45 epochs, batch size 128

Adam optimizer with lr=0.001 caused significant performance increases

**2) Fine-tuning late layers**

*Report the hyperparameters you used to fine-tune your model. Compare the performance between pre-trained model and randomly initialized model.*

lr=0.0005

Pre-Trained Model: acc 67%

Randomly Initialized Model: acc 47%

Pre-Trained Model is better.

**3) Fully supervised learning**

*Report the hyperparameters you used to fine-tune your model. Compare the performance between pre-trained model and randomly initialized model. Discuss anything you find interesting comparing fine-tuning the late layers only in section (2) and fine-tuning the whole model in section (3).*

lr=0.0005, 20 epochs

Pre-Trained Model: acc 83%

Randomly Initialized Model: acc 82%

The two perform similarly.

**4) With a more advanced architecture**

*Report the details of your architecture and the hyperparameters used to train this model. Compare the performance between this model and the models in sections (1) and (3).*

**5) Extra credit**
