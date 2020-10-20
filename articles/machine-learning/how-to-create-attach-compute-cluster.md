---
title: 建立計算叢集
titleSuffix: Azure Machine Learning
description: 瞭解如何在您的 Azure Machine Learning 工作區中建立計算叢集。 使用計算叢集作為定型或推斷的計算目標。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 56ab5ba93545ffdbfd36850c08eda78cc239f694
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207116"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>建立 Azure Machine Learning 計算叢集

瞭解如何在您的 Azure Machine Learning 工作區中建立及管理 [計算](concept-compute-target.md#azure-machine-learning-compute-managed) 叢集。

您可以使用 Azure Machine Learning 計算叢集，將訓練或批次處理推斷程式散發到雲端中的 CPU 或 GPU 計算節點叢集。 如需包含 GPU 的 VM 大小有關的詳細資訊，請參閱 [GPU 最佳化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。 

在本文中，了解如何：

* 建立計算叢集
* 降低您的計算叢集成本
* 設定叢集的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* Machine Learning 服務、 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)或[Azure Machine Learning Visual Studio Code 延伸](tutorial-setup-vscode-extension.md)模組的[Azure CLI 延伸](reference-azure-machine-learning-cli.md)模組。

## <a name="what-is-a-compute-cluster"></a>什麼是計算叢集？

Azure Machine Learning 計算叢集是受控的計算基礎結構，可讓您輕鬆建立單一或多重節點計算。 計算會在工作區的區域內建立為能夠與工作區中的其他使用者共用的資源。 計算會在提交作業時自動相應增加，而且可以放在 Azure 虛擬網路中。 計算會在容器化環境中執行，並在 [Docker 容器](https://www.docker.com/why-docker)中封裝模型的相依性。

計算叢集可以在 [虛擬網路環境](how-to-secure-training-vnet.md)中安全地執行工作，而不需要企業開啟 SSH 埠。 作業會在容器化環境中執行，並將您的模型相依性封裝在 Docker 容器中。 

## <a name="limitations"></a>限制

* 請勿從您的工作區**建立多個同時附加至相同計算的附件**。 例如，使用兩個不同的名稱將一個計算叢集附加至工作區。 每個新的附件都會中斷先前現有的附件 (s) 。

    如果您想要重新連接計算目標，例如變更叢集設定，您必須先移除現有的附件。

* 本檔中列出的某些案例會標示為 __預覽__。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* Azure Machine Learning Compute 有預設限制，例如可配置的核心數目。 如需詳細資訊，請參閱[管理和要求 Azure 資源的配額](how-to-manage-quotas.md)。

* Azure 可讓您將 _鎖定_ 放置在資源上，使其無法被刪除或處於唯讀狀態。 __請勿將資源鎖定套用至包含您工作區的資源群組__。 將鎖定套用至包含您工作區的資源群組，將會防止 Azure ML 計算叢集的調整作業。 如需鎖定資源的詳細資訊，請參閱 [鎖定資源以防止非預期的變更](../azure-resource-manager/management/lock-resources.md)。

> [!TIP]
> 只要有足夠的配額可滿足所需的核心數目，叢集一般可以擴大為 100 個節點。 例如，叢集預設會設定為已在叢集節點之間啟用節點間通訊，以便支援 MPI 作業。 不過，您可以直接 [提出支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，並要求允許列出您的訂用帳戶或工作區，或使用特定叢集來停用節點間通訊，藉此將叢集調整為數千個節點。 


## <a name="create"></a>建立

**預估時間**：大約5分鐘。

Azure Machine Learning Compute 可以跨回合重複使用。 計算可與工作區中的其他使用者共用，並在回合之間保留，且會根據所提交的回合數目以及叢集上設定的 max_nodes 自動擴大或縮小節點。 min_nodes 設定可以控制可用的節點數目下限。

適用于計算叢集建立的每個區域每個區域的專用核心是統一的，並與 Azure Machine Learning 訓練計算實例配額共用。 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

未使用時，計算會自動向下調整為零節點。   視需要建立專用的虛擬機器以執行您的作業。
    
# <a name="python"></a>[Python](#tab/python)

若要使用 Python 建立持續性 Azure Machine Learning Compute 資源，請指定 **vm_size** 和 **max_nodes** 屬性。 Azure Machine Learning 接著會對於其他屬性使用智慧型預設值。 
    
* **vm_size**：Azure Machine Learning Compute 建立的節點虛擬機器系列。
* **max_nodes**：在 Azure Machine Learning Compute 上執行作業時，自動向上調整的最大節點數。


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

建立 Azure Machine Learning Compute 時，您也可以設定多個進階屬性。 這些屬性可讓您建立固定大小的持續性叢集，也可以在您訂用帳戶中現有的 Azure 虛擬網路內建立。  如需詳細資料，請參閱 [AmlCompute 類別](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true)。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

如需詳細資訊，請參閱 [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true)。

# <a name="studio"></a>[Studio](#tab/azure-studio)

如需在 studio 中建立計算叢集的詳細資訊，請參閱 [在 Azure Machine Learning studio 中建立計算目標](how-to-create-attach-compute-studio.md#amlcompute)。

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 降低您的計算叢集成本

您也可以選擇使用 [低優先順序的 vm](concept-plan-manage-cost.md#low-pri-vm) 來執行部分或所有工作負載。 這些 VM 沒有保證可用性，可能會在使用時被佔用。 被佔用的作業會重新開機，不會繼續。 

使用下列任何一種方式來指定低優先順序的 VM：
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

設定 `vm-priority` ：
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

在 studio 中，當您建立 VM 時，請選擇 **低優先順序** 。

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 設定受控識別

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* 在布建設定中設定受控識別：  

    * 系統指派的受控識別：
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * 使用者指派的受控識別：
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* 將受控識別新增至現有的計算叢集 
    
    * 系統指派的受控識別：
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * 使用者指派的受控識別：
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 使用受控識別建立新的受控計算叢集

  * 使用者指派的受控識別

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * 系統指派的受控識別

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 將受控識別新增至現有的叢集：

    * 使用者指派的受控識別
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * 系統指派的受控識別

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

請參閱 [在 studio 中設定受控識別](how-to-create-attach-compute-studio.md#managed-identity)。

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>受控識別使用方式

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>後續步驟

使用您的計算叢集：

* [提交定型回合](how-to-set-up-training-targets.md) 
* [執行批次推斷](how-to-use-parallel-run-step.md)。