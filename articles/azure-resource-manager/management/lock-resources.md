---
title: 鎖定資源以防止更改
description: 透過將鎖定套用到所有使用者和角色，防止使用者更新或刪除重要的 Azure 資源。
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274004"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>鎖定資源以防止非預期的變更

身為系統管理員，您可能需要鎖定訂用帳戶、資源群組或資源，以防止組織中的其他使用者不小心刪除或修改重要資源。 您可以將鎖定層級設定為 **CanNotDelete** 或 **ReadOnly**。 在入口網站中，鎖定分別名為 [刪除]**** 和 [唯讀]****。

* **CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但無法刪除資源。 
* **ReadOnly** 表示經過授權的使用者可以讀取資源，但無法刪除或更新資源。 套用這個鎖定類似於限制所有經過授權使用者的權限是由「讀取者」**** 角色所授與。

## <a name="how-locks-are-applied"></a>如何套用鎖定

當您在父範圍套用鎖定時，該範圍內的所有資源都會都繼承相同的鎖定。 甚至您稍後新增的資源都會繼承父項的鎖定。 繼承中限制最嚴格的鎖定優先順序最高。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。 如要了解使用者和角色的設定權限，請參閱 [Azure 角色型存取控制](../../role-based-access-control/role-assignments-portal.md)。

Resource Manager 鎖定只會套用於管理平面發生的作業，亦即要傳送至 `https://management.azure.com` 的作業。 鎖定並不會限制資源執行自己函式的方式。 限制資源的變更，但沒有限制資源的作業。 例如，SQL Database 上的唯讀鎖定會防止您刪除或修改資料庫。 它不會防止您建立、更新或刪除資料庫中的資料。 允許資料交易，因為那些作業並不會傳送到 `https://management.azure.com`。

應用**ReadOnly**可能會導致意外的結果，因為某些似乎沒有修改資源的操作實際上需要被鎖阻止的操作。 **ReadOnly**鎖可以應用於資源或包含資源的資源組。 **ReadOnly**鎖阻止的操作的一些常見示例包括：

* 存儲帳戶上的**唯讀**鎖可防止所有使用者列出金鑰。 清單金鑰作業是透過 POST 要求進行處理，因為傳回的金鑰可用於寫入作業。

* App Service 資源上的**唯讀**鎖定會防止 Visual Studio 伺服器總管顯示該資源的檔案，因為該互動需要寫入存取權。

* 包含虛擬機器的資源組中的**ReadOnly**鎖可防止所有使用者啟動或重新開機虛擬機器。 這些操作需要 POST 請求。

## <a name="who-can-create-or-delete-locks"></a>誰可以創建或刪除鎖

若要建立或刪除管理鎖定，您必須擁有 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 動作的存取權。 在內置角色中，僅授予**擁有者**和**使用者訪問管理員**這些操作。

## <a name="managed-applications-and-locks"></a>託管應用程式和鎖

某些 Azure 服務（如 Azure 資料塊）使用[託管應用程式](../managed-applications/overview.md)來實現該服務。 在這種情況下，服務將創建兩個資源組。 一個資源組包含服務的概述，並且未鎖定。 另一個資源組包含服務的基礎結構並處於鎖定狀態。

如果嘗試刪除基礎結構資源組，則收到一條錯誤，指出資源組已鎖定。 如果嘗試刪除基礎結構資源組的鎖，則收到一條錯誤，指出無法刪除該鎖，因為它歸系統應用程式所有。

相反，請刪除服務，該服務也會刪除基礎結構資源組。

對於託管應用程式，請選擇您部署的服務。

![選擇服務](./media/lock-resources/select-service.png)

請注意，該服務包含**託管資源組**的連結。 該資源組保存基礎結構並處於鎖定狀態。 無法直接刪除它。

![顯示託管組](./media/lock-resources/show-managed-group.png)

要刪除服務的所有內容（包括鎖定的基礎結構資源組），請選擇"**刪除**服務"。

![刪除服務](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure 備份和鎖定

如果鎖定 Azure 備份服務創建的資源組，備份將開始失敗。 該服務最多支援 18 個還原點。 使用**CanNotDelete**鎖時，備份服務無法清理還原點。 有關詳細資訊，請參閱常見問題[- 備份 Azure VM。](../../backup/backup-azure-vm-backup-faq.md)

## <a name="portal"></a>入口網站

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>[範本]

使用資源管理器範本部署鎖時，根據鎖的範圍對名稱和類型使用不同的值。

將鎖應用於**資源**時，請使用以下格式：

* 名稱 -`{resourceName}/Microsoft.Authorization/{lockName}`
* 類型 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

將鎖應用於**資源組**或**訂閱**時，請使用以下格式：

* 名稱 -`{lockName}`
* 類型 - `Microsoft.Authorization/locks`

下列範例示範建立應用程式服務方案的範本、網站和網站上的鎖定。 鎖定的資源類型為要鎖定之資源的資源類型和 **/providers/locks**。 鎖定名稱的建立方式是串連資源名稱與 **/Microsoft.Authorization/** 和鎖定的名稱。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

有關在資源組上設置鎖的示例，請參閱[創建資源組並鎖定它](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)。

## <a name="powershell"></a>PowerShell
您可以使用 Azure PowerShell 以 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 命令鎖定已部署的資源。

若要鎖定資源，請提供資源的名稱、其資源類型，以及其資源群組名稱。

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要鎖定資源群組，請提供資源群組的名稱。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

要獲取有關鎖的資訊，請使用[獲取-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)。 若要取得訂用帳戶中的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock
```

若要取得資源的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要取得資源群組的所有鎖定，請使用︰

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

若要將鎖定刪除，請使用：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

您可使用 [az lock create](/cli/azure/lock#az-lock-create) 命令，透過 Azure CLI 來鎖定已部署的資源。

若要鎖定資源，請提供資源的名稱、其資源類型，以及其資源群組名稱。

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

若要鎖定資源群組，請提供資源群組的名稱。

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

若要取得鎖定的相關資訊，請使用 [az lock list](/cli/azure/lock#az-lock-list)。 若要取得訂用帳戶中的所有鎖定，請使用︰

```azurecli
az lock list
```

若要取得資源的所有鎖定，請使用︰

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

若要取得資源群組的所有鎖定，請使用︰

```azurecli
az lock list --resource-group exampleresourcegroup
```

若要將鎖定刪除，請使用：

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
您可以使用[管理鎖定的 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks)，來鎖定已部署的資源。 此 REST API 可讓您建立及刪除鎖定，以及抓取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。 lock-name 是您想要命名鎖定的任何名稱。 對於 api 版本，請使用**2016-09-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>後續步驟
* 若要了解如何邏輯地組織您的資源，請參閱 [使用標記來組織您的資源](tag-resources.md)
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 有關詳細資訊，請參閱什麼是[Azure 策略？](../../governance/policy/overview.md)
* 如需關於企業如何使用 Resource Manager 有效地管理訂用帳戶的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](/azure/architecture/cloud-adoption-guide/subscription-governance)。

