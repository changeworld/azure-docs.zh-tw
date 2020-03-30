---
title: 將資源移動到新的訂閱或資源組
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248849"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>將資源移至新的資源群組或訂用帳戶

本文示範如何將 Azure 資源移至其他 Azure 訂用帳戶或相同訂用帳戶內的其他資源群組。 您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來移動資源。

移動作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定意味著您無法添加、更新或刪除資源組中的資源。 這並不意味著資源被凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。 鎖最多可以持續四個小時，但大多數移動在更短的時間內完成。

移動資源只會將它移到新的資源群組或訂用帳戶。 不會變更資源的位置。

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單

在移動資源之前，有幾個重要步驟需要進行。 藉由驗證這些條件，您可以避免錯誤。

1. 要移動的資源必須支援移動操作。 有關資源支援移動的清單，請參閱[移動資源操作支援](move-support-resources.md)。

1. 某些服務在移動資源時具有特定的限制或要求。 如果要移動以下任何服務，請在移動之前檢查該指南。

   * [應用服務移動指南](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps 服務移動指南](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [經典部署模型移動指南](./move-limitations/classic-model-move-limitations.md)- 經典計算、經典存儲、經典虛擬網路和雲服務
   * [網路移動指南](./move-limitations/networking-move-limitations.md)
   * [恢復服務移動指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虛擬機器移動指南](./move-limitations/virtual-machines-move-limitations.md)

1. 來源和目的地訂用帳戶必須為作用中。 如果您在啟用已停用的帳戶時遇到問題，請[建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 針對問題類型選取 [訂用帳戶管理]****。

1. 來源和目的地的訂用帳戶必須存在於相同的 [Azure Active Directory 租用戶](../../active-directory/develop/quickstart-create-new-tenant.md)內。 若要檢查這兩個訂用帳戶都有相同的租用戶識別碼，請使用 Azure PowerShell 或 Azure CLI。

   如果是 Azure PowerShell，請使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   對於 Azure CLI，請使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果來源和目的地訂用帳戶的租用戶識別碼不相同，請使用下列方法來協調租用戶識別碼：

   * [將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../../billing/billing-subscription-transfer.md)
   * [如何關聯 Azure 訂閱或將 Azure 訂閱添加到 Azure 活動目錄](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必須針對要移動之資源的資源提供者註冊其目的地訂用帳戶。 否則，您會收到錯誤，指出 **未針對資源類型註冊訂用帳戶**。 將資源移至新的訂用帳戶時，可能會因為不曾以指定的資源類型使用過該訂用帳戶，因而出現此錯誤。

   針對 PowerShell，使用下列命令來取得註冊狀態：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要註冊資源提供者，請使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   針對 Azure CLI，使用下列命令來取得註冊狀態：

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   若要註冊資源提供者，請使用：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 移動資源的帳戶至少必須有下列權限：

   * **Microsoft.資源/訂閱/資源組/移動資源/對**源資源組的操作。
   * **Microsoft.資源/訂閱/資源組/寫入**目標資源組。

1. 移動資源之前，請針對您要將資源移入的訂用帳戶，檢查其訂用帳戶配額。 如果移動資源表示訂用帳戶會超出限制，那麼您必須檢閱您是否可以要求增加配額。 如需限制清單和如何要求增加配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

1. **要跨訂閱移動，資源及其從屬資源必須位於同一資源組中，並且必須一起移動這些資源。** 例如，具有託管磁片的 VM 需要將 VM 和託管磁片與其他從屬資源一起移動。

   如果要將資源移動到新訂閱，請檢查該資源是否有任何從屬資源，以及這些資源是否位於同一資源組中。 如果資源不在同一資源組中，請檢查資源是否可以合併到同一資源組中。 如果是這樣，請使用跨資源組的移動操作將所有這些資源放入同一資源組中。

   有關詳細資訊，請參閱[跨訂閱移動的方案](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>在訂用帳戶之間移動的案例

將資源從一個訂閱移動到另一個訂閱是一個三步過程：

![交叉訂閱移動方案](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

為了便於說明，我們只有一個從屬資源。

* 步驟 1：如果依賴資源分佈在不同的資源組中，則首先將它們移動到一個資源組中。
* 步驟 2：將資源和從屬資源一起從源訂閱移動到目標訂閱。
* 步驟 3：可選地將從屬資源重新分發到目標訂閱中的不同資源組。 

## <a name="validate-move"></a>驗證移動

[驗證移動作業](/rest/api/resources/resources/validatemoveresources)可讓您直接測試移動案例，而不需要實際移動資源。 使用此操作可檢查移動是否成功。 當您發送移動請求時，將自動調用驗證。 僅當需要預先確定結果時，才使用此操作。 若要執行這項作業，您需要：

* 來源資源群組的名稱
* 目標資源群組的資源識別碼
* 要移動的每個資源所具備的資源識別碼
* 帳戶的[存取權杖](/rest/api/azure/#acquire-an-access-token)

傳送下列要求：

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

使用要求本文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果要求的格式正確，此作業將會傳回：

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 狀態碼指出已接受驗證要求，但尚未判斷移動作業是否會成功。 `location` 值包含一個 URL，可讓您用來檢查長時間執行作業的狀態。  

若要檢查狀態，請傳送下列要求：

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

當作業仍在執行時，您會持續收到 202 狀態碼。 請等候 `retry-after` 值中指出的秒數，再重新嘗試。 如果移動作業驗證成功，您會收到 204 狀態碼。 如果移動驗證失敗，則會收到錯誤訊息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>使用入口網站

若要移動資源，請選取具有這些資源的資源群組，然後選取 [移動]**** 按鈕。

![移動資源](./media/move-resource-group-and-subscription/select-move.png)

選擇將資源移動到新的資源群組或新的訂用帳戶。

選取要移動的資源和目的地資源群組。 認可您需要更新這些資源的指令碼，然後選取 [確定] ****。 如果您在上一個步驟中選取了 [編輯訂用帳戶] 圖示，則也必須選取目的地訂用帳戶。

![選取目的地](./media/move-resource-group-and-subscription/select-destination.png)

在 [通知] **** 中，您會看到移動作業正在執行。

![顯示移動狀態](./media/move-resource-group-and-subscription/show-status.png)

完成後，您會收到結果的通知。

![顯示移動結果](./media/move-resource-group-and-subscription/show-result.png)

如果收到錯誤，請參閱將[Azure 資源移動到新資源組或訂閱的疑難排解](troubleshoot-move.md)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下列範例示範了如何將多個資源移動到新的資源群組。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移動到新的訂用帳戶，請包含 `DestinationSubscriptionId`參數的值。

如果收到錯誤，請參閱將[Azure 資源移動到新資源組或訂閱的疑難排解](troubleshoot-move.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移動之資源的資源識別碼。 下列範例示範了如何將多個資源移動到新的資源群組。 請在 `--ids` 參數中，為要移動的資源識別碼提供以空格分隔的清單。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

如果收到錯誤，請參閱將[Azure 資源移動到新資源組或訂閱的疑難排解](troubleshoot-move.md)。

## <a name="use-rest-api"></a>使用 REST API

要將現有資源移動到其他資源組或訂閱，請使用["移動資源](/rest/api/resources/Resources/MoveResources)"操作。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在要求主體中，您可以指定目標資源群組以及要移動的資源。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果收到錯誤，請參閱將[Azure 資源移動到新資源組或訂閱的疑難排解](troubleshoot-move.md)。

## <a name="frequently-asked-questions"></a>常見問題集

**問：我的資源移動操作通常需要幾分鐘時間，已運行近一個小時。有什麼問題嗎？**

移動資源是具有不同階段的複雜操作。 它不僅僅涉及您嘗試移動的資源的資來源提供者。 由於資來源提供者之間的依賴關係，Azure 資源管理器允許 4 小時才能完成操作。 此時間段使資來源提供者有機會從暫時性問題中恢復。 如果移動請求在 4 小時內，操作將繼續嘗試完成，並且可能仍然成功。 在此期間，源和目標資源組將鎖定，以避免一致性問題。

**問：為什麼我的資源組在資源移動期間鎖定 4 小時？**

4 小時視窗是資源移動允許的最大時間。 為了防止對要移動的資源進行修改，源資源組和目標資源組在資源移動期間都會鎖定。

移動請求中有兩個階段。 在第一階段，將移動資源。 在第二階段，通知將發送到依賴于要移動的資源的其他資來源提供者。 當資來源提供者失敗任一階段時，可以在整個 4 小時視窗中鎖定資源組。 在允許的時間內，資源管理器重試失敗的步驟。

如果無法在 4 小時視窗中移動資源，資源管理器將解鎖兩個資源組。 已成功移動的資源位於目標資源組中。 無法移動的資源將留給源資源組。

**問：在資源移動期間鎖定源和目標資源組有何影響？**

該鎖阻止刪除任一資源組、在任一資源組中創建新資源或刪除移動中涉及的任何資源。

下圖顯示使用者嘗試刪除作為正在進行的移動一部分的資源組時來自 Azure 門戶的錯誤訊息。

![移動嘗試刪除的錯誤訊息](./media/move-resource-group-and-subscription/move-error-delete.png)

**問：錯誤代碼"缺失移動相關資源"是什麼意思？**

移動資源時，其從屬資源必須存在於目標資源組或訂閱中，或者包含在移動請求中。 當從屬資源不符合此要求時，您將獲得缺失移動依賴資源錯誤代碼。 錯誤訊息包含有關需要包含在移動請求中的從屬資源的詳細資訊。

例如，移動虛擬機器可能需要使用三個不同的資來源提供者移動七種資源類型。 這些資來源提供者和類型包括：

* Microsoft.Compute
   * virtualMachines
   * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

另一個常見示例涉及移動虛擬網路。 您可能需要移動與該虛擬網路關聯的其他幾個資源。 移動請求可能需要移動公共 IP 位址、路由表、虛擬網路閘道、網路安全性群組等。

**問：為什麼無法在 Azure 中移動某些資源？**

目前，Azure 支援中的所有資源都移動。 有關支援移動的資源清單，請參閱[移動資源操作支援](move-support-resources.md)。

## <a name="next-steps"></a>後續步驟

有關資源支援移動的清單，請參閱[移動資源操作支援](move-support-resources.md)。
