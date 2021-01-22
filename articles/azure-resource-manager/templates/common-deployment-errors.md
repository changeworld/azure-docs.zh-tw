---
title: 針對一般部署錯誤進行疑難排解
description: 說明如何解決使用 Azure Resource Manager 將資源部署至 Azure 時的常見錯誤。
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 61a306cd36c55a005ee9ebd897fcfc9a6c88d7c9
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696391"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解

本文說明一些常見的 Azure 部署錯誤，並且提供解決錯誤的資訊。 如果您找不到部署錯誤的錯誤碼，請參閱[尋找錯誤碼](#find-error-code)。

如果您要尋找錯誤碼相關資訊，但本文並未提供該資訊，請讓我們知道。 您可在此頁面底部留下意見反應。 意見反應會與 GitHub 問題一併追蹤。

## <a name="error-codes"></a>錯誤碼

| 錯誤碼 | 降低 | 詳細資訊 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 遵循儲存體帳戶的命名限制。 | [解析儲存體帳戶名稱](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | 檢查可用儲存體帳戶屬性。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 叢集或區域沒有可用的資源或無法支援所要求的 VM 大小。 稍後重試要求，或要求不同的 VM 大小。 | [Linux 的佈建和配置問題](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-linux.md)、[Windows 的佈建和配置問題](../../virtual-machines/troubleshooting/troubleshoot-deployment-new-vm-windows.md)以及[配置失敗疑難排解](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | 等候並行作業完成。 | |
| AuthorizationFailed | 您的帳戶或服務主體沒有完成部署的足夠存取權。 請檢查您的帳戶所屬的角色以及它針對部署範圍的存取權。<br><br>當必要的資源提供者未註冊時，您可能會收到此錯誤。 | [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[解析註冊](error-register-resource-provider.md) |
| BadRequest | 您傳送的部署值不符合資源管理員的預期。 請查看內部狀態訊息，以取得疑難排解的說明。 | [範本參考](/azure/templates/)和[支援位置](resource-location.md) |
| 衝突 | 您要求的作業在資源的目前狀態下不允許。 例如，只有在建立 VM 時或解除配置 VM 之後，才可調整磁碟大小。 | |
| DeploymentActiveAndUneditable | 等候此資源群組的並行部署完成。 | |
| DeploymentFailedCleanUp | 當您以完整模式部署時，會刪除不在範本中的任何資源。 當您沒有足夠的許可權可刪除不在範本中的所有資源時，就會收到這個錯誤。 若要避免這個錯誤，請將部署模式變更為累加式。 | [Azure Resource Manager 部署模式](deployment-modes.md) |
| DeploymentNameInvalidCharacters | 部署名稱只可包含字母、數位、'-'、'. ' 或 ' _ '。 | |
| DeploymentNameLengthLimitExceeded | 部署名稱的限制為64個字元。  | |
| DeploymentFailed | DeploymentFailed 錯誤是一般錯誤，不會提供您解決錯誤所需的詳細資料。 尋找錯誤碼的錯誤詳細資料，以提供更多資訊。 | [尋找錯誤碼](#find-error-code) |
| DeploymentQuotaExceeded | 如果每個資源群組的部署達到 800 個數量限制，請從歷程記錄中刪除不再需要的部署。 | [解決部署計數超過800時的錯誤](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | 簡化您的範本以縮減大小。 | [解決範本大小錯誤](error-job-size-exceeded.md) |
| DnsRecordInUse | DNS 記錄名稱必須是唯一的。 輸入不同的名稱。 | |
| ImageNotFound | 檢查 VM 映像設定。 |  |
| InUseSubnetCannotBeDeleted | 當您嘗試更新資源時，可能會收到此錯誤，並藉由刪除和建立資源來處理要求。 請務必指定所有不變的值。 | [更新資源](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 取得適當租用戶的存取權杖。 您只能從您的帳戶所屬的租用戶取得權杖。 | |
| InvalidContentLink | 您最有可能嘗試連結至無法使用的嵌套範本。 再次確認您為巢狀範本提供的 URI。 如果儲存體帳戶中已有範本，請確定 URI 可存取。 您可能需要傳遞 SAS 權杖。 目前，您無法連結到位於 [Azure 儲存體防火牆](../../storage/common/storage-network-security.md)後方之儲存體帳戶中的範本。 請考慮將您的範本移至另一個存放庫，例如 GitHub。 | [連結的範本](linked-templates.md) |
| InvalidDeploymentLocation | 在訂用帳戶層級進行部署時，您為先前使用的部署名稱提供不同的位置。 | [訂用帳戶層級部署](deploy-to-subscription.md) |
| InvalidParameter | 您為資源提供的其中一個值與預期值不相符。 此錯誤的原因可能是許多不同情況。 例如，密碼強度不足，或 blob 名稱不正確。 錯誤訊息應該會指出需要更正的值。 | |
| InvalidRequestContent | 部署值包含無法辨識的值，或遺漏必要的值。 請確認您的資源類型值。 | [範本參考](/azure/templates/) |
| InvalidRequestFormat | 執行部署時啟用 debug 記錄，並確認要求的內容。 | [偵錯記錄](#enable-debug-logging) |
| InvalidResourceNamespace | 請檢查您在 **type** 屬性中指定的資源命名空間。 | [範本參考](/azure/templates/) |
| InvalidResourceReference | 資源不存在或未正確地參考。 檢查是否需要新增相依性。 確認您使用 **reference** 函式包括案例的必要參數。 | [解析相依性](error-not-found.md) |
| InvalidResourceType | 請檢查您在 **type** 屬性中指定的資源類型。 | [範本參考](/azure/templates/) |
| InvalidSubscriptionRegistrationState | 向資源提供者註冊訂用帳戶。 | [解析註冊](error-register-resource-provider.md) |
| InvalidTemplate | 請檢查錯誤的範本語法。 | [解析無效的範本](error-invalid-template.md) |
| InvalidTemplateCircularDependency | 移除不必要的相依性。 | [解析循環相依性](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | 簡化您的範本以縮減大小。 | [解決範本大小錯誤](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | 檢查您的帳戶是否屬於與您要部署的資源群組相同的租使用者。 | |
| LinkedInvalidPropertyId | 資源的資源識別碼未正確地解析。 請檢查您為資源識別碼提供所有必要值，包含訂用帳戶識別碼、資源群組名稱、資源類型、父代資源名稱 (如有需要) 和資源名稱。 | |
| LocationRequired | 提供資源的位置。 | [設定位置](resource-location.md) |
| MismatchingResourceSegments | 請確定巢狀資源的名稱和類型都有正確的區段數目。 | [解析資源區段](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | 檢查資源提供者註冊狀態和支援的位置。 | [解析註冊](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | 向資源提供者註冊訂用帳戶。 | [解析註冊](error-register-resource-provider.md) |
| NoRegisteredProviderFound | 檢查資源提供者註冊狀態。 | [解析註冊](error-register-resource-provider.md) |
| NotFound | 您可能嘗試使用父資源平行部署相依資源。 檢查是否需要新增相依性。 | [解析相依性](error-not-found.md) |
| OperationNotAllowed | 部署嘗試進行超過訂用帳戶、資源群組或區域配額的作業。 可能的話，請修改您的部署，以維持在配額內。 否則，請考慮要求變更您的配額。 | [解析配額](error-resource-quota.md) |
| ParentResourceNotFound | 請確定父代資源在建立子系資源之前即已存在。 | [解析父代資源](error-parent-resource.md) |
| PasswordTooLong | 您可能選取了具有太多字元的密碼，或將密碼值轉換為安全字串，然後再將它當作參數傳遞。 如果範本包含 **安全字串** 參數，則不需要將值轉換為安全字串。 提供密碼值作為文字。 |  |
| PrivateIPAddressInReservedRange | 指定的 IP 位址包含 Azure 所需的位址範圍。 變更 IP 位址以避免保留的範圍。 | [IP 位址](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | 指定的 IP 位址在子網路範圍之外。 變更 IP 位址，使其落在子網路範圍內。 | [IP 位址](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | 某些屬性無法在已部署的資源上變更。 更新資源時，將您的變更限制為允許的屬性。 | [更新資源](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | 您的訂用帳戶包含資源原則，可防止您在部署期間嘗試執行的動作。 尋找封鎖動作的原則。 可能的話，請變更您的部署，以符合原則的限制。 | [解析原則](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | 提供不包含保留名稱的資源名稱。 | [唯一的資源名稱](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 等候刪除完成。 | |
| ResourceGroupNotFound | 檢查部署的目標資源群組名稱。 目標資源群組必須已存在於您的訂用帳戶中。 檢查訂用帳戶內容。 | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | 您的部署會參考無法解析的資源。 確認您使用 **reference** 函式包括案例的必要參數。 | [解析參考](error-not-found.md) |
| ResourceQuotaExceeded | 部署嘗試建立資源，這些資源超過訂用帳戶、資源群組或區域的配額。 可能的話，請修改您的基礎結構，以維持在配額內。 否則，請考慮要求變更您的配額。 | [解析配額](error-resource-quota.md) |
| SkuNotAvailable | 選取可供您選取之位置使用的 SKU (例如 VM 大小)。 | [解析 SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | 提供儲存體帳戶的唯一名稱。 | [解析儲存體帳戶名稱](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | 提供儲存體帳戶的唯一名稱。 | [解析儲存體帳戶名稱](error-storage-account-name.md) |
| StorageAccountNotFound | 檢查您嘗試使用的訂用帳戶、資源群組和儲存體帳戶的名稱。 | |
| SubnetsNotInSameVnet | 虛擬機器只能有一個虛擬網路。 在部署數個 NIC 時，請確定它們屬於相同的虛擬網路。 | [多個 NIC](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | 無法存取指定的部署訂用帳戶。 可能是訂用帳戶識別碼錯誤、部署範本的使用者沒有足夠的許可權可部署至訂用帳戶，或訂用帳戶識別碼的格式錯誤。 使用嵌套部署 [跨範圍部署](./deploy-to-resource-group.md)時，請提供訂用帳戶的 GUID。 | |
| SubscriptionNotRegistered | 部署資源時，必須為您的訂用帳戶註冊資源提供者。 當您使用 Azure Resource Manager 範本進行部署時，會在訂用帳戶中自動註冊資源提供者。 有時候，自動註冊不會及時完成。 若要避免此間歇性錯誤，請在部署之前註冊資源提供者。 | [解析註冊](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | 移除不必要的相依性。 | [解析循環相依性](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | 減少單一部署的資源群組數目。 | [跨領域部署](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>尋找錯誤碼

您可能會收到兩種錯誤類型︰

* 驗證錯誤
* 部署錯誤

驗證錯誤來自可在部署之前判斷的情況。 其中包含您範本中的語法錯誤，或者嘗試部署會超出您訂用帳戶配額的資源。 部署程序期間出現的情況下會發生驗證錯誤。 其中包括嘗試存取以平行方式部署的資源。

這兩種錯誤類型都會傳回錯誤碼，以供您針對部署進行疑難排解。 這兩種錯誤類型都會出現在[活動記錄](../management/view-activity-logs.md)中。 不過，驗證錯誤不會出現在部署歷程記錄中，因為部署永遠不會啟動。

### <a name="validation-errors"></a>驗證錯誤

透過入口網站進行部署時，您在提交值之後看到驗證錯誤。

![顯示入口網站驗證錯誤](./media/common-deployment-errors/validation-error.png)

選取訊息以取得詳細資訊。 在下圖中，您會看到 **InvalidTemplateDeployment** 錯誤，以及指出某個原則封鎖了部署的訊息。

![顯示驗證詳細資料](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>部署錯誤

當作業通過驗證，但在部署期間失敗時，您會收到部署錯誤。

若要使用 PowerShell 查看部署錯誤的代碼和訊息，請使用：

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

若要使用 Azure CLI 查看部署錯誤的代碼和訊息，請使用：

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

在入口網站中選取通知。

![通知錯誤](./media/common-deployment-errors/notification.png)

您會看到更多與部署相關的詳細資料。 選取選項來尋找與錯誤相關的詳細資訊。

![部署失敗](./media/common-deployment-errors/deployment-failed.png)

您會看到錯誤訊息和錯誤碼。 請注意，有兩個錯誤碼。 第一個錯誤碼 (**DeploymentFailed**) 是一般錯誤，不會提供您解決錯誤所需的詳細資料。 第二個錯誤碼 (**StorageAccountNotFound**) 會提供您需要的詳細資料。

![錯誤詳細資料](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>啟用偵錯記錄

有時您需要更多關於要求和回應的資訊，以了解哪裡出了錯。 在部署期間，您可以要求在部署期間記錄其他資訊。

### <a name="powershell"></a>PowerShell

在 PowerShell 中，將 **DeploymentDebugLogLevel** 參數設定為 [All]、[ResponseContent] 或 [RequestContent]。

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

檢查具有下列 Cmdlet 的要求內容︰

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

或者，具有下列項目的回應內容︰

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

此資訊可協助您判斷範本中的值是否會正確設定。

### <a name="azure-cli"></a>Azure CLI

目前，Azure CLI 不支援開啟偵錯記錄功能，但您可以擷取偵錯記錄。

使用下列命令檢查部署作業︰

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

使用下列命令檢查要求內容︰

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

使用下列命令檢查回應內容︰

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>巢狀範本

若要記錄巢狀範本的偵錯資訊，請使用 **debugSetting** 項目。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>建立疑難排解範本

在某些情況下，針對範本進行疑難排解的最簡單方式是測試其組件。 您可以建立簡化範本，以便專注於您認為是錯誤成因的組件。 例如，假設您在參考資源時收到錯誤。 不需要處理整個範本，而是建立一個會傳回可能造成問題之組件的範本。 它可協助您判斷是否傳入正確的參數、正確地使用範本函式，以及取得所預期的資源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

或者，假設您收到的是您認為與不正確設定相依性相關的部署錯誤。 將其細分為簡化範本以測試您的範本。 首先，建立可部署單一資源 (例如 SQL Server) 的範本。 當您確定已正確定義該資源時，請新增相依于它的資源 (例如 SQL Database) 。 當您正確定義這兩個資源時，加入其他相依的資源 (例如稽核原則)。 在每個測試部署之間，刪除資源群組以確保您充分測試相依性。

## <a name="next-steps"></a>後續步驟

* 若要進行疑難排解教學課程，請參閱 [教學課程：針對 Resource Manager 範本部署進行疑難排解](template-tutorial-troubleshoot.md)
* 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../management/view-activity-logs.md)。
* 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](deployment-history.md)。