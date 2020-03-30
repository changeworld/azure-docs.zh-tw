---
title: 用於 Azure 機器學習的 Git 集成
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習如何與本地 Git 存儲庫集成。 從 Git 存儲庫的本地目錄提交訓練運行時，有關存儲庫、分支和當前提交的資訊將作為運行的一部分進行跟蹤。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402830"
---
# <a name="git-integration-for-azure-machine-learning"></a>用於 Azure 機器學習的 Git 集成

[Git](https://git-scm.com/)是一個流行的版本控制系統，允許您共用和協作您的專案。 

Azure 機器學習完全支援 Git 存儲庫以進行跟蹤工作 - 您可以將存儲庫直接克隆到共用工作區檔案系統上，在本地工作站上使用 Git，或者從 CI/CD 管道中使用 Git。

將作業提交到 Azure 機器學習時，如果原始檔案存儲在本地 git 存儲庫中，則有關存儲庫的資訊將作為培訓過程的一部分進行跟蹤。

由於 Azure 機器學習跟蹤來自本地 git 存儲庫的資訊，因此不會將其綁定到任何特定的中央存儲庫。 可以從 GitHub、GitLab、Bitbucket、Azure DevOps 或任何其他與 git 相容的服務克隆存儲庫。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>將 Git 存儲庫克隆到工作區檔案系統中
Azure 機器學習為工作區中的所有使用者提供了共用檔案系統。
要將 Git 存儲庫克隆到此檔共用中，我們建議您創建計算實例&打開終端。
打開終端後，您可以訪問完整的 Git 用戶端，並可通過 Git CLI 體驗克隆和使用 Git。

我們建議您將存儲庫克隆到使用者目錄中，以便其他人不會直接在您的工作分支上發生衝突。

您可以克隆可以驗證的任何 Git 存儲庫（GitHub、Azure 存儲庫、Bit Bucket 等）

有關如何使用 Git CLI 的指南，[請閱讀此處](https://guides.github.com/introduction/git-handbook/)。

## <a name="track-code-that-comes-from-git-repositories"></a>跟蹤來自 Git 存儲庫的代碼

當您提交從 Python SDK 或機器學習 CLI 運行的培訓時，訓練模型所需的檔將上載到工作區。 如果該`git`命令在開發環境中可用，上載過程將使用它檢查檔是否存儲在 git 存儲庫中。 如果是這樣，則 git 存儲庫中的資訊也會作為培訓運行的一部分上載。 此資訊存儲在以下用於定型運行的屬性中：

| 屬性 | 用於獲取值的 Git 命令 | 描述 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 從中克隆存儲庫的 URI。 |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 從中克隆存儲庫的 URI。 |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 提交運行時的活動分支。 |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 提交運行時的活動分支。 |
| `azureml.git.commit` | `git rev-parse HEAD` | 已提交用於運行的代碼的提交雜湊。 |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 已提交用於運行的代碼的提交雜湊。 |
| `azureml.git.dirty` | `git status --porcelain .` | `True`，如果分支/提交是髒的;如果分支/提交是髒的;如果分支/提交是髒的;如果分支/提交否則， `false`. |

此資訊用於使用估計器、機器學習管道或腳本運行的運行。

如果訓練檔未位於開發環境中的 git 存儲庫中，或者`git`該命令不可用，則不會跟蹤與 git 相關的資訊。

> [!TIP]
> 要檢查 git 命令在開發環境中是否可用，請打開 shell 會話、命令提示符、PowerShell 或其他命令列介面並鍵入以下命令：
>
> ```
> git --version
> ```
>
> 如果已安裝，並且在路徑中，您將收到類似于`git version 2.4.1`的回應。 有關在開發環境中安裝 git 的詳細資訊，請參閱[Git 網站](https://git-scm.com/)。

## <a name="view-the-logged-information"></a>查看記錄的資訊

git 資訊存儲在訓練運行的屬性中。 您可以使用 Azure 門戶、Python SDK 和 CLI 查看此資訊。 

### <a name="azure-portal"></a>Azure 入口網站

1. 從[Azure 門戶](https://portal.azure.com)中選擇工作區。
1. 選擇__實驗__，然後選擇一個實驗。
1. 從 __"運行編號"__ 列中選擇一個運行。
1. 選擇__日誌__，然後展開__日誌__和__azureml__條目。 選擇以__###\_azure__開頭的連結。

    ![門戶中的 ##_azure 條目](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

記錄的資訊包含類似于以下 JSON 的文本：

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

提交訓練運行後，將返回[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)物件。 此`properties`物件的屬性包含記錄的 git 資訊。 例如，以下代碼檢索提交雜湊：

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

CLI`az ml run`命令可用於從運行中檢索屬性。 例如，以下命令返回名為 的`train-on-amlcompute`實驗中最後一次運行的屬性：

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

有關詳細資訊，請參閱 az [ml 運行](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)引用文檔。

## <a name="next-steps"></a>後續步驟

* [設置和使用計算目標進行模型培訓](how-to-set-up-training-targets.md)
