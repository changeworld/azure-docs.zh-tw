---
title: 快速入門：使用 Azure CLI 進行新原則指派
description: 在本快速入門中，使用 Azure 入口網站建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2c289aa01362fcf0ede24bc1da7e8aef9e9be072
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496673"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>快速入門：建立原則指派，以便使用 Azure CLI 識別不相容資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。
本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將會成功識別出未使用受控磁碟的虛擬機器。 它們「不符合」  原則指派的規範。

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本指南使用 Azure CLI 來建立原則指派，以及識別 Azure 環境中不符合規範的資源。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 本快速入門會要求您執行 Azure CLI 2.0.76 版或更新版本，以在本機安裝和使用 CLI。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

- 使用 Azure CLI 註冊 Azure 原則見解資源提供者。 註冊資源提供者，以確保您的訂用帳戶可搭配它使用。 若要註冊資源提供者，您必須有權註冊資源提供者作業。 這項作業包含在「參與者」和「擁有者」角色中。 執行下列命令以註冊資源提供者：

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  如需註冊及檢視資源提供者的詳細資訊，請參閱[資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)

- 請安裝 [ARMClient](https://github.com/projectkudu/ARMClient) (如果尚未安裝)。 此工具會將 HTTP 要求傳送至以 Azure Resource Manager 為基礎的 API。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派 **Audit VMs that do not use managed disks (稽核沒有受控磁碟的虛擬機器)** 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

執行下列命令以建立原則指派：

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

上述命令會使用下列資訊：

- **Name** - 指派的實際名稱。 例如，我們使用了 audit-vm-manageddisks  。
- **DisplayName** - 原則指派的顯示名稱。 在此案例中，您會使用_稽核沒有受控磁碟指派的虛擬機器_。
- **原則** – 原則定義識別碼，這是您用來建立指派的根基。 在此案例中，即為原則定義_稽核沒有受控磁碟的虛擬機器_的 ID。 若要取得原則定義識別碼，請執行此命令：`az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。 請務必將 &lt;scope&gt; 取代為您的資源群組。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

若要檢視這個新指派之下不符合規範的資源，請執行下列命令來取得原則指派識別碼：

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

如需有關原則指派識別碼的詳細資訊，請參閱 [az policy assignment](/cli/azure/policy/assignment)。

接下來，執行下列命令，以取得不合規資源的資源識別碼，而這些識別碼會輸出到 JSON 檔案中：

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

您的結果類似下列範例：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

結果類似於您通常在 Azure 入口網站檢視中看到列在 [不符合規範的資源]  之下的內容。

## <a name="clean-up-resources"></a>清除資源

若要移除建立的指派，請使用下列命令：

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以驗證新資源是相容的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)
