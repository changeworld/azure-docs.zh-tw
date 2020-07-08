---
title: 使用 Azure 事件方格發佈事件網域的事件
description: 顯示如何使用事件網域管理 Azure 事件方格中的大量主題集合，並將事件發佈到這些主題中。
services: event-grid
author: femila
ms.service: event-grid
ms.author: femila
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 10a0b138c702d4e4d09608c42959c68e97790f81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560979"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>使用事件網域管理主題並發佈事件

本文將說明如何：

* 建立事件方格網域
* 訂閱事件方格主題
* 列出金鑰
* 將事件發佈至網域

若要深入了解事件網域，請參閱[了解用於管理事件方格主題的事件網域](event-domains.md)。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>安裝預覽功能

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>建立事件網域

若要管理大量主題集合，請建立事件網域。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

建立成功會傳回下列值：

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

請記下 `endpoint` 和 `id`，因為管理網域和發佈事件時需要這些項目。

## <a name="manage-access-to-topics"></a>管理主題的存取權

您可以透過[角色指派](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)來管理主題的存取權。 角色指派會使用角色型存取控制，限制只有特定範圍上的授權使用者可在 Azure 資源上執行作業。

事件方格有兩個內建角色，可用來將網域中各種主題的存取權指派給特定使用者。 這些角色是 `EventGrid EventSubscription Contributor (Preview)` (可讓您建立和刪除訂閱) 和 `EventGrid EventSubscription Reader (Preview)` (只允許列出事件訂閱)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
下列 Azure CLI 命令會限制 `alice@contoso.com` 只能建立和刪除 `demotopic1` 主題的事件訂用帳戶：

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
下列 PowerShell 命令會限制 `alice@contoso.com` 只能建立和刪除 `demotopic1` 主題的事件訂用帳戶：

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

如需管理事件方格作業存取的詳細資訊，請參閱[事件方格安全性和驗證](./security-authentication.md)。

## <a name="create-topics-and-subscriptions"></a>建立主題和訂用帳戶

事件方格服務會根據為網域主題建立事件訂用帳戶的呼叫，在網域中自動建立及管理對應的主題。 沒有任何個別步驟可用來建立網域中的主題。 同樣地，刪除主題的最後一個事件訂閱時，也會將主題一併刪除。

訂閱網域中的主題如同訂閱任何其他 Azure 資源。 對於來源資源識別碼，請指定稍早建立網域時所傳回的事件網域識別碼。 若要指定您想要訂閱的主題，請在來源資源識別碼結尾新增 `/topics/<my-topic>`。 若要建立可接收網域中所有事件的網域範圍事件訂用帳戶，請指定事件網域識別碼，但不要指定任何主題。

您在上一節中授與存取權的使用者通常會建立訂用帳戶。 若要簡化這篇文章，您可以建立訂用帳戶。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

如果您需要能訂閱事件的測試端點，您可以一律部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)，以顯示傳入的事件。 您可以將事件傳送到您的測試網站：`https://<your-site-name>.azurewebsites.net/api/updates`。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

為主題設定的權限會儲存在 Azure Active Directory，必須明確地刪除。 如果使用者在主題上具有寫入權限，則刪除事件訂閱並不會撤銷使用者建立事件訂閱的存取權。


## <a name="publish-events-to-an-event-grid-domain"></a>將事件發佈到事件方格網域

將事件發佈至網域與[發行至自訂主題](./post-to-custom-topic.md)相同。 不過，您會所有事件發佈至網域端點，而不是發佈至自訂主題。 在 JSON 事件資料中，您可指定希望事件移至的主題。 下列事件陣列會將 `"id": "1111"` 的事件傳送到主題 `demotopic1`，同時將 `"id": "2222"` 的事件傳送到主題 `demotopic2`：

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
若要透過 Azure CLI 取得網域金鑰，請使用：

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

若要取得網域適用的金鑰，請使用：

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
若要使用 PowerShell 取得網域端點，請使用：

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

若要取得網域適用的金鑰，請使用：

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

然後使用您慣用的方法，讓 HTTP POST 將您的事件發佈至事件方格網域。

## <a name="next-steps"></a>後續步驟

* 如需深入了解事件網域和其為何實用的整體概念，請參閱[事件網域的概觀](event-domains.md)。
