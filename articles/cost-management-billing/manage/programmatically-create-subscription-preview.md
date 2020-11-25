---
title: 使用預覽 API 以程式設計方式建立 Azure 訂用帳戶
description: 了解如何使用預覽版的 REST API、Azure CLI 和 Azure PowerShell，以程式設計方式建立額外的 Azure 訂用帳戶。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844330"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>使用預覽 API 以程式設計方式建立 Azure 訂用帳戶

本文可協助您使用我們舊版的預覽 API，以程式設計方式建立 Azure 訂用帳戶。 在本文中，可了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶。

我們提供了新文章，說明可與不同的 Azure 合約訂用帳戶類型搭配使用的最新 API 版本：

- [使用最新 API 以程式設計方式建立 EA 訂用帳戶](programmatically-create-subscription-enterprise-agreement.md)
- [使用最新 API 以程式設計方式建立 MCA 訂用帳戶](programmatically-create-subscription-microsoft-customer-agreement.md)
- [使用最新 API 以程式設計方式建立 MPA 訂用帳戶](Programmatically-create-subscription-microsoft-customer-agreement.md)

不過，如果您不想要使用最新的 API 版本，仍然可以使用本文中的資訊。

Azure 客戶若使用下列合約類型的計費帳戶，則可以程式設計方式建立訂用帳戶：

- Enterprise 合約
- Microsoft 客戶合約 (MCA)
- Microsoft 合作夥伴合約 (MPA)

當您以程式設計方式建立 Azure 訂用帳戶時，該訂用帳戶會受合約規範，此合約是您向 Microsoft 或授權經銷商取得 Azure 服務時所簽署的合約。 如需詳細資訊，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>為 EA 計費帳戶建立訂用帳戶

使用下列各節中的資訊來建立 EA 訂用帳戶。

### <a name="prerequisites"></a>Prerequisites

您必須擁有註冊帳戶的「擁有者」角色，才能建立訂用帳戶。 有兩種方式可取得此角色：

* 您註冊中的 Enterprise 管理員可以[讓您成為帳戶擁有者](https://ea.azure.com/helpdocs/addNewAccount) (需登入)，這可讓您成為註冊帳戶的擁有者。
* 註冊帳戶的現有擁有者可以[授與您存取權](grant-access-to-create-subscription.md)。 同樣地，若要使用服務主體來建立 EA 訂用帳戶，您必須[授與該服務主體建立訂用帳戶的能力](grant-access-to-create-subscription.md)。

### <a name="find-accounts-you-have-access-to"></a>尋找您可以存取的帳戶

當您加入與「帳戶擁有者」相關聯的註冊帳戶之後，Azure 會使用帳戶與註冊的關聯性來判斷由誰支付訂用帳戶的費用。 所有在該帳戶下所建立訂用帳戶的費用，都會由該帳戶所在的 EA 註冊支付。 若要建立訂用帳戶，您必須傳入註冊帳戶與使用者主體的相關值，才能擁有訂用帳戶。

若要執行下列命令，您必須登入「帳戶擁有者」的 *主目錄* (建立訂用帳戶時預設的所在目錄)。

### <a name="rest"></a>[REST](#tab/rest)

要求列出您有權存取的所有註冊帳戶：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 回應會列出您有權存取的所有註冊帳戶：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 複製該帳戶的 `name`。 例如，若要在 SignUpEngineering@contoso.com 註冊帳戶下建立訂用帳戶，請複製 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此識別碼是註冊帳戶的物件識別碼。 將此值貼在某處，以便您在下一個步驟中將其作為 `enrollmentAccountObjectId`。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

開啟 [Azure Cloud Shell](https://shell.azure.com/)，然後選取 [PowerShell]。

使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) Cmdlet 來列出您可以存取的所有註冊帳戶。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 會以您可以存取的註冊帳戶清單來回應：

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 複製該帳戶的 `ObjectId`。 例如，若要在 SignUpEngineering@contoso.com 註冊帳戶下建立訂用帳戶，請複製 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 將物件識別碼貼在某處，以便您在下一個步驟中作為 `enrollmentAccountObjectId` 使用。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az billing enrollment-account list](/cli/azure/billing) 命令來列出您可以存取的所有註冊帳戶。

