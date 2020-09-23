---
title: 教學課程：開始使用機器學習 - Python
titleSuffix: Azure Machine Learning
description: 在本教學課程中，您將開始使用在您個人開發環境中執行的 Azure Machine Learning Python SDK。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929331"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>教學課程：開始在開發環境中使用 Azure Machine Learning (第 1 部，共 4 部)

在此**四部分教學課程系列**中，您將了解 Azure Machine Learning 的基礎概念，並在 Azure 雲端中完成作業型 Python ML 工作，包括：

1. 設定工作區和您的本機機器學習開發人員環境。
2. 使用 Azure Machine Learning 的 Python SDK 在雲端中執行程式碼。
3. 管理用於模型訓練的 Python 環境。
4. 將資料上傳至 Azure，並在訓練中使用該資料。

在**本教學課程系列的第 1 部分**中，您將會：

> [!div class="checklist"]
> * 安裝 Azure Machine Learning SDK
> * 設定程式碼的目錄結構
> * 建立 Azure Machine Learning 工作區
> * 設定本機開發環境
> * 設定計算叢集

>[!NOTE]
> 本教學課程系列著重於適用於 Python __作業型__機器學習工作的 Azure Machine Learning 概念，而這些工作需要大量計算和/或重現性。 如果您的機器學習工作不符合此設定檔，請使用 [Azure Machine Learning 計算執行個體上的 Jupyter 或 RStudio 功能](tutorial-1st-experiment-sdk-setup.md)來上架至 Azure Machine Learning。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即免費試用 [Azure Machine Learning](https://aka.ms/AMLFree)。
- 熟悉 Python 和 [Machine Learning 概念](concept-azure-machine-learning-architecture.md)。 例如，環境、訓練、評分等等。
- 本機開發環境 - 已安裝 Python 的膝上型電腦和您慣用的 IDE (例如：VSCode、Pycharm、Jupyter 等等)。

## <a name="install-the-azure-machine-learning-sdk"></a>安裝 Azure Machine Learning SDK

在本教學課程中，我們將使用 Azure Machine Learning Python SDK。

您可以使用最熟悉的工具 (例如：Conda、pip 等等) 來設定要在整個教學課程中使用的環境。 透過 pip 將 Azure Machine Learning Python SDK 安裝到環境：

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>建立程式碼的目錄結構
我們建議您為本教學課程設定下列簡單的目錄：

```markdown
tutorial
└──.azureml
```

- **tutorial** (專案的最上層目錄)
- **.azureml**(教學課程的隱藏子目錄)：`.azureml` 目錄會用來儲存 Azure Machine Learning 組態檔。

## <a name="create-an-azure-machine-learning-workspace"></a>建立 Azure Machine Learning 工作區

工作區是 Azure Machine Learning 的最上層資源，而且是用來進行下列作業的集中區域：

- 管理資源，例如計算
- 儲存筆記本、環境、資料集、管線、模型和端點等資產
- 與其他小組成員共同作業

在最上層的父目錄 (`tutorial`) 中新增名為 `01-create-workspace.py` 的新 Python 檔案和下列程式碼。 依您的喜好來調整參數 (名稱、訂用帳戶識別碼、資源群組和[位置](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service))。

您可以在互動式工作階段中或以 Python 檔案形式執行程式碼。

>[!NOTE]
> 使用本機開發環境 (例如膝上型電腦) 時，系統會要求您在第一次執行下列程式碼時，使用「裝置代碼」向您的工作區進行驗證。 遵循畫面上的指示操作。

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

從 `tutorial` 目錄執行此程式碼：

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

執行上述程式碼片段之後，您的資料夾結構看起來會像這樣：

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

`.azureml/config.json` 檔案包含連線到您 Azure Machine Learning 工作區所需的中繼資料，也就是您的訂用帳戶識別碼、資源群組和工作區名稱。 

> [!NOTE]
> `config.json` 的內容並不是祕密，因此分享這些詳細資料完全沒問題。
> 但仍然需要進行驗證，才能與您的 Azure Machine Learning 工作區互動。

## <a name="create-an-azure-machine-learning-compute-cluster"></a>建立 Azure Machine Learning 計算叢集

在名為 `02-create-compute.py` 的 `tutorial` 頂層目錄中建立 Python 指令碼，並填入下列程式碼，以建立會自動調整零到四個節點的 Azure Machine Learning 計算叢集：

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

執行 Python 檔案：

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 建立叢集之後，其中會佈建 0 個節點。 因此，在您提交作業之前，叢集**不會**產生成本。 此叢集會在閒置 2400 秒 (40 分鐘) 後縮小規模。

您的資料夾結構現在看起來會像這樣：

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>後續步驟

在此設定教學課程中，您已經：

- 建立 Azure Machine Learning 工作區
- 設定本機開發環境
- 建立 Azure Machine Learning 計算叢集。

在下一個教學課程中，您會了解將指令碼提交至 Azure Machine Learning 計算叢集的步驟。

> [!div class="nextstepaction"]
> [教學課程：在 Azure 上執行 "Hello World" Python 指令碼](tutorial-1st-experiment-hello-world.md)
