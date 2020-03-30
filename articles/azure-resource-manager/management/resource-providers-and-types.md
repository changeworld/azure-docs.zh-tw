---
title: 資來源提供者和資源類型
description: 說明支援資源管理員、其結構描述及可用 API 版本的資源提供者，以及可裝載資源的區域。
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 82b8251006a1a2d4edd198eca843489d3720f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273978"
---
# <a name="azure-resource-providers-and-types"></a>Azure 資源提供者和類型

部署資源時，您經常需要擷取有關資源提供者和類型的資訊。 例如，如果想要儲存金鑰和密碼，您會使用 Microsoft.KeyVault 資源提供者。 此資源提供者會提供稱為保存庫的資源類型來建立金鑰保存庫。

資源類型名稱的格式：**{resource-provider}/{resource-type}**。 金鑰保存庫的資源類型是 **Microsoft.KeyVault/vaults**。

在本文中，您將學會如何：

* 在 Azure 中檢視所有資源提供者
* 檢查資源提供者的註冊狀態
* 註冊資源提供者
* 檢視資源提供者的資源類型
* 檢視資源類型的有效位置
* 檢視資源類型的有效 API 版本

您可以通過 Azure 門戶、Azure PowerShell 或 Azure CLI 執行這些步驟。

有關將資來源提供者映射到 Azure 服務的清單，請參閱[Azure 服務的資來源提供者](azure-services-resource-providers.md)。

## <a name="azure-portal"></a>Azure 入口網站

若要查看所有資源提供者，以及您訂用帳戶的登錄狀態：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 Azure 入口網站功能表上，選取 [所有服務]****。

    ![選取 [訂用帳戶]](./media/resource-providers-and-types/select-all-services.png)

3. 在 [所有服務]**** 方塊中，輸入 [訂用帳戶]****，然後選取 [訂用帳戶]****。
4. 從訂用帳戶清單中選取要檢視的訂用帳戶。
5. 選取**資源提供者**並檢視可用資源提供者的清單。

    ![顯示資源提供者](./media/resource-providers-and-types/show-resource-providers.png)

6. 註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 註冊範圍一律是訂用帳戶。 許多資源提供者都會預設為自動註冊。 不過，您可能需要手動註冊某些資源提供者。 要註冊資來源提供者，您必須具有為資來源提供者執行`/register/action`操作的許可權。 這項作業包含在「參與者」和「擁有者」角色中。 若要註冊資源提供者，請選取 [註冊]****。 在前一個螢幕擷取畫面中，已針對 **Microsoft.Blueprint** 醒目提示 [註冊]**** 連結。

    當您在訂閱中仍具有來自該資來源提供者的資源類型時，無法取消註冊資來源提供者。

若要查看特定資源提供者的資訊：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 Azure 入口網站功能表上，選取 [所有服務]****。
3. 在 [所有服務]**** 方塊中，輸入 [資源總管]****，然後選取 [資源總管]****。

    ![選取所有服務](./media/resource-providers-and-types/select-resource-explorer.png)

4. 選取向右箭號可展開 [提供者]****。

    ![選取 [提供者]](./media/resource-providers-and-types/select-providers.png)

5. 展開資源提供者和您想要檢視的資源類型。

    ![選取 [資源類型]](./media/resource-providers-and-types/select-resource-type.png)

6. 所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。 資源總管會顯示資源類型的有效位置。

    ![顯示位置](./media/resource-providers-and-types/show-locations.png)

7. API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。 資源總管會顯示資源類型的有效 API 版本。

    ![顯示 API 版本](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要查看 Azure 中的所有資源提供者，以及您訂用帳戶的登錄狀態，請使用：

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

傳回的結果類似於：

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 註冊範圍一律是訂用帳戶。 許多資源提供者都會預設為自動註冊。 不過，您可能需要手動註冊某些資源提供者。 要註冊資來源提供者，您必須具有為資來源提供者執行`/register/action`操作的許可權。 這項作業包含在「參與者」和「擁有者」角色中。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

傳回的結果類似於：

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

當您在訂閱中仍具有來自該資來源提供者的資源類型時，無法取消註冊資來源提供者。

若要查看特定資源提供者的資訊，請使用：

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

傳回的結果類似於：

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

若要查看資源提供者的資源類型，請使用：

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

它會傳回：

```output
batchAccounts
operations
locations
locations/quotas
```

API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。

若要取得資源類型的可用 API 版本，請使用：

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

它會傳回：

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。

若要取得資源類型支援的位置，請使用：

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

它會傳回：

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

若要查看 Azure 中的所有資源提供者，以及您訂用帳戶的登錄狀態，請使用：

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

傳回的結果類似於：

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

註冊資源提供者可將您的訂用帳戶設定為可搭配資源提供者使用。 註冊範圍一律是訂用帳戶。 許多資源提供者都會預設為自動註冊。 不過，您可能需要手動註冊某些資源提供者。 要註冊資來源提供者，您必須具有為資來源提供者執行`/register/action`操作的許可權。 這項作業包含在「參與者」和「擁有者」角色中。

```azurecli
az provider register --namespace Microsoft.Batch
```

它會傳回一則訊息說明註冊持續進行中。

當您在訂閱中仍具有來自該資來源提供者的資源類型時，無法取消註冊資來源提供者。

若要查看特定資源提供者的資訊，請使用：

```azurecli
az provider show --namespace Microsoft.Batch
```

傳回的結果類似於：

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

若要查看資源提供者的資源類型，請使用：

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

它會傳回：

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。

若要取得資源類型的可用 API 版本，請使用：

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

它會傳回：

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶上可能會有一些限制，以防止您使用某些支援該資源的區域。

若要取得資源類型支援的位置，請使用：

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

它會傳回：

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>後續步驟

* 要瞭解如何創建資源管理器範本，請參閱[創作 Azure 資源管理器範本](../templates/template-syntax.md)。 
* 若要檢視資源提供者範本結構描述，請參閱[範本參考](/azure/templates/)。
* 有關將資來源提供者映射到 Azure 服務的清單，請參閱[Azure 服務的資來源提供者](azure-services-resource-providers.md)。
* 若要檢視資源提供者的作業，請參閱 [Azure REST API](/rest/api/)。
