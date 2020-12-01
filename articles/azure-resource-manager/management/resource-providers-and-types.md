---
title: 資源提供者和資源類型
description: 描述支援 Azure Resource Manager 的資源提供者。 它描述其架構、可用的 API 版本，以及可裝載資源的區域。
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a8adbce80d5e8f9ee9df2050d8f43363cbf57dc3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352094"
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

您可以透過 Azure 入口網站、Azure PowerShell 或 Azure CLI 來執行這些步驟。

如需將資源提供者對應至 Azure 服務的清單，請參閱 [Azure 服務的資源提供者](azure-services-resource-providers.md)。

## <a name="register-resource-provider"></a>註冊資源提供者

使用資源提供者之前，必須先為資源提供者註冊您的 Azure 訂用帳戶。 註冊會將您的訂用帳戶設定為使用資源提供者。 預設會註冊某些資源提供者。 當您採取特定動作時，會自動註冊其他資源提供者。 例如，當您透過入口網站建立資源時，通常會為您註冊資源提供者。 若是其他情況，您可能需要手動註冊資源提供者。

本文說明如何檢查資源提供者的註冊狀態，並視需要進行註冊。 您必須擁有對資源提供者進行作業的許可權 `/register/action` 。 許可權包含在「參與者」和「擁有者」角色中。

> [!IMPORTANT]
> 當您準備好要使用資源提供者時，請只註冊資源提供者。 註冊步驟可讓您在訂用帳戶內維持最低許可權。 惡意使用者無法使用未註冊的資源提供者。

您的應用程式程式碼不應針對處於 **註冊** 狀態的資源提供者封鎖資源的建立。 當您註冊資源提供者時，會針對每個支援的區域個別執行此作業。 若要在區域中建立資源，註冊只需要在該區域中完成。 藉由不封鎖註冊狀態的資源提供者，您的應用程式可以比等候所有區域完成更快。

當您的訂用帳戶中仍有來自該資源提供者的資源類型時，您無法取消註冊資源提供者。

## <a name="azure-portal"></a>Azure 入口網站

若要查看所有資源提供者，以及您訂用帳戶的登錄狀態：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站功能表上，選取 [所有服務]。

    ![選取 [訂用帳戶]](./media/resource-providers-and-types/select-all-services.png)

3. 在 [所有服務] 方塊中，輸入 [訂用帳戶]，然後選取 [訂用帳戶]。
4. 從訂用帳戶清單中選取要檢視的訂用帳戶。
5. 選取 **資源提供者** 並檢視可用資源提供者的清單。

    ![顯示資源提供者](./media/resource-providers-and-types/show-resource-providers.png)

6. 若要註冊資源提供者，請選取 [註冊]。 在前一個螢幕擷取畫面中，已針對 **Microsoft.Blueprint** 醒目提示 [註冊] 連結。 若要在您的訂用帳戶中維持最低許可權，請只註冊您已備妥可使用的資源提供者。

若要查看特定資源提供者的資訊：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站功能表上，選取 [所有服務]。
3. 在 [所有服務] 方塊中，輸入 [資源總管]，然後選取 [資源總管]。

    ![選取所有服務](./media/resource-providers-and-types/select-resource-explorer.png)

4. 選取向右箭號可展開 [提供者]。

    ![選取 [提供者]](./media/resource-providers-and-types/select-providers.png)

5. 展開資源提供者和您想要檢視的資源類型。

    ![選取 [資源類型]](./media/resource-providers-and-types/select-resource-type.png)

6. 所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶可能會有限制，以防止您使用某些支援該資源的區域。 資源總管會顯示資源類型的有效位置。

    ![顯示位置](./media/resource-providers-and-types/show-locations.png)

7. API 版本會對應至資源提供者所發行的 REST API 作業版本。 當資源提供者啟用新功能時，它會發行新版本的 REST API。 資源總管會顯示資源類型的有效 API 版本。

    ![顯示 API 版本](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

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

若要查看您訂用帳戶的所有已註冊資源提供者，請使用：

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

若要在您的訂用帳戶中維持最低許可權，請只註冊您已備妥可使用的資源提供者。 若要註冊資源提供者，請使用：

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

所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶可能會有限制，以防止您使用某些支援該資源的區域。

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

```azurecli-interactive
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

若要查看您訂用帳戶的所有已註冊資源提供者，請使用：

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

若要在您的訂用帳戶中維持最低許可權，請只註冊您已備妥可使用的資源提供者。 若要註冊資源提供者，請使用：

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

它會傳回一則訊息說明註冊持續進行中。

若要查看特定資源提供者的資訊，請使用：

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
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

所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 此外，您的訂用帳戶可能會有限制，以防止您使用某些支援該資源的區域。

若要取得資源類型支援的位置，請使用：

```azurecli-interactive
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

* 若要瞭解如何建立 Resource Manager 範本，請參閱 [撰寫 Azure Resource Manager 範本](../templates/template-syntax.md)。 
* 若要檢視資源提供者範本結構描述，請參閱[範本參考](/azure/templates/)。
* 如需將資源提供者對應至 Azure 服務的清單，請參閱 [Azure 服務的資源提供者](azure-services-resource-providers.md)。
* 若要檢視資源提供者的作業，請參閱 [Azure REST API](/rest/api/)。
