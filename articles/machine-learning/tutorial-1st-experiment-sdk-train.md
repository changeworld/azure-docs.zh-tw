---
title: 教學課程：訓練您的第一個機器學習模型 - Python
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 入門系列的第 3 部分，說明如何訓練機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a267231dd447b114c69e6ead20c8ab5252f85d0e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896724"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>教學課程：訓練您的第一個機器學習模型 (第 3 部，共 4 部)

本教學課程說明如何在 Azure Machine Learning 中訓練機器學習模型。

本教學課程是**四部分教學課程系列的第三部分**，您會在其中了解 Azure Machine Learning 的基本概念，以及在 Azure 中完成作業型機器學習工作。 本教學課程建基於此系列的[第 1 部分：設定](tutorial-1st-experiment-sdk-setup-local.md)和[第 2 部分：執行 "Hello World"](tutorial-1st-experiment-hello-world.md)。

在本教學課程中，您會透過提交可訓練機器學習模型的指令碼來進行下一步。 此範例將協助您了解 Azure Machine Learning 如何在本機偵錯和遠端回合之間，輕鬆地達成一致的行為。

在本教學課程中，您會：

> [!div class="checklist"]
> * 建立訓練指令碼。
> * 使用 Conda 來定義 Azure Machine Learning 環境。
> * 建立控制指令碼。
> * 了解 Azure Machine Learning 類別 (環境、執行、計量)。
> * 提交並執行您的訓練指令碼。
> * 在雲端中檢視程式碼輸出。
> * 將計量記錄到 Azure Machine Learning。
> * 在雲端中檢視您的計量。

## <a name="prerequisites"></a>Prerequisites

* 如果您還沒有 Azure Machine Learning 工作區，請完成[第 1 部分](tutorial-1st-experiment-sdk-setup-local.md)。
* Python 語言和機器學習工作流程的簡介知識。
* 本機開發環境。 這包括 (但不限於) Visual Studio Code、Jupyter 或 PyCharm。
* Python (版本 3.5-3.7)。

## <a name="create-training-scripts"></a>建立訓練指令碼

首先，您會在 `model.py` 檔案中定義神經網路架構。 您的所有訓練程式碼都會進入 `src` 子目錄，包括 `model.py`。

下列程式碼取自 PyTorch 的[此簡介範例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)。 請注意，Azure Machine Learning 的概念適用於任何機器學習服務程式碼，而不只是 PyTorch。

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

接下來，您會定義訓練指令碼。 此指令碼會使用 PyTorch `torchvision.dataset` API 來下載 CIFAR10 資料集、設定 `model.py` 中定義的網路，以及使用標準 SGD 和交叉熵損失，針對兩個 epoch 來進行訓練。

在 `src` 子目錄中建立 `train.py` 指令碼：

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

您現在有如下所述的目錄結構：

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="define-a-python-environment"></a>建立 Python 環境

基於示範目的，我們將使用 Conda 環境 (pip 虛擬環境的步驟幾乎完全相同)。

在 `.azureml` 隱藏目錄中，建立稱為 `pytorch-env.yml` 的檔案：

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

此環境具有您模型和訓練指令碼所需的所有相依性。 請注意，Azure Machine Learning Python SDK 上沒有相依性。

## <a name="test-locally"></a>本機測試