```azurecli-interactive
az billing enrollment-account list
```

Azure 會以您可以存取的註冊帳戶清單來回應：

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 複製該帳戶的 `name`。 例如，若要在 SignUpEngineering@contoso.com 註冊帳戶下建立訂用帳戶，請複製 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此識別碼是註冊帳戶的物件識別碼。 將此值貼在某處，以便您在下一個步驟中將其作為 `enrollmentAccountObjectId`。

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定的註冊帳戶下建立訂用帳戶

下列範例會在上一個步驟中選取的註冊帳戶中，建立名為「開發小組訂用帳戶」的訂用帳戶。 訂用帳戶供應項目是 MS-AZR-0017P (一般 Microsoft Enterprise 合約)。 這也會依選擇新增兩個使用者作為該訂用帳戶的「Azure RBAC 擁有者」。

### <a name="rest"></a>[REST](#tab/rest)

提出下列要求，並以從第一個步驟複製的 `name` 取代 `<enrollmentAccountObjectId>` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 若要指定擁有者，請參閱[如何取得使用者物件識別碼](grant-access-to-create-subscription.md#userObjectId)。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 元素名稱  | 必要 | 類型   | 說明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | String | 訂用帳戶的顯示名稱。 如果未指定，會設定為供應項目的名稱，例如「Microsoft Azure 企業版」。                                 |
| `offerType`   | 是      | String | 訂用帳戶的供應項目方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `owners`      | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之使用者的「物件識別碼」。  |

在回應中，您可以從 `Location` 標頭中取得可查詢訂用帳戶建立作業狀態的 URL。 建立好訂用帳戶之後，`Location` URL 上的 GET 會傳回 `subscriptionLink` 物件，其中包含訂用帳戶 ID。 如需詳細資訊，請參閱[訂用帳戶 API 文件](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要安裝包含 `New-AzSubscription` Cmdlet 的最新版模組，請執行 `Install-Module Az.Subscription`。 若要安裝最新版本的 PowerShellGet，請參閱[取得 PowerShellGet 模組](/powershell/scripting/gallery/installing-psget)。

執行下列 [New-AzSubscription](/powershell/module/az.subscription) 命令，將 `<enrollmentAccountObjectId>` 取代為第一個步驟中所收集的 `ObjectId` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 若要指定擁有者，請參閱[如何取得使用者物件識別碼](grant-access-to-create-subscription.md#userObjectId)。

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 元素名稱  | 必要 | 類型   | 說明 |
|---------------|----------|--------|----|
| `Name` | 否      | String | 訂用帳戶的顯示名稱。 若未指定，則會設定為供應項目的名稱，例如 *Microsoft Azure 企業版*。 |
| `OfferType`   | 是      | String | 訂用帳戶供應項目。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `EnrollmentAccountObjectId`      | 是       | String | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 此值是您從 `Get-AzEnrollmentAccount` 取得的 GUID。 |
| `OwnerObjectId`      | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之使用者的「物件識別碼」。  |
| `OwnerSignInName`    | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `OwnerObjectId`。 使用此參數時，服務主體必須具有[目錄的讀取權限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true)。|

若要查看所有參數的完整清單，請參閱 [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription)。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，藉由執行 `az extension add --name subscription` 來安裝預覽延伸模組。

執行下列 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) 命令，將 `<enrollmentAccountObjectId>` 取代為您在第一個步驟中複製的 `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 若要指定擁有者，請參閱[如何取得使用者物件識別碼](grant-access-to-create-subscription.md#userObjectId)。

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名稱  | 必要 | 類型   | 說明 |
|---------------|----------|--------|------------|
| `display-name` | 否      | String | 訂用帳戶的顯示名稱。 若未指定，則會設定為供應項目的名稱，例如 *Microsoft Azure 企業版*。|
| `offer-type`   | 是      | String | 訂用帳戶的供應項目方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `enrollment-account-object-id`      | 是       | String | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 此值是您從 `az billing enrollment-account list` 取得的 GUID。 |
| `owner-object-id`      | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之使用者的「物件識別碼」。  |
| `owner-upn`    | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `owner-object-id`。|
| `owner-spn` | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `owner-object-id`。 使用此參數時，服務主體必須具有[目錄的讀取權限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true)。|

若要查看所有參數的完整清單，請參閱 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true)。

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 企業版訂用帳戶建立 API 的限制

- 使用此 API 只能建立「Azure 企業版」訂用帳戶。
- 每一註冊帳戶僅限 2000 個訂用帳戶。 之後，您只能在 Azure 入口網站中為帳戶建立更多訂用帳戶。 如果您想要透過 API 建立更多訂用帳戶，請建立另一個註冊帳戶。 已取消、已刪除和已轉移的訂用帳戶會計入 2000 筆的限制。
- 不是「帳戶擁有者」但已透過 Azure RBAC 新增至註冊帳戶的使用者，並無法在 Azure 入口網站中建立訂用帳戶。
- 您無法選取要作為訂用帳戶建立位置的租用戶。 訂用帳戶一律會建立在「帳戶擁有者」的主租用戶中。 若要將訂用帳戶移至不同的租用戶，請參閱[變更訂用帳戶租用戶](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。


## <a name="create-subscriptions-for-an-mca-account"></a>為 MCA 帳戶建立訂用帳戶

使用下列各節中的資訊，建立 MCA 帳戶的訂用帳戶。

### <a name="prerequisites"></a>Prerequisites

您必須是發票區段上的擁有者、參與者或 Azure 訂用帳戶角色，或是帳單設定檔或計費帳戶上的擁有者或參與者角色，才能建立訂用帳戶。 如需詳細資訊，請參閱[訂用帳戶計費角色和工作](understand-mca-roles.md#subscription-billing-roles-and-tasks)。

下列範例會使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

### <a name="find-billing-accounts-that-you-have-access-to"></a>尋找您可以存取的計費帳戶

請提出下列要求，以列出所有計費帳戶。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 回應會列出您有權存取的計費帳戶。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的計費帳戶。 請確定帳戶的 agreementType 是 MicrosoftCustomerAgreement。 複製帳戶的 `name`。  例如，若要建立 `Contoso` 計費帳戶的訂用帳戶，請複製 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="find-invoice-sections-to-create-subscriptions"></a>尋找發票區段以建立訂用帳戶

訂用帳戶的費用將會顯示在帳單設定檔的發票區段。 請使用下列 API 來取得您有權在其中建立 Azure 訂用帳戶的發票區段和帳單設定檔清單。

提出下列要求，並以從第一個步驟複製的 `name` 取代 `<billingAccountName>` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)。

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

API 回應會列出所有您有權在其中建立訂用帳戶的發票區段及其帳單設定檔：

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

請使用 `invoiceSectionDisplayName` 屬性來識別您要為其建立訂用帳戶的發票區段。 複製發票區段的 `invoiceSectionId`、`billingProfileId` 和其中一個 `skuId`。 例如，若要針對 `Development` 發票區段建立 `Microsoft Azure plan` 類型的訂用帳戶，請複製 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` 和 `0001`。 將這些值貼入某處，以便您在下一個步驟中使用。

### <a name="create-a-subscription-for-an-invoice-section"></a>為發票區段建立訂用帳戶

下列範例會針對「開發」發票區段建立名為「開發小組訂用帳戶」且類型為「Microsoft Azure 方案」的訂用帳戶。 訂用帳戶的費用會計入「Contoso 財務部門」帳單設定檔，並出現在其發票的「開發」區段中。

提出下列要求，並以從第二個步驟複製的 `invoiceSectionId` 取代 `<invoiceSectionId>` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```)。 將您在第二個步驟中複製的 `billingProfileId` 和 `skuId` 傳遞至 API 的要求參數。 若要指定擁有者，請參閱[如何取得使用者物件識別碼](grant-access-to-create-subscription.md#userObjectId)。

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| 元素名稱  | 必要 | 類型   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 是      | String | 訂用帳戶的顯示名稱。|
| `billingProfileId`   | 是      | String | 要對訂用帳戶收取費用的帳單設定檔識別碼。  |
| `skuId` | 是      | String | 決定 Azure 方案類型的 SKU 識別碼。 |
| `owners`      | 否       | String | 在建立訂用帳戶之後，任何要在該訂用帳戶上新增為「Azure RBAC 擁有者」的使用者或服務主體的「物件識別碼」。  |
| `costCenter` | 否      | String | 與訂用帳戶相關聯的成本中心。 這會顯示在使用量 CSV 檔案中。 |
| `managementGroupId` | 否      | String | 其中將新增訂用帳戶的管理群組識別碼。 若要取得管理群組的清單，請參閱[管理群組 - 清單 API](/rest/api/resources/managementgroups/list)。 使用 API 中的管理群組識別碼。 |

在回應中，會傳回一個 `subscriptionCreationResult` 物件供您監視。 建立完訂用帳戶之後，`subscriptionCreationResult` 物件會傳回 `subscriptionLink` 物件，其中包含訂用帳戶識別碼。

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>為 MPA 計費帳戶建立訂用帳戶

使用下列各節中的資訊，建立 MPA 計費帳戶的訂用帳戶。

### <a name="prerequisites"></a>必要條件

在組織的雲端解決方案提供者帳戶中，您必須是全域管理員或管理員代理者角色，才能為計費帳戶建立訂用帳戶。 如需詳細資訊，請參閱[合作夥伴中心 - 指派使用者角色和權限](/partner-center/permissions-overview)。

下列範例會使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>尋找您可以存取的計費帳戶

請在下方提出要求，以列出您有權存取的所有計費帳戶。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

API 回應會列出計費帳戶。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的計費帳戶。 請確定帳戶的 agreementType 是 MicrosoftPartnerAgreement。 複製帳戶的 `name`。 例如，若要建立 `Contoso` 計費帳戶的訂用帳戶，請複製 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="find-customers-that-have-azure-plans"></a>尋找具有 Azure 方案的客戶

進行下列要求，將 `<billingAccountName>` 取代為從第一個步驟複製的 `name` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)，以列出計費帳戶中您可以為其建立 Azure 訂用帳戶的所有客戶。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 回應會列出計費帳戶中具有 Azure 方案的客戶。 您可以為這些客戶建立訂用帳戶。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的客戶。 複製客戶的 `id`。 例如，若要建立 `Fabrikam toys` 的訂用帳戶，請複製 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 將此值貼到某個位置，以便在後續步驟中使用。

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>間接提供者的選用服務：為客戶指定轉銷商

如果您是 CSP 兩層型模型中的間接提供者，您可以在為客戶建立訂用帳戶時指定轉銷商。

提出下列要求，並將 `<customerId>` 取代為第二個步驟中複製的 `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)，以列出客戶適用的所有轉銷商。

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 回應會列出客戶適用的轉售商：

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

請使用 `description` 屬性來識別要與訂用帳戶相關聯的轉銷商。 複製轉銷商的 `resellerId`。 例如，若要建立 `Wingtip` 的關聯，請複製 `3xxxxx`。 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="create-a-subscription-for-a-customer"></a>為客戶建立訂用帳戶

下列範例會為 *Fabrikam toys* 建立名為「開發小組訂用帳戶」的訂用帳戶，並讓 *Wingtip* 轉銷商與訂用帳戶建立關聯。 

提出下列要求，並以從第二個步驟複製的 `id` 取代 `<customerId>` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 在 API 的要求參數中，傳遞從第二個步驟中複製的選擇性 *resellerId*。

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 元素名稱  | 必要 | 類型   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 是      | String | 訂用帳戶的顯示名稱。|
| `skuId` | 是      | String | Azure 方案的 SKU 識別碼。 針對 Microsoft Azure 方案類型的訂用帳戶，使用 0001 |
| `resellerId`      | 否       | String | 將與訂用帳戶建立關聯的轉銷商 MPN 識別碼。  |

在回應中，會傳回一個 `subscriptionCreationResult` 物件供您監視。 建立完訂用帳戶之後，`subscriptionCreationResult` 物件會傳回 `subscriptionLink` 物件。 其中包含訂用帳戶識別碼。

## <a name="next-steps"></a>後續步驟

- 若要檢視使用 .NET 建立 Enterprise 合約 (EA) 訂用帳戶的範例，請參閱 [GitHub 上的程式碼範例](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
