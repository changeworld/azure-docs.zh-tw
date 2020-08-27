---
title: 使用私人 Python 套件
titleSuffix: Azure Machine Learning
description: 從 Azure Machine Learning 環境安全地存取私人 Python 套件。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 314f6a45bf688125e79f0b8ce0099a8326b339dc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958145"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>搭配 Azure Machine Learning 使用私人 Python 套件
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure Machine Learning 中安全地使用私人 Python 套件。 私人 Python 套件的使用案例包括：

 * 您已開發出不想公開共用的私人套件。
 * 您想要使用儲存在企業防火牆內之封裝的策劃存放庫。

建議的方法取決於您的單一 Azure Machine Learning 工作區是否有少數套件，或是組織內所有工作區的整個封裝存放庫。

私用套件會透過 [環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) 類別使用。 在環境中，您會宣告要使用的 Python 套件，包括私用套件。 若要深入瞭解 Azure Machine Learning 中的環境，請參閱 [如何使用環境](how-to-use-environments.md)。 

## <a name="prerequisites"></a>Prerequisites

 * [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Azure Machine Learning 工作區](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>使用少數套件進行開發和測試

針對單一工作區的少數私用封裝，請使用靜態 [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 方法。 這種方法可讓您快速將私人套件新增至工作區，而且非常適合用於開發和測試用途。

將檔案路徑引數指向本機滾輪檔案，然後執行 ```add_private_pip_wheel``` 命令。 此命令會傳回用來追蹤您工作區中套件位置的 URL。 捕捉儲存體 URL，並將方法傳遞給它 `add_pip_package()` 。

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Azure Machine Learning 服務會在內部將 URL 取代為安全的 SAS URL，讓您的滾輪檔案保持私密且安全。

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>從 Azure DevOps 摘要使用套件的儲存機制

如果您正在積極開發機器學習應用程式的 Python 套件，您可以將它們裝載在 Azure DevOps 存放庫中作為成品，並將其發佈為摘要。 這種方法可讓您整合 DevOps 工作流程，以建立套件與您的 Azure Machine Learning 工作區。 若要瞭解如何使用 Azure DevOps 設定 Python 摘要，請參閱 [Azure Artifacts 中的 Python 套件開始](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

這種方法會使用個人存取權杖來針對存放庫進行驗證。 相同的方法適用于具有權杖型驗證的其他存放庫，例如私人 GitHub 存放庫。 

 1. 為您的 Azure DevOps 實例[建立 (PAT) 的個人存取權杖](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat)。 設定要 __封裝 > 讀取__的權杖範圍。 

 2. 使用 [工作區 set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) 方法，新增 Azure DevOps URL 和 PAT 作為工作區屬性。

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. 建立 Azure Machine Learning 環境，並從摘要新增 Python 套件。
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

環境現在已準備好用於定型回合或 web 服務端點部署中。 在建立環境時，Azure Machine Learning 服務會使用 PAT 對具有相符基底 URL 的摘要進行驗證。

## <a name="use-a-repository-of-packages-from-private-storage"></a>從私用儲存體使用套件存放庫

您可以從組織防火牆內的 Azure 儲存體帳戶使用套件。 儲存體帳戶可以保留一組策劃的封裝或公開可用封裝的內部鏡像。

若要設定這類私用儲存體：

1. [將工作區放在虛擬網路中 (VNet) ](how-to-enable-virtual-network.md)。
1. 建立儲存體帳戶，但不 [允許公用存取](https://docs.microsoft.com/azure/storage/common/storage-network-security)。
1. 將您想要使用的 Python 套件放入儲存體帳戶內的容器 
1. [允許從工作區 VNet 存取儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
1. [將 Azure Container Registry 的 (ACR) 放置於 VNet 背後的工作區](how-to-enable-virtual-network.md#azure-container-registry)。

    > [!IMPORTANT]
    > 您必須完成此步驟，才能使用私用套件儲存機制來定型或部署模型。

完成這些設定之後，您可以透過 Azure blob 儲存體中的完整 URL，參考 Azure Machine Learning 環境定義中的套件。

## <a name="next-steps"></a>後續步驟

 * 深入瞭解 [Azure Machine Learning 中的企業安全性](concept-enterprise-security.md)