若要在本機測試您的指令碼執行，使用此環境搭配：

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env             # activate conda environment
python src/train.py                    # train model
```

執行此指令碼之後，您會看到資料下載到名為 `tutorial/data` 的目錄中。

## <a name="create-the-control-script"></a>建立控制項指令碼

下列控制項指令碼與用來提交 "Hello World" 的指令碼不同，差異在於，您可以加入幾行額外的程式碼來設定環境。

在名為 `04-run-pytorch.py` 的 `tutorial` 目錄中建立新的 Python 檔案：

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>了解程式碼變更

:::row:::
   :::column span="":::
      `env = Environment.from_conda_specification( ... )`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning 提供[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true)概念來代表可重現且已建立版本的 Python 環境，用於執行實驗。 從本機 Conda 或 pip 環境中建立環境很容易。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      將環境新增至 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)。
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>提交回合至 Azure Machine Learning

在您切換本機環境時，請務必切換回已安裝並執行 Azure Machine Learning Python SDK 的環境：

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> 第一次執行此指令碼時，Azure Machine Learning 會從您的 PyTorch 環境中建立新的 Docker 映像。 整個執行可能需要 5-10 分鐘的時間才能完成。 您可以在 Azure Machine Learning Studio 中看到 Docker 組建記錄檔：遵循 Machine Learning Studio 的連結 > 選取 [輸出 + 記錄] 索引標籤 > 選取 [`20_image_build_log.txt`]。
此映像將會在未來的執行中重複使用，使其執行速度變得更快。

建立映像之後，請選取 [`70_driver_log.txt`] 以查看訓練指令碼的輸出。

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> 如果您看到 `Your total snapshot size exceeds the limit` 錯誤，表示 `data` 目錄位於 `ScriptRunConfig` 所使用的 `source_directory` 中。
> 請務必將 `data` 移至 `src` 以外的地方。

環境可以透過 `env.register(ws)` 註冊到工作區，讓其輕鬆地共用、重複使用及建立版本。 環境可讓您輕鬆重現先前的結果，並與您的小組共同作業。

Azure Machine Learning 也會維護策展環境的集合。 這些環境涵蓋了常見的機器學習案例，並由快取的 Docker 映像所支援。 快取的 Docker 映像可讓第一次的遠端執行更快速。

簡單地說，使用已註冊的環境可以節省您的時間！ 如需更多詳細資料，請參閱[環境文件](./how-to-use-environments.md)

## <a name="log-training-metrics"></a>記錄訓練計量

現在，您已在 Azure Machine Learning 中進行模型訓練，接著您可以開始追蹤一些效能計量。
目前的訓練指令碼會將計量列印到終端機。 Azure Machine Learning 提供一種機制來記錄具有更多功能的計量。 藉由加入幾行程式碼，您就能夠將 Studio 中的計量視覺化，並比較多個執行之間的計量。

### <a name="modify-trainpy-to-include-logging"></a>修改 `train.py` 以包含記錄

修改您的 `train.py` 指令碼，使其包含額外兩行程式碼：

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=torchvision.transforms.ToTensor())
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>了解額外的兩行程式碼

在 `train.py` 中，您可以使用 `Run.get_context()` 方法，在訓練指令碼本身「內」存取回合物件，並用其來記錄計量：

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

Azure Machine Learning 中的計量有以下特點：

- 透過實驗和回合進行整理，讓您可以輕鬆地追蹤和比較計量。
- 搭配 UI，讓您可以在 Studio 中將訓練效能視覺化。
- 專為擴充而設計，因此您可以在執行數百個實驗後，依然保有這些優點。

### <a name="update-the-conda-environment-file"></a>更新 Conda 環境檔案

`train.py` 指令碼只會在 `azureml.core` 上採用新的相依性。 更新 `pytorch-env.yml` 以反映這種變更：

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-run-to-azure-machine-learning"></a>提交回合至 Azure Machine Learning
再次提交此指令碼：

```bash
python 04-run-pytorch.py
```

這次當您造訪 Studio 時，請移至 [計量] 索引標籤，您現在可以在此查看模型訓練損失的即時更新！

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="[計量] 索引標籤中的訓練損失圖形":::

## <a name="next-steps"></a>後續步驟

在此課程中，您已從基本的 "Hello world!" 升級 編寫更實際的訓練指令碼，以執行特定的 Python 環境。 您已了解如何透過 Azure Machine Learning 環境，將本機 Conda 環境帶到雲端。 最後，您已了解如何透過幾行程式碼，將計量記錄到 Azure Machine Learning。

還有其他方法可以建立 Azure Machine Learning 環境，包括[從 pip requirements.txt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-)，甚至[從現有的本機 Conda 環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-)。

在下一個課程中，您將了解如何將 CIFAR10 資料集上傳至 Azure，以使用 Azure Machine Learning 中的資料。

> [!div class="nextstepaction"]
> [教學課程：使用您自己的資料](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> 如果您想要在此完成教學課程系列，而不是前往下一個步驟，請記得[清除您的資源](tutorial-1st-experiment-bring-data.md#clean-up-resources)