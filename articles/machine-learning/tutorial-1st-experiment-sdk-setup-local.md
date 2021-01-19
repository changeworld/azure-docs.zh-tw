---
title: 教學課程：開始使用機器學習 - Python
titleSuffix: Azure Machine Learning
description: 在本教學課程中，您將開始使用在您個人開發環境中執行的 Azure Machine Learning SDK for Python。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2f33fe4fafbe194238fcfbd4942807ed2fc4d6ff
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183535"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>教學課程：開始在開發環境中使用 Azure Machine Learning (第 1 部分，共 4 部分)

在此「四部分教學課程系列」中，您將了解 Azure Machine Learning 的基礎概念，並在 Azure 雲端平台上完成作業型 Python 教學課程工作。 

在本教學課程系列的第 1 部分中，您將會：

> [!div class="checklist"]
> * 安裝 Azure Machine Learning SDK。
> * 設定程式碼的目錄結構。
> * 建立 Azure Machine Learning 工作區。
> * 設定本機開發環境。
> * 設定計算叢集。

> [!NOTE]
> 本教學課程系列著重提交 **批次工作** 所需的 Azure Machine Learning 概念，也就是將程式碼提交至雲端以在背景中執行，而不需要任何使用者互動。 這適用於想要重複執行的指令碼或程式碼，或用於計算密集型機器學習工作的作業。 如果您對探索性工作流程比較有興趣，則可以改為[在 Azure Machine Learning 計算執行個體上使用 Jupyter 或 RStudio](tutorial-1st-experiment-sdk-setup.md)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 嘗試 [Azure Machine Learning](https://aka.ms/AMLFree)。
- 管理 Python 虛擬環境及安裝套件的 [Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://www.anaconda.com/download/)。

## <a name="install-the-azure-machine-learning-sdk"></a>安裝 Azure Machine Learning SDK

在本教學課程中，我們將使用 Azure Machine Learning SDK for Python。 為了避免發生 Python 相依性問題，您將建立隔離的環境。 本教學課程系列使用 Conda 來建立該環境。 如果要使用其他解決方案，例如 `venv`、`virtualenv` 或 Docker，請確定您使用的是3.5 以上但低於 3.9 的 Python 版本。

檢查系統上是否已安裝 Conda：
    
```bash
conda --version
```
    
如果此命令傳回 `conda not found` 錯誤，請[下載並安裝 Miniconda](https://docs.conda.io/en/latest/miniconda.html)。 

安裝 Conda 後，請使用終端機或 Anaconda 提示視窗建立新的環境：

```bash
conda create -n tutorial python=3.8
```

接下來，將 Azure Machine Learning SDK 安裝到您所建立的 Conda 環境中：

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Azure Machine Learning SDK 需要大約 2 分鐘才能完成安裝。
>
> 如果遇到逾時錯誤，請改為嘗試 `pip install --default-timeout=100 azureml-core`。


> [!div class="nextstepaction"]
> [我已安裝 SDK](?success=install-sdk#dir) [我遇到問題](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>建立程式碼的目錄結構

我們建議您為本教學課程設定下列簡單的目錄結構：

```markdown
tutorial
└──.azureml
```

- `tutorial`：專案的最上層目錄。
- `.azureml`：用來儲存 Azure Machine Learning 組態檔的隱藏子目錄。

> [!TIP]
> 您可以在終端機視窗中建立隱藏的 .azureml 子目錄。  或使用下列方式：
>
> * 在 Mac 的 Finder 視窗中，使用 **Command + Shift + .** 切換功能以查看並建立以點 (.) 開頭的目錄。  
> * 在 Windows 10 檔案總管中，請參閱 [如何檢視隱藏的檔案和資料夾](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5)。 
> * 在 Linux 圖形化介面中，使用 **Ctrl + h** 或 [檢視] 功能表，然後勾選 [顯示隱藏的檔案] 方塊。

> [!div class="nextstepaction"]
> [我已建立目錄](?success=create-dir#workspace) [我遇到問題](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>建立 Azure Machine Learning 工作區

工作區是 Azure Machine Learning 的最上層資源，而且是用來進行下列作業的集中區域：

- 管理資源，例如計算。
- 儲存筆記本、環境、資料集、管線、模型和端點等資產。
- 與其他小組成員共同作業。

在頂層目錄中，`tutorial`，使用下列程式碼新增名為 `01-create-workspace.py` 的新 Python 檔案。 依您的喜好來調整參數 (名稱、訂用帳戶識別碼、資源群組和[位置](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service))。

您可以在互動式工作階段中或以 Python 檔案形式執行程式碼。

>[!NOTE]
> 使用本機開發環境 (例如您的電腦) 時，系統會要求您在第一次執行下列程式碼時，使用「裝置代碼」向您的工作區進行驗證。 遵循畫面上的指示操作。

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

在已啟用 tutorial1 Conda 環境的視窗中，從 `tutorial` 目錄執行此程式碼。

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> 如果執行此程式碼會顯示無法存取訂用帳戶的錯誤，請參閱[建立工作區](how-to-manage-workspace.md?tab=python#create-multi-tenant)以取得驗證選項的相關資訊。


成功執行 *01-create-workspace.py* 之後，您的資料夾結構看起來會像這樣：

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

檔案 `.azureml/config.json` 包含連線到您 Azure Machine Learning 工作區所需的中繼資料。 亦即，其包含您的訂用帳戶識別碼、資源群組和工作區名稱。 

> [!NOTE]
> `config.json` 的內容不是秘密。 共用這些詳細資料並無大礙。
>
> 但仍然需要進行驗證，才能與您的 Azure Machine Learning 工作區互動。

> [!div class="nextstepaction"]
> [我已建立工作區](?success=create-workspace#cluster) [我遇到問題](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> 建立 Azure Machine Learning 計算叢集

在 `tutorial` 的頂層目錄中，建立名為 `02-create-compute.py` 的 Python 指令碼。 填入下列程式碼，以建立會自動調整零到四個節點的 Azure Machine Learning 計算叢集：

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

在已啟用 tutorial1 Conda 環境的視窗中，執行 Python 檔案：

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 建立叢集時，其中會佈建 0 個節點。 在您提交作業之前，叢集「不會」產生成本。 此叢集會在閒置 2,400 秒 (40 分鐘) 後縮小規模。

您的資料夾結構現在看起來會像這樣：

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

> [!div class="nextstepaction"]
> [我已建立計算叢集](?success=create-compute-cluster#next-steps) [我遇到問題](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>在 Studio 中檢視

登入 [Azure Machine Learning Studio](https://ml.azure.com)，檢視您所建立的工作區和計算執行個體。

1. 選取用來建立工作區的 [訂用帳戶]。
1. 選取您建立的 **Machine Learning 工作區**，tutorial-ws。
1. 載入工作區後，在左側選取 [計算]。
1. 在頂端，選取 [計算叢集] 索引標籤。

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="螢幕擷取畫面：在工作區中檢視計算執行個體。":::

此檢視會顯示已佈建的計算叢集，以及閒置節點的數目、忙碌節點，以及取消佈建的節點。  由於您尚未使用叢集，因此目前已取消佈建所有節點。

## <a name="next-steps"></a>後續步驟

在此設定教學課程中，您已經：

- 建立 Azure Machine Learning 工作區。
- 設定本機開發環境。
- 建立 Azure Machine Learning 計算叢集。

在本教學課程的其他部分中，您將了解：

* 第 2 部分： 使用 Azure Machine Learning SDK for Python 在雲端中執行程式碼。
* 第 3 部分： 管理用於模型訓練的 Python 環境。
* 第 4 部分： 將資料上傳至 Azure，並在訓練中使用該資料。

繼續進行下一個教學課程，以了解將指令碼提交至 Azure Machine Learning 計算叢集的步驟。

> [!div class="nextstepaction"]
> [教學課程：執行 "Hello world!"Azure 上的 Python 指令碼](tutorial-1st-experiment-hello-world.md)
