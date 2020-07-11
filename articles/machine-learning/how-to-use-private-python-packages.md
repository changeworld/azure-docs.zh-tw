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
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209367"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>搭配 Azure Machine Learning 使用私人 Python 套件
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何在 Azure Machine Learning 內安全地使用私人 Python 套件。 私人 Python 套件的使用案例包括：

 * 您開發了不想要公開共用的私用套件。
 * 您想要使用儲存在企業防火牆內之封裝的策劃存放庫。

建議的方法取決於您的單一 Azure Machine Learning 工作區是否有幾個套件，或組織內所有工作區的整個套件存放庫。

私用套件是透過[環境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment)類別來使用。 在環境內，您會宣告要使用的 Python 套件，包括私用封裝。 若要瞭解一般 Azure Machine Learning 中的環境，請參閱[如何使用環境](how-to-use-environments.md)。 

## <a name="prerequisites"></a>先決條件

 * [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Azure Machine Learning 工作區](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>使用少數封裝進行開發和測試

如果是單一工作區的少數私用套件，請使用靜態 [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 方法。 這種方法可讓您快速地將私用套件新增至工作區，而且很適合用於開發和測試用途。

將檔案路徑引數指向本機滾輪檔案，然後執行 ```add_private_pip_wheel``` 命令。 此命令會傳回用來追蹤您工作區中套件位置的 URL。 請捕獲儲存體 URL，並將方法傳遞給它 `add_pip_package()` 。

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

就內部而言，Azure Machine Learning 服務會以安全的 SAS URL 取代 URL，因此您的滾輪檔案會保持私用且安全。

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>從 Azure DevOps 摘要取用套件的存放庫

如果您主動為機器學習應用程式開發 Python 套件，您可以將它們裝載在 Azure DevOps 存放庫中做為成品，並將它們發佈為摘要。 這種方法可讓您整合 DevOps 工作流程，以 Azure Machine Learning 工作區建立套件。 若要瞭解如何使用 Azure DevOps 來設定 Python 摘要，請參閱[開始使用中的 Python 套件 Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops)

這個方法會使用個人存取權杖來對存放庫進行驗證。 相同的方法適用于其他具有權杖型驗證的存放庫，例如私人 GitHub 存放庫。 

 1. 為 Azure DevOps 實例[ (PAT) 建立個人存取權杖](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat)。 設定要__封裝 > 讀取__的權杖範圍。 

 2. 使用[工作區. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-)方法，新增 [Azure DevOps URL] 和 [PAT] 做為工作區屬性。

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

環境現在已準備好用於定型回合或 web 服務端點部署中。 在建立環境時，Azure Machine Learning 服務會使用 PAT，以相符的基底 URL 對摘要進行驗證。

### <a name="consume-a-repository-of-packages-from-private-storage"></a>從私人儲存體取用套件的存放庫

您可以從組織防火牆內的 Azure 儲存體帳戶使用套件。 這類儲存體帳戶可以保留一組策劃的封裝，以供企業使用或可公開使用之套件的內部鏡像。

若要設定這類私用儲存體：

 1. [將工作區放在虛擬網路中 (VNET) ](how-to-enable-virtual-network.md)。
 2. 建立儲存體帳戶並不[允許公用存取](https://docs.microsoft.com/azure/storage/common/storage-network-security)。
 2. 將您想要使用的 Python 套件放入儲存體帳戶內的容器中 
 3. [允許從工作區 VNET 存取儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 

然後，您可以透過 Azure blob 儲存體中的完整 URL 來參考 Azure Machine Learning 環境定義中的套件。

## <a name="next-steps"></a>後續步驟

 * 深入瞭解[Azure Machine Learning 中的企業安全性](concept-enterprise-security.md)
