---
title: 快速入門：使用 Python 進行新原則指派
description: 在本快速入門中，您會使用 Python 建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b2bbe38ce979dd12694908240a6adab317332a35
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074085"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>快速入門：使用 Python 建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 在本快速入門中，您會建立原則指派，以識別未使用受控磁碟的虛擬機器。 完成時，您會識別出「不符合規範」__ 的虛擬機器。

Python 程式庫可用來從命令列或在指令碼中管理 Azure 資源。 本指南說明如何使用 Python 程式庫建立原則指派。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>新增原則程式庫

若要讓 Python 能夠與 Azure 原則搭配使用，則必須新增程式庫。 此程式庫適用於可使用 Python 的任何位置，包括 [bash on Windows 10](/windows/wsl/install-win10) 或安裝在本機上。

1. 確認已安裝最新的 Python (至少 **3.8**)。 如果尚未安裝，請在 [Python.org](https://www.python.org/downloads/) 下載。

1. 確認已安裝最新的 Azure CLI (至少 **2.5.1**)。 如果尚未安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > Azure CLI 是啟用 Python 以在下列範例中使用 **CLI 型驗證**的必要項目。 如需其他選項的相關資訊，請參閱[使用適用於 Python 的 Azure 管理程式庫進行驗證](/azure/developer/python/azure-sdk-authenticate)。

1. 透過 Azure CLI 進行驗證。

   ```azurecli
   az login
   ```

1. 在您選擇的 Python 環境中，安裝 Azure Resource Graph 所需的程式庫：

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 如果為所有使用者安裝 Python，則必須從提高許可權的主控台執行這些命令。

1. 驗證是否已安裝程式庫。 `azure-mgmt-policyinsights` 應為 **0.5.0** 或更高版本，`azure-mgmt-resource` 應為 **9.0.0** 或更高版本，`azure-cli-core` 應為 **2.5.0** 或更高版本。

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派**稽核沒有受控磁碟的虛擬機器** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

執行下列程式碼以建立新的原則指派：

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

上述命令會使用下列資訊：

指派詳細資料：
- **display_name** - 原則指派的顯示名稱。 在此案例中，您會使用_稽核沒有受控磁碟指派的虛擬機器_。
- **policy_definition_id** – 原則定義路徑，這是您用來建立指派的根基。 在此案例中，即為原則定義_稽核沒有受控磁碟的虛擬機器_的 ID。 在此範例中，原則定義是內建的，而且路徑不包含管理群組或訂用帳戶資訊。
- **scope** - 範圍會決定在哪些資源或資源群組上強制執行原則指派。 此範圍可包含管理群組到個別資源。 請務必將 `{scope}` 取代為下列其中一個模式：
  - 管理群組：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 訂用帳戶：`/subscriptions/{subscriptionId}`
  - 資源群組：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 資源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** - 原則用途或為何指派給此範圍的更深入說明。

指派建立：

- Scope - 此範圍會決定原則指派的儲存位置。 指派詳細資料中設定的範圍必須存在於此範圍內。
- Name - 指派的實際名稱。 例如，我們使用了 audit-vm-manageddisks__。
- Policy assignment - 在上一個步驟中建立的 Python **PolicyAssignment** 物件。

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

使用下列資訊來識別不符合您所建立之原則指派的資源。 執行下列程式碼：

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

以您想要查看此原則指派合規性結果的訂用帳戶取代 `{subscriptionId}`。 如需其他範圍和資料摘要方式的清單，請參閱[原則狀態方法](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)。

您的結果類似下列範例：

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

結果會符合原則指派在 Azure 入口網站檢視中的 [資源合規性]**** 索引標籤所顯示的內容。

## <a name="clean-up-resources"></a>清除資源

若要移除建立的指派，請使用下列命令：

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

以您用來建立原則指派的相同範圍取代 `{scope}`。

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則定義，以驗證新資源是符合規範的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)