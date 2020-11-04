---
title: 建立及管理計算實例
titleSuffix: Azure Machine Learning
description: 瞭解如何在您的 Azure Machine Learning 工作區中建立及管理計算實例。 使用計算實例作為開發環境，或用於定型和推斷開發/測試用途。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 6e0d80c35a4822ad46973c94b32cf71b129ad1e1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318032"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>建立及管理 Azure Machine Learning 計算實例

瞭解如何在您的 Azure Machine Learning 工作區中建立及管理 [計算實例](concept-compute-instance.md) 。

請在雲端中使用計算執行個體來作為已完整設定和完全受控的開發環境。 針對開發和測試，您也可以使用實例做為 [定型計算目標](concept-compute-target.md#train) 或 [推斷目標](concept-compute-target.md#deploy)。   計算實例可以平行執行多個作業，並具有作業佇列。 在開發環境中，無法與您工作區中的其他使用者共用計算實例。

在本文中，您將學會如何：

* 建立計算執行個體 
* 管理 (啟動、停止、重新開機、刪除) 計算實例
* 存取終端機視窗 
* 安裝 R 或 Python 套件
* 建立新環境或 Jupyter 核心

計算實例可以在 [虛擬網路環境](how-to-secure-training-vnet.md)中安全地執行工作，而不需要企業開啟 SSH 埠。 作業會在容器化環境中執行，並將您的模型相依性封裝在 Docker 容器中。 

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

## <a name="create"></a>建立

**預估時間** ：大約5分鐘。

建立計算實例是工作區的一次性進程。 您可以將此計算重複使用於開發工作站或作為定型的計算目標。 您可以將多個計算實例附加至工作區。

適用于計算實例建立的每個區域每個區域的專用核心是統一的，並與 Azure Machine Learning 訓練計算叢集配額共用。 停止計算實例並不會釋放配額，以確保您能夠重新開機計算實例。 請注意，建立計算實例之後，就無法變更其虛擬機器大小。

下列範例示範如何建立計算實例：

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

如需此範例中所使用之類別、方法和參數的詳細資訊，請參閱下列參考檔：

* [ComputeInstance 類別](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?preserve-view=true&view=azure-ml-py)
* [ComputeTarget 建立](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

如需詳細資訊，請參閱 [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) reference （建立參考）。

# <a name="studio"></a>[Studio](#tab/azure-studio)

在 Azure Machine Learning studio 的工作區中，從 [ **計算** ] 區段或 [ **筆記本** ] 區段中建立新的計算實例（當您準備好要執行其中一個筆記本時）。

如需在 studio 中建立計算實例的相關資訊，請參閱 [在 Azure Machine Learning studio 中建立計算目標](how-to-create-attach-compute-studio.md#compute-instance)。

---

您也可以建立具有 [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)的計算實例。 

### <a name="create-on-behalf-of-preview"></a>代表 (預覽版建立) 

系統管理員可以代表資料科學家建立計算實例，並使用下列專案將實例指派給它們：
* [Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)。  如需有關如何尋找此範本所需之 TenantID 和 ObjectID 的詳細資訊，請參閱 [尋找驗證設定的識別物件識別碼](../healthcare-apis/find-identity-object-ids.md)。  您也可以在 Azure Active Directory 入口網站中找到這些值。
* REST API

您為其建立計算實例的資料科學家需要下列 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/overview.md) 許可權： 
* *MachineLearningServices/workspace/計算/開始/動作*
* *MachineLearningServices/workspace/計算/停止/動作*
* *MachineLearningServices/workspace/計算/重新開機/動作*
* *MachineLearningServices/workspace/計算/applicationaccess/action*

資料科學家可以啟動、停止和重新開機計算實例。 他們可以使用的計算實例：
* Jupyter
* JupyterLab
* RStudio
* 整合式筆記本

## <a name="manage"></a>管理

啟動、停止、重新開機和刪除計算實例。 計算實例不會自動縮小，因此請務必停止資源以防止持續收費。

# <a name="python"></a>[Python](#tab/python)

在下列範例中，計算實例的名稱為 **instance**

* 取得狀態

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* 停止

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* 開始

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* 重新啟動

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* 刪除

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

在下列範例中，計算實例的名稱為 **instance**

* 停止

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)。

* 開始 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)。

* 重新啟動 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)。

* 刪除

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    如需詳細資訊，請參閱 [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-delete)。

# <a name="studio"></a>[Studio](#tab/azure-studio)

在 Azure Machine Learning Studio 的工作區中選取 [計算]，然後選取頂端的 [計算執行個體]。

