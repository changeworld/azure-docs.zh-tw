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
ms.openlocfilehash: df511e79b73256833ec54c5906bb6acbc852bc46
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739615"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>教學課程：訓練您的第一個機器學習模型 (第 3 部分，共 4 個部分)

本教學課程說明如何在 Azure Machine Learning 中訓練機器學習模型。

本教學課程是 *四部分教學課程系列的第 3 部分*，您會在其中了解 Azure Machine Learning 的基本概念，以及在 Azure 中完成作業型機器學習工作。 本教學課程建基於您在系列[第 1 部分：設定](tutorial-1st-experiment-sdk-setup-local.md)和[第 2 部分：執行 "Hello world!"](tutorial-1st-experiment-hello-world.md) 完成的工作而建置的。

在本教學課程中，您會透過提交可訓練機器學習模型的指令碼來進行下一步。 此範例將協助您了解 Azure Machine Learning 如何在本機偵錯和遠端回合之間，輕鬆地達成一致的行為。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立定型指令碼。
> * 使用 Conda 來定義 Azure Machine Learning 環境。
> * 建立控制指令碼。
> * 了解 Azure Machine Learning 類別 (`Environment`、`Run`、`Metrics`)。
> * 提交並執行您的訓練指令碼。
> * 在雲端中檢視程式碼輸出。
> * 將計量記錄到 Azure Machine Learning。
> * 在雲端中檢視您的計量。

## <a name="prerequisites"></a>Prerequisites

* 完成系列的[第 2 部分](tutorial-1st-experiment-hello-world.md)。
* Python 語言和機器學習工作流程的簡介知識。
* Visual Studio Code、Jupyter 或 PyCharm 等本機開發環境。
* Python (版本 3.5 至 3.7)。

## <a name="create-training-scripts"></a>建立訓練指令碼

首先，您會在 `model.py` 檔案中定義神經網路架構。 您的所有訓練程式碼都會進入 `src` 子目錄，包括 `model.py`。

下列程式碼取自 PyTorch 的[此簡介範例](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)。 請注意，Azure Machine Learning 的概念適用於任何機器學習服務程式碼，而不只是 PyTorch。

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

接下來，您會定義訓練指令碼。 此指令碼會使用 PyTorch `torchvision.dataset` API 來下載 CIFAR10 資料集、設定 `model.py` 中定義的網路，以及使用標準 SGD 和交叉熵損失，針對兩個 epoch 來進行訓練。

在 `src` 子目錄中建立 `train.py` 指令碼：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

您現在具有下列目錄結構：

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

> [!div class="nextstepaction"]
> [我已建立訓練指令碼](?success=create-scripts#environment) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=create-scripts)

## <a name="create-a-python-environment"></a><a name="environment"></a> 建立 Python 環境

基於示範目的，我們即將使用 Conda 環境。 (Pip 虛擬環境的步驟幾乎完全相同。)

在 `.azureml` 隱藏目錄中，建立稱為 `pytorch-env.yml` 的檔案：

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

此環境具有您模型和訓練指令碼所需的所有相依性。 請注意，Azure Machine Learning SDK for Python 上沒有相依性。

> [!div class="nextstepaction"]
> [我已建立環境檔案](?success=create-env-file#test-local) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> 本機測試

使用下列程式碼，在此環境中測試您在本機執行的程式碼：

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

執行此指令碼之後，您會看到資料下載到名為 `tutorial/data` 的目錄中。

> [!div class="nextstepaction"]
> [我已建立環境檔案](?success=test-local#create-local) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> 建立控制項指令碼

下列控制項指令碼與您用來提交 "Hello world!" 的控制項程式碼之間的差異 在於您會加入幾行額外的程式碼來設定環境。

在名為 `04-run-pytorch.py` 的 `tutorial` 目錄中建立新的 Python 檔案：

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>了解程式碼變更

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning 提供[環境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)概念來代表可重現且已建立版本的 Python 環境，用於執行實驗。 從本機 Conda 或 pip 環境中建立環境很容易。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      將環境新增至 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py)。
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [我已建立控制指令碼](?success=control-script#submit)[我遇到問題](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> 將執行提交至 Azure Machine Learning

如果您已切換本機環境，請務必切回已安裝 Azure Machine Learning SDK for Python 的環境。

然後執行：

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> 第一次執行此指令碼時，Azure Machine Learning 會從您的 PyTorch 環境中建立新的 Docker 映像。 整個執行可能需要 5 至 10 分鐘的時間來完成。 
>
> 您可以在 Azure Machine Learning 工作室中看到 Docker 組建記錄檔。 遵循工作室的連結，選取 [輸出 + 記錄] 索引標籤，然後選取 `20_image_build_log.txt`。
>
> 此映像將會在未來的執行中重複使用，使其執行速度變得更快。

建置映像之後，請選取 `70_driver_log.txt` 以查看訓練指令碼的輸出。

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
> 如果您看到錯誤 `Your total snapshot size exceeds the limit`，表示 `data` 目錄位於 `ScriptRunConfig` 所使用的 `source_directory` 值中。
>
> 移動 `src` 外的 `data`。

環境可以利用 `env.register(ws)` 註冊到工作區。 然後，這些環境可以輕鬆地共用、重複使用及設定版本。 環境可讓您輕鬆重現先前的結果，並與您的小組共同作業。

Azure Machine Learning 也會維護策展環境的集合。 這些環境涵蓋了常見的機器學習案例，並由快取的 Docker 映像所支援。 快取的 Docker 映像可讓第一次的遠端執行更快速。

簡單地說，使用已註冊的環境可以節省您的時間！ 如需詳細資訊，請閱讀[如何使用環境](./how-to-use-environments.md)。

> [!div class="nextstepaction"]
> [我已提交執行](?success=test-w-environment#log) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> 記錄訓練計量

現在，您已在 Azure Machine Learning 中進行模型訓練，接著您可以開始追蹤一些效能計量。

目前的訓練指令碼會將計量列印到終端機。 Azure Machine Learning 提供一種機制來記錄具有更多功能的計量。 藉由加入幾行程式碼，您就能夠將 Studio 中的計量視覺化，並比較多個執行之間的計量。

### <a name="modify-trainpy-to-include-logging"></a>修改 `train.py` 以包含記錄

修改您的 `train.py` 指令碼，使其再多包含兩行程式碼：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


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

> [!div class="nextstepaction"]
> [我修改了 train.py ](?success=modify-train#log) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>更新 Conda 環境檔案

`train.py` 指令碼只會在 `azureml.core` 上採用新的相依性。 更新 `pytorch-env.yml` 以反映這種變更：

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [我已更新環境檔案](?success=update-environment#submit-again) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=update-environment)

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> 將執行提交至 Azure Machine Learning
再次提交此指令碼：

```bash
python 04-run-pytorch.py
```

這次當您造訪 Studio 時，請移至 [計量] 索引標籤，您現在可以在此查看模型訓練損失的即時更新！

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="[計量] 索引標籤上的訓練損失圖形。":::

> [!div class="nextstepaction"]
> [我已重新提交執行](?success=resubmit-with-logging#next-steps) [我遇到問題](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>後續步驟

在此課程中，您已從基本的 "Hello world!" 升級 編寫更實際的訓練指令碼，以執行特定的 Python 環境。 您已了解如何透過 Azure Machine Learning 環境，將本機 Conda 環境帶到雲端。 最後，您已了解如何透過幾行程式碼，將計量記錄到 Azure Machine Learning。

還有其他方法可以建立 Azure Machine Learning 環境，包括[從 pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-pip-requirements-name--file-path-)，或[從現有的本機 Conda 環境](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-)。

在下一個課程中，您將了解如何將 CIFAR10 資料集上傳至 Azure，以使用 Azure Machine Learning 中的資料。

> [!div class="nextstepaction"]
> [教學課程：使用您自己的資料](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> 如果您想要在此完成教學課程系列，而不是前往下一個步驟，請記得[清除您的資源](tutorial-1st-experiment-bring-data.md#clean-up-resources)。
