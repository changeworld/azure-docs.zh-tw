---
title: 設定私人端點
titleSuffix: Azure Machine Learning
description: 使用 Azure Private Link 從虛擬網路安全地存取您的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 2953f85a5c21cdd670d6e133d09ffacf06f178ef
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842697"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>設定 Azure Machine Learning 工作區的 Azure Private Link

在本檔中，您將瞭解如何搭配使用 Azure Private Link 與您的 Azure Machine Learning 工作區。 如需建立 Azure Machine Learning 之虛擬網路的相關資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)

Azure Private Link 可讓您使用私人端點連接到工作區。 私人端點是虛擬網路內的一組私人 IP 位址。 然後，您可以將工作區的存取許可權制為只在私人 IP 位址上進行。 Private Link 有助於降低資料遭到外泄的風險。 若要深入了解私人端點，請參閱 [Azure Private Link](../private-link/private-link-overview.md) 一文。

> [!IMPORTANT]
> Azure Private Link 不會影響 Azure 控制平面 (管理作業) 例如刪除工作區或管理計算資源。 例如，建立、更新或刪除計算目標。 這些作業會正常地在公用網際網路上執行。 資料平面作業（例如使用 Azure Machine Learning studio、Api (包括) 的已發佈管線），或 SDK 使用私人端點。
>
> 如果您使用 Mozilla Firefox，您可能會遇到嘗試存取工作區私人端點的問題。 此問題可能與 Mozilla 的 HTTPS DNS 相關。 我們建議使用 Google Chrome Microsoft Edge 作為因應措施。

## <a name="prerequisites"></a>先決條件

如果您計畫使用具有客戶管理金鑰的已啟用私人連結的工作區，您必須使用支援票證要求這項功能。 如需詳細資訊，請參閱 [管理和增加配額](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)。

## <a name="limitations"></a>限制

Azure Government 區域或 Azure 中國的世紀區域無法使用具有 private link 的 Azure Machine Learning 工作區。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>建立使用私人端點的工作區

使用下列其中一種方法來建立具有私人端點的工作區。 這兩種方法都 __需要現有的虛擬網路__：

> [!TIP]
> 如果您想要同時建立工作區、私人端點和虛擬網路，請參閱 [使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。

# <a name="python"></a>[Python](#tab/python)

Azure Machine Learning Python SDK 提供可搭配工作區使用的 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 類別 [。建立 ( # B1 ](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) 以建立具有私人端點的工作區。 此類別需要現有的虛擬網路。

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning 的 Azure CLI 擴充](reference-azure-machine-learning-cli.md)功能提供[az ml workspace create](/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create)命令。 此命令的下列參數可以用來建立具有私人網路的工作區，但需要現有的虛擬網路：

* `--pe-name`：已建立之私用端點的名稱。
* `--pe-auto-approval`：是否應自動核准工作區的私人端點連接。
* `--pe-resource-group`：要在其中建立私人端點的資源群組。 必須是包含虛擬網路的相同群組。
* `--pe-vnet-name`：要在其中建立私人端點的現有虛擬網路。
* `--pe-subnet-name`：要在其中建立私人端點的子網名稱。 預設值是 `default`。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

Azure Machine Learning studio 中的 [ __網路__ 功能] 索引標籤可讓您設定私人端點。 不過，它需要現有的虛擬網路。 如需詳細資訊，請參閱 [在入口網站中建立工作區](how-to-manage-workspace.md)。

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>將私人端點新增至工作區

使用下列其中一種方法，將私人端點新增至現有的工作區：

> [!IMPORTANT]
>
> 您必須擁有現有的虛擬網路，才能在中建立私人端點。 您也必須 [停用私人端點的網路原則](../private-link/disable-private-endpoint-network-policy.md) ，才能新增私人端點。

> [!WARNING]
>
> 如果您有任何現有的計算目標與此工作區相關聯，而且它們不在相同的虛擬網路中（在中建立私人端點），它們將無法運作。

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

如需此範例中所使用之類別和方法的詳細資訊，請參閱 [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) 和 [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning 的 Azure CLI 擴充](reference-azure-machine-learning-cli.md)[功能提供 az ml 工作區私用端點新增](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add)命令。

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[入口網站](#tab/azure-portal)

從入口網站的 [Azure Machine Learning] 工作區中，選取 [ __私人端點__ 連線]，然後選取 [ __+ 私人端點__]。 使用欄位來建立新的私人端點。

* 選取 __區域__ 時，請選取與您的虛擬網路相同的區域。 
* 選取 __資源類型__ 時，請使用 __MachineLearningServices/工作區__。 
* 將 __資源__ 設定為您的工作區名稱。

最後，選取 [ __建立__ ] 以建立私人端點。

---

## <a name="remove-a-private-endpoint"></a>移除私人端點

您可以使用下列其中一種方法，從工作區中移除私人端點：

# <a name="python"></a>[Python](#tab/python)

使用 [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#delete-private-endpoint-connection-private-endpoint-connection-name-) 移除私人端點。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Machine learning 的 Azure CLI 擴充](reference-azure-machine-learning-cli.md)[功能提供 az ml workspace 私用端點刪除](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete)命令。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

從入口網站的 [Azure Machine Learning] 工作區中，選取 [ __私人端點__ 連線]，然後選取您想要移除的端點。 最後，選取 [ __移除__]。

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>使用私人端點上的工作區

因為僅允許從虛擬網路對工作區進行通訊，使用該工作區的任何開發環境都必須是虛擬網路的成員。 例如，虛擬網路中的虛擬機器。

> [!IMPORTANT]
> 為了避免連線暫時中斷，Microsoft 建議在啟用 Private Link 後，在連線到工作區的電腦上清除 DNS 快取。 

如需 Azure 虛擬機器的詳細資訊，請參閱 [虛擬機器檔](../virtual-machines/index.yml)。


## <a name="next-steps"></a>後續步驟

* 如需保護 Azure Machine Learning 工作區安全的詳細資訊，請參閱 [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md) 文章。

* 如果您打算在虛擬網路中使用自訂 DNS 解決方案，請參閱 [如何使用工作區與自訂 dns 伺服器](how-to-custom-dns.md)。