![管理計算執行個體](./media/concept-compute-instance/manage-compute-instance.png)

您可以執行下列動作：

* 建立新的計算實例 
* 重新整理 [計算實例] 索引標籤。
* 啟動、停止和重新開機計算實例。  當實例正在執行時，您需要支付該實例的費用。 當您未使用計算實例來降低成本時，請將它停止。 停止計算實例會將其解除配置。 然後再於需要時加以啟動。
* 刪除計算實例。
* 篩選計算實例的清單，只顯示您已建立的實例。

針對您在工作區中建立 (或為您建立的每個計算實例) ，您可以：

* 存取計算執行個體上的 Jupyter、JupyterLab、RStudio
* 透過 SSH 連線到計算執行個體。 預設會停用 SSH 存取，但可在建立計算執行個體時加以啟用。 SSH 存取是透過公開/私密金鑰機制來加以控制的。 此索引標籤會提供 SSH 連線的詳細資料，例如 IP 位址、使用者名稱和連接埠號碼。
* 取得特定計算執行個體的詳細資料，例如 IP 位址和區域。

---

[AZURE RBAC](../role-based-access-control/overview.md) 可讓您控制工作區中的哪些使用者可以建立、刪除、啟動、停止、重新開機計算實例。 工作區參與者和擁有者角色中的所有使用者都可以在工作區中建立、刪除、啟動、停止和重新啟動計算執行個體。 不過，只有特定計算實例的建立者，或代表其建立的使用者指派時，才允許存取該計算實例上的 Jupyter、JupyterLab 和 RStudio。 計算實例專用於具有根存取權的單一使用者，而且可以透過 Jupyter/JupyterLab/RStudio 來進行終端機。 計算實例將會有單一使用者登入，而且所有動作都將使用該使用者的身分識別來進行 Azure RBAC 和實驗執行的屬性。 SSH 存取是透過公開/私密金鑰機制來加以控制的。

這些動作可由 Azure RBAC 控制：
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *MachineLearningServices/workspace/計算/開始/動作*
* *MachineLearningServices/workspace/計算/停止/動作*
* *MachineLearningServices/workspace/計算/重新開機/動作*


## <a name="access-the-terminal-window"></a>存取終端機視窗

以下列任何方式開啟計算實例的終端機視窗：

* RStudio：選取左上角的 [終端機] 索引標籤。
* Jupyter Lab：在 [啟動器] 索引標籤中的 [其他] 標題下，選取 [終端機] 圖格。
* Jupyter：在 [檔案] 索引標籤的右上角選取 [新增] > [終端機]。
* 如果您在建立計算實例時啟用 SSH 存取，請透過 SSH 連線至電腦。

使用終端機視窗安裝套件，並建立其他核心。

## <a name="install-packages"></a>安裝套件

您可以直接在 Jupyter Notebook 或 RStudio 中安裝套件：

* RStudio：使用右下角的 [套件] 索引標籤，或左上角的 [主控台] 索引標籤。  
* Python：新增安裝程式碼，並在 Jupyter Notebook 儲存格中執行。

或者，您也可以從終端機視窗進行安裝。 將 Python 套件安裝至 **python 3.6-AzureML** 環境。  將 R 套件安裝到 **R** 環境中。

> [!NOTE]
> 針對筆記本內的套件管理，請使用 **% pip** 或 **% conda** 魔術函式將套件自動安裝 **到目前** 執行中的核心，而不是代表所有套件的 **！ pip** 或 **！ conda** ， (包括目前執行中核心以外的套件) 

## <a name="add-new-kernels"></a>加入新的核心程序

> [!WARNING]
>  自訂計算實例時，請確定您未刪除 **azureml_py36** conda 環境或 **Python 3.6-azureml** 核心。 這是 Jupyter/JupyterLab 功能的必要項

若要將新的 Jupyter 核心新增至計算實例：

1. 從 Jupyter、JupyterLab 或從筆記本窗格或 SSH 建立新的終端機到計算實例
2. 使用終端機視窗建立新的環境。  例如，下列程式碼會建立 `newenv` ：

    ```shell
    conda create --name newenv
    ```

3. 啟用環境。  例如，建立 `newenv` 之後：

    ```shell
    conda activate newenv
    ```

4. 將 pip 和 ipykernel 套件安裝至新環境，並為該 conda 環境建立核心

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

您可以安裝任何[可用的 Jupyter 核心程序](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) 。



## <a name="next-steps"></a>後續步驟

* [提交定型回合](how-to-set-up-training-targets.md)