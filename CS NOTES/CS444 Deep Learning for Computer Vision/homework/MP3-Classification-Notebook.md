# MP3 Classification Notebook

> Notebook converted to Markdown (outputs stripped; see the report PDF for results).

# Creating Kaggle submissions for CIFAR10 classification (Parts 2, 3, and 4b)

```python
import torch
import torchvision
import torchvision.transforms as transforms
import numpy as np


class CIFAR10TestOnly(torchvision.datasets.CIFAR10):

    def __init__(self, root, download, transform) -> None:
        super().__init__(root=root, train=False, download=download, transform=transform)
    
    def __len__(self):
        return len(self.data)
    
    def __getitem__(self, index: int):
        image, cls_label = super().__getitem__(index)

        return image, cls_label

transform_test = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
])

batch_size = 128

testset = CIFAR10TestOnly(root='./data', download=True, transform=transform_test)
testloader = torch.utils.data.DataLoader(testset, batch_size=batch_size,
                                         shuffle=False, num_workers=2)
```

```python
device = 'cuda' if torch.cuda.is_available() else 'cpu'
device
```

```python
@torch.inference_mode()
def run_classification_test(net, testloader):
    all_output = []
    for images, _ in testloader:
        images = images.to(device)
        # calculate outputs by running images through the network
        outputs = net(images)
        # the class with the highest energy is what we choose as prediction
        _, predicted = torch.max(outputs.data, 1)
        all_output.append(predicted.cpu().numpy())
    return np.concatenate(all_output)
```

## Instantiate your model here

```python
import torch.nn as nn
import torch.nn.functional as F

net = torchvision.models.resnet18(num_classes=10)
net = net.to(device)
net.eval()
```

## Load the model weights

```python
net.load_state_dict(torch.load("classification_model.pth"))
```

## Submit the generated csv file to Kaggle

```python
import pandas as pd
predictions = run_classification_test(net, testloader)
ids = np.arange(len(predictions))
df = pd.DataFrame({'id': ids, 'prediction': predictions})
df.to_csv('./classification_test_predictions.csv', index=False)
```
