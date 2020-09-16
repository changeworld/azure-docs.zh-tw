---
title: 將資源移至新的訂用帳戶或資源群組
description: 使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b5cd8c9fa5947ff8f345bd0cd3ad26d9e61923
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603147"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>將資源移到新的資源群組或訂用帳戶 \(部分機器翻譯\)

本文示範如何將 Azure 資源移至其他 Azure 訂用帳戶或相同訂用帳戶內的其他資源群組。 您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來移動資源。

移動作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定表示您無法新增、更新或刪除資源群組中的資源。 這並不表示資源已遭到凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。 鎖定最多可以持續四個小時，但大部分的移動會在較短的時間內完成。

移動資源只會將它移到新的資源群組或訂閱。 不會變更資源的位置。

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單

在移動資源之前，有幾個重要步驟需要進行。 藉由驗證這些條件，您可以避免錯誤。

1. 您要移動的資源必須支援移動作業。 如需支援移動的資源清單，請參閱 [移動資源的作業支援](move-support-resources.md)。

1. 某些服務在移動資源時有特定的限制或需求。 如果您要移動下列任何服務，請在移動之前先檢查指引。

   * 如果您是使用 Azure Stack Hub，就無法在群組之間移動資源。
   * [應用程式服務移動指導方針](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 移動指導方針](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [傳統部署模型移動指引](./move-limitations/classic-model-move-limitations.md) -傳統計算、傳統儲存體、傳統虛擬網路和雲端服務
   * [網路移動指導方針](./move-limitations/networking-move-limitations.md)
   * [復原服務移動指引](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虛擬機器移動指導方針](./move-limitations/virtual-machines-move-limitations.md)

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

   * [將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [如何將 Azure 訂用帳戶關聯或新增至 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

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

   * 來源資源群組上的**Microsoft .resources/訂用帳戶/resourceGroups/moveResources/action** 。
   * **Microsoft .resources/訂用帳戶/resourceGroups/寫入** 目的地資源群組。

1. 移動資源之前，請針對您要將資源移入的訂用帳戶，檢查其訂用帳戶配額。 如果移動資源表示訂用帳戶會超出限制，那麼您必須檢閱您是否可以要求增加配額。 如需限制清單和如何要求增加配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

1. **對於跨訂用帳戶的移動，資源和其相依的資源必須位於相同的資源群組中，而且必須一起移動。** 例如，具有受控磁片的 VM 需要將 VM 和受控磁片一起移動，以及其他相依資源。

   如果您要將資源移至新的訂用帳戶，請檢查資源是否有任何相依的資源，以及它們是否位於相同的資源群組中。 如果資源不在相同的資源群組中，請檢查資源是否可以合併成相同的資源群組。 若是如此，請使用跨資源群組的移動作業，將所有這些資源帶入相同的資源群組。

   如需詳細資訊，請參閱跨訂用帳戶 [移動的案例](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>在訂用帳戶之間移動的案例

將資源從一個訂用帳戶移至另一個訂用帳戶是三個步驟的程式：

![跨訂用帳戶移動案例](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

為了方便說明，我們只有一個相依的資源。

* 步驟1：如果相依資源分散在不同的資源群組中，請先將它們移到一個資源群組。
* 步驟2：將資源和相依資源一起從來源訂用帳戶移至目標訂用帳戶。
* 步驟3：選擇性地將相依資源重新散發至目標訂用帳戶內的不同資源群組。

## <a name="validate-move"></a>驗證移動

[驗證移動作業](/rest/api/resources/resources/validatemoveresources)可讓您直接測試移動案例，而不需要實際移動資源。 您可以使用此作業來檢查移動是否成功。 當您傳送移動要求時，會自動呼叫驗證。 只有當您需要預先確定結果時，才使用此作業。 若要執行這項作業，您需要：

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

若要移動資源，請選取包含這些資源的資源群組。

當您查看資源群組時，移動選項會停用。

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="移動選項已停用":::

若要啟用移動選項，請選取您想要移動的資源。 若要選取所有資源，請選取清單最上方的核取方塊。 或者，個別選取 [資源]。 選取資源之後，就會啟用移動選項。

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="選取資源":::

選取 [ **移動** ] 按鈕。

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="移動選項":::

此按鈕提供三個選項：

* 移至新的資源群組。
* 移至新的訂用帳戶。
* 移至新的區域。 若要變更區域，請參閱在 [不同區域間移動資源 (從資源群組) ](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)。

選擇將資源移動到新的資源群組或新的訂用帳戶。

選取目的地資源群組。 認可您需要更新這些資源的指令碼，然後選取 [確定] ****。 如果您選取移至新的訂用帳戶，您也必須選取目的地訂用帳戶。

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="選取目的地":::

驗證資源可以移動之後，您會看到移動作業正在執行的通知。

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="通知":::

完成後，您會收到結果的通知。

如果您收到錯誤，請參閱 [疑難排解將 Azure 資源移至新的資源群組或訂](troubleshoot-move.md)用帳戶。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下列範例示範了如何將多個資源移動到新的資源群組。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移動到新的訂用帳戶，請包含 `DestinationSubscriptionId`參數的值。

如果您收到錯誤，請參閱 [疑難排解將 Azure 資源移至新的資源群組或訂](troubleshoot-move.md)用帳戶。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 [az resource move](/cli/azure/resource#az-resource-move) 命令。 提供要移動之資源的資源識別碼。 下列範例示範了如何將多個資源移動到新的資源群組。 請在 `--ids` 參數中，為要移動的資源識別碼提供以空格分隔的清單。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

如果您收到錯誤，請參閱 [疑難排解將 Azure 資源移至新的資源群組或訂](troubleshoot-move.md)用帳戶。

## <a name="use-rest-api"></a>使用 REST API

若要將現有資源移至另一個資源群組或訂用帳戶，請使用「 [移動資源](/rest/api/resources/Resources/MoveResources) 」作業。

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

如果您收到錯誤，請參閱 [疑難排解將 Azure 資源移至新的資源群組或訂](troubleshoot-move.md)用帳戶。

## <a name="frequently-asked-questions"></a>常見問題集

**問題：我的資源移動作業（通常需要幾分鐘的時間）已在執行大約一小時。是否有問題？**

移動資源是具有不同階段的複雜作業。 它可能只牽涉到您想要移動之資源的資源提供者。 由於資源提供者之間的相依性，Azure Resource Manager 可讓作業完成4小時的時間。 這段時間可讓資源提供者有機會從暫時性問題中復原。 如果您的移動要求在四小時內，則作業會繼續嘗試完成，而且可能仍會成功。 來源和目的地資源群組會在這段時間鎖定，以避免發生一致性問題。

**問題：為什麼我的資源群組在資源移動期間會鎖定四小時？**

移動要求最多可以完成四個小時。 為了防止修改所移動的資源，來源和目的地資源群組會在資源移動期間鎖定。

移動要求中有兩個階段。 在第一個階段中，會移動資源。 在第二個階段中，會將通知傳送給相依于要移動之資源的其他資源提供者。 資源群組可在資源提供者失敗任一階段時，鎖定整個四個小時。 在允許的時間內，Resource Manager 會重試失敗的步驟。

如果無法在四小時內移動資源，Resource Manager 會將這兩個資源群組解除鎖定。 成功移動的資源位於目的地資源群組中。 無法移動的資源會保留來源資源群組。

**問題：在資源移動期間鎖定的來源和目的地資源群組有何影響？**

此鎖定可防止您刪除其中一個資源群組、在資源群組中建立新的資源，或刪除任何參與移動的資源。

下圖顯示當使用者嘗試刪除正在進行中移動的資源群組時，來自 Azure 入口網站的錯誤訊息。

![移動嘗試刪除的錯誤訊息](./media/move-resource-group-and-subscription/move-error-delete.png)

**問題：錯誤碼 "MissingMoveDependentResources" 是什麼意思？**

移動資源時，其相依資源必須存在於目的地資源群組或訂用帳戶中，或包含在移動要求中。 當相依資源不符合這項需求時，您會收到 MissingMoveDependentResources 錯誤碼。 此錯誤訊息包含需要包含在移動要求中的相依資源的詳細資料。

例如，移動虛擬機器可能需要以三個不同的資源提供者來移動七個資源類型。 這些資源提供者和類型為：

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

另一個常見的範例牽涉到移動虛擬網路。 您可能必須移動多個與該虛擬網路相關聯的其他資源。 移動要求可能需要移動公用 IP 位址、路由表、虛擬網路閘道、網路安全性群組，以及其他專案。

**問題：為什麼我無法在 Azure 中移動某些資源？**

目前，Azure 支援不會移動中的所有資源。 如需支援移動的資源清單，請參閱 [移動資源](move-support-resources.md)的作業支援。

## <a name="next-steps"></a>後續步驟

如需支援移動的資源清單，請參閱 [移動資源的作業支援](move-support-resources.md)。
