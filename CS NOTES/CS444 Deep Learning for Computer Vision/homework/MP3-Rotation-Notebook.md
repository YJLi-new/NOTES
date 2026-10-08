# MP3 Rotation Notebook

> Notebook converted to Markdown (outputs stripped; see the report PDF for results).

# Creating Kaggle submissions for CIFAR10 rotation classification (Parts 1/4a)

```python
import torch
import torchvision
import torchvision.transforms as transforms
import numpy as np


def rotate_img(img, rot):
    """
    Implement the same rotation logic as in your code
    """
    if rot == 0: # 0 degrees rotation
        return img
    # TODO: Implement rotate_img() - return the rotated img
    elif rot == 1:
        return transforms.functional.rotate(img, 90, expand=True)
    elif rot == 2:
        return transforms.functional.rotate(img, 180, expand=True)
    elif rot == 3:
        return transforms.functional.rotate(img, 270, expand=True)
                                            
    else:
        raise ValueError('rotation should be 0, 90, 180, or 270 degrees')


class CIFAR10RotationTestOnly(torchvision.datasets.CIFAR10):

    def __init__(self, root, download, transform) -> None:
        super().__init__(root=root, train=False, download=download, transform=transform)
    
    def __len__(self):
        return len(self.data)
    
    def __getitem__(self, index: int):
        image, cls_label = super().__getitem__(index)
        image_rotated = rotate_img(image, index % 4)

        return image_rotated

transform_test = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
])

batch_size = 128

testset = CIFAR10RotationTestOnly(root='./data', download=True, transform=transform_test)
testloader = torch.utils.data.DataLoader(testset, batch_size=batch_size,
                                         shuffle=False, num_workers=2)
```

```python
device = 'cuda' if torch.cuda.is_available() else 'cpu'
device
```

```python
@torch.inference_mode()
def run_rotation_test(net, testloader):
    all_output = []
    for images in testloader:
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

net = torchvision.models.resnet18(num_classes=4)
net = net.to(device)
net.eval()
```

## Load the model weights

```python
net.load_state_dict(torch.load("rot_model.pth"))
```

## Submit the generated csv file to Kaggle

```python
import pandas as pd
predictions = run_rotation_test(net, testloader)
ids = np.arange(len(predictions))
df = pd.DataFrame({'id': ids, 'prediction': predictions})
df.to_csv('./rotation_test_predictions.csv', index=False)
```
