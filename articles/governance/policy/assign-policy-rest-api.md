---
title: 快速入門：使用 REST API 進行新原則指派
description: 在本快速入門中，使用 REST API 建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: ab05079c5bb319f0808a743a1d668649df51b1b3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074000"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>快速入門：使用 REST API 建立原則指派，以識別不符合規範的資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。
本快速入門會逐步引導您完成程序來建立原則指派，以識別出未使用受控磁碟的虛擬機器。

在此程序結束時，您將會成功識別出未使用受控磁碟的虛擬機器。 它們「不符合」原則指派的規範。

REST API 可用來建立和管理 Azure 資源。 本指南使用 REST API 來建立原則指派，以及識別 Azure 環境中不符合規範的資源。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 請安裝 [ARMClient](https://github.com/projectkudu/ARMClient) (如果尚未安裝)。 此工具會將 HTTP 要求傳送至以 Azure Resource Manager 為基礎的 REST API。 您也可以使用 REST 文件中的「試試看」功能或 PowerShell 的 [RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 或 [Postman](https://www.postman.com)等工具。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會建立一個原則指派，並且指派 **稽核沒有受控磁碟的虛擬機器** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義。 此原則定義會識別與原則定義中設定之條件不相符的資源。

執行下列命令以建立原則指派：

   - REST API URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - 要求本文

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
       }
     }
     ```

先前的端點和要求本文會使用下列資訊：

REST API URI：
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 此範圍可包含管理群組到個別資源。 請務必將 `{scope}` 取代為下列其中一個模式：
  - 管理群組：`/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 訂用帳戶：`/subscriptions/{subscriptionId}`
  - 資源群組：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 資源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name** - 指派的實際名稱。 例如，我們使用了 audit-vm-manageddisks  。

要求本文：
- **DisplayName** - 原則指派的顯示名稱。 在此案例中，您會使用 _稽核沒有受控磁碟指派的虛擬機器_ 。
- **Description** - 原則用途或為何指派給此範圍的更深入說明。
- **policyDefinitionId** – 原則定義識別碼，這是您用來建立指派的根基。 在此案例中，即為原則定義 _稽核沒有受控磁碟的虛擬機器_ 的 ID。

## <a name="identify-non-compliant-resources"></a>識別不符合規範的資源

若要在此新指派下查看不符合規範的資源，請執行下列命令，以取得不合規資源的資源識別碼，而這些識別碼會輸出到 JSON 檔案中：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

結果類似於您通常在 Azure 入口網站檢視中看到列在 [不符合規範的資源]  之下的內容。

## <a name="clean-up-resources"></a>清除資源

若要移除建立的指派，請使用下列命令：

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

以您第一次建立原則指派時所使用的範圍取代 `{scope}`。

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以驗證新資源是相容的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)
