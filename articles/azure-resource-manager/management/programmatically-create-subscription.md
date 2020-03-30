---
title: 以程式設計方式建立 Azure 訂用帳戶
description: 瞭解如何以程式設計方式創建其他 Azure 訂閱。
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460393"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>以程式設計方式創建 Azure 訂閱（預覽）

具有[企業協定 （EA）、Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)[客戶協定 （MCA）](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)或[Microsoft 合作夥伴協定 （MPA）](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)計費帳戶的 Azure 客戶可以以程式設計方式創建訂閱。 在本文中，可了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶。

以程式設計方式創建 Azure 訂閱時，該訂閱受從 Microsoft 或授權轉銷商處獲取 Azure 服務的協定的約束。 若要深入了解，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>為 EA 計費帳戶創建訂閱

使用以下部分中的資訊創建 EA 訂閱。

### <a name="prerequisites"></a>Prerequisites

您必須在註冊帳戶上具有擁有者角色才能創建訂閱。 有兩種方法可以獲取角色：

* 註冊的企業管理員可以[為您創建帳戶擁有者](https://ea.azure.com/helpdocs/addNewAccount)（需要登錄），從而使其成為註冊帳戶的擁有者。

* 註冊帳戶的現有擁有者可以[授與您存取權](grant-access-to-create-subscription.md)。 同樣，如果要使用服務主體創建 EA 訂閱，則必須[授予該服務主體創建訂閱的功能](grant-access-to-create-subscription.md)。

### <a name="find-accounts-you-have-access-to"></a>尋找您可以存取的帳戶

將添加到與帳戶擁有者關聯的註冊帳戶後，Azure 將使用帳戶到註冊關係來確定在何處計費訂閱費用。 在帳戶下創建的所有訂閱都向該帳戶的 EA 註冊計費。 若要建立訂用帳戶，您必須傳入註冊帳戶與使用者主體的相關值，才能擁有訂用帳戶。

若要執行下列命令，您必須登入「帳戶擁有者」的*主目錄* (建立訂用帳戶時預設的所在目錄)。

### <a name="rest"></a>[休息](#tab/rest)

請求列出您有權訪問的所有註冊帳戶：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 回應列出了您有權訪問的所有註冊帳戶：

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

使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 `name`複製該帳戶。 例如，如果要在SignUpEngineering@contoso.com註冊帳戶下創建訂閱，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此識別碼是註冊帳戶的物件識別碼。 將此值粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

打開[Azure 雲外殼](https://shell.azure.com/)並選擇 PowerShell。

使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) Cmdlet 來列出您可以存取的所有註冊帳戶。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 使用有權訪問的註冊帳戶清單進行回應：

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 `ObjectId`複製該帳戶。 例如，如果要在SignUpEngineering@contoso.com註冊帳戶下創建訂閱，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 將此物件識別碼 粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令來列出您可以存取的所有註冊帳戶。

```azurecli-interactive
az billing enrollment-account list
```

Azure 使用有權訪問的註冊帳戶清單進行回應：

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

使用 `principalName` 屬性來識別要為訂用帳戶付費的帳戶。 `name`複製該帳戶。 例如，如果要在SignUpEngineering@contoso.com註冊帳戶下創建訂閱，則可以複製```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此識別碼是註冊帳戶的物件識別碼。 將此值粘貼到某處，以便在下一步中將其用作`enrollmentAccountObjectId`。

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定的註冊帳戶下建立訂用帳戶

以下示例在上一步驟中選擇的註冊帳戶中創建名為*Dev Team 訂閱的訂閱*。 訂閱產品是*MS-AZR-0017P（* 常規微軟企業協定）。 這也會依選擇新增兩個使用者作為該訂用帳戶的「RBAC 擁有者」。

### <a name="rest"></a>[休息](#tab/rest)

提出下列要求，並以從第一個步驟複製的 `name` 取代 `<enrollmentAccountObjectId>` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果要指定擁有者，請[瞭解如何獲取使用者物件 I。](grant-access-to-create-subscription.md#userObjectId)

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

| 元素名稱  | 必要 | 類型   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | String | 訂用帳戶的顯示名稱。 如果未指定，會設定為供應項目的名稱，例如「Microsoft Azure 企業版」。                                 |
| `offerType`   | 是      | String | 訂用帳戶的供應項目方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `owners`      | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |

在回應中，會傳回一個 `subscriptionOperation` 物件供您監視。 建立完訂用帳戶之後，`subscriptionOperation` 物件會傳回 `subscriptionLink` 物件，其中包含訂用帳戶 ID。

### <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

首先，通過運行`Install-Module Az.Subscription -AllowPrerelease`安裝此預覽模組。 若要確認 `-AllowPrerelease` 是否可運作，請從[取得 PowerShellGet 模組](/powershell/scripting/gallery/installing-psget)安裝最新版的 PowerShellGet。

在下面運行[New-Az 訂閱](/powershell/module/az.subscription)命令`<enrollmentAccountObjectId>`，替換為`ObjectId`第一步 （）```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```中收集的命令。 如果要指定擁有者，請[瞭解如何獲取使用者物件 I。](grant-access-to-create-subscription.md#userObjectId)

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 元素名稱  | 必要 | 類型   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 否      | String | 訂用帳戶的顯示名稱。 如果未指定，會設定為供應項目的名稱，例如「Microsoft Azure 企業版」。                                 |
| `OfferType`   | 是      | String | 訂用帳戶的供應項目方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `EnrollmentAccountObjectId`      | 是       | String | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 此值是您從 `Get-AzEnrollmentAccount` 取得的 GUID。 |
| `OwnerObjectId`      | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |
| `OwnerSignInName`    | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `OwnerObjectId`。 使用此參數時，服務主體必須具有[目錄的讀取權限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。|

若要查看所有參數的完整清單，請參閱 [New-AzSubscription](/powershell/module/az.subscription)。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，通過運行`az extension add --name subscription`安裝此預覽擴展。

在下面運行[az 帳戶創建](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)命令`<enrollmentAccountObjectId>`，替換為`name`第一步 （）```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```中複製的命令。 如果要指定擁有者，請[瞭解如何獲取使用者物件 I。](grant-access-to-create-subscription.md#userObjectId)

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名稱  | 必要 | 類型   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 否      | String | 訂用帳戶的顯示名稱。 如果未指定，會設定為供應項目的名稱，例如「Microsoft Azure 企業版」。                                 |
| `offer-type`   | 是      | String | 訂用帳戶的供應項目方案。 EA 的兩個選項為 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (生產環境使用) 和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (開發/測試環境使用，必須[使用 EA 入口網站來開啟](https://ea.azure.com/helpdocs/DevOrTestOffer))。                |
| `enrollment-account-object-id`      | 是       | String | 用來建立訂用帳戶並加以收費的註冊帳戶物件識別碼。 此值是您從 `az billing enrollment-account list` 取得的 GUID。 |
| `owner-object-id`      | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的「物件識別碼」。  |
| `owner-upn`    | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之使用者的電子郵件地址。 您可以使用此參數來取代 `owner-object-id`。|
| `owner-spn` | 否       | String | 在建立訂用帳戶之後，任何您想要在該訂用帳戶上新增為「RBAC 擁有者」之服務主體的「應用程式識別碼」。 您可以使用此參數來取代 `owner-object-id`。 使用此參數時，服務主體必須具有[目錄的讀取權限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。|

若要查看所有參數的完整清單，請參閱 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)。

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 企業版訂用帳戶建立 API 的限制

- 使用此 API 只能建立「Azure 企業版」訂用帳戶。
- 每個註冊帳戶有 500 個訂閱的限制。 之後，只能在 Azure 門戶中創建帳戶的更多訂閱。 如果要通過 API 創建更多訂閱，請創建另一個註冊帳戶。
- 不是帳戶擁有者但通過 RBAC 添加到註冊帳戶的使用者無法在 Azure 門戶中創建訂閱。
- 您無法選取要作為訂用帳戶建立位置的租用戶。 訂用帳戶一律會建立在「帳戶擁有者」的主租用戶中。 若要將訂用帳戶移至不同的租用戶，請參閱[變更訂用帳戶租用戶](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。


## <a name="create-subscriptions-for-an-mca-account"></a>為 MCA 帳戶創建訂閱

### <a name="prerequisites"></a>Prerequisites

您必須在發票部分或帳單設定檔或帳單帳戶上具有擁有者、參與者或 Azure 訂閱建立者角色才能創建訂閱。 如需詳細資訊，請參閱[訂用帳戶計費角色和工作](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks)。

下面顯示的示例使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

### <a name="find-billing-accounts-that-you-have-access-to"></a>查找您有權訪問的帳單帳戶

發出以下請求以列出所有計費帳戶。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 回應列出了您有權訪問的計費帳戶。

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
使用`displayName`屬性標識要為其創建訂閱的帳單帳戶。 確保帳戶的同意類型為*Microsoft 客戶協定*。 複製帳戶`name`。  例如，如果要為`Contoso`帳單帳戶創建訂閱，則複製 。 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="find-invoice-sections-to-create-subscriptions"></a>查找發票部分以創建訂閱

訂閱費用將顯示在帳單設定檔發票的一節中。 使用以下 API 獲取具有創建 Azure 訂閱許可權的發票部分和帳單設定檔的清單。

提出下列要求，並以從第一個步驟複製的 `name` 取代 `<billingAccountName>` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)。

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API 回應列出了您有權訪問創建訂閱的所有發票部分及其計費設定檔：

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

使用`invoiceSectionDisplayName`屬性標識要為其創建訂閱的發票部分。 複製`invoiceSectionId`和`billingProfileId`發票 部分`skuId`的 一個 。 例如，如果要`Microsoft Azure plan`為`Development`發票節創建類型的訂閱，則複製`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`和`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx`。 `0001` 將這些值粘貼到某處，以便在下一步中使用它們。

### <a name="create-a-subscription-for-an-invoice-section"></a>為發票部分創建訂閱

下面的示例為開發發票部分創建名為*開發團隊訂閱*的*訂閱*，該訂閱類型為 Microsoft *Azure 計畫*。 訂閱將計費到*Contoso 財務的*帳單設定檔，並顯示在其發票的 *"開發*"部分。

發出以下請求，`<invoiceSectionId>`替換為從第二步`invoiceSectionId`（）```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```複製的 。 您需要傳遞`billingProfileId`和`skuId`從 API 的請求參數中的第二步複製。 如果要指定擁有者，請[瞭解如何獲取使用者物件 I。](grant-access-to-create-subscription.md#userObjectId)

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
| `billingProfileId`   | 是      | String | 將針對訂閱費用計費的帳單設定檔的 ID。  |
| `skuId` | 是      | String | 確定 Azure 計畫類型的 sku ID。 |
| `owners`      | 否       | String | 要在創建訂閱時作為 RBAC 擁有者添加的任何使用者或服務主體的物件識別碼。  |
| `costCenter` | 否      | String | 與訂閱關聯的成本中心。 它顯示在使用方式 csv 檔中。 |
| `managementGroupId` | 否      | String | 要向其添加訂閱的管理組的 ID。 要獲取管理組的清單，請參閱[管理組 - 清單 API](/rest/api/resources/managementgroups/list)。 使用 API 中的管理組的 ID。 |

在回應中，會傳回一個 `subscriptionCreationResult` 物件供您監視。 建立完訂用帳戶之後，`subscriptionCreationResult` 物件會傳回 `subscriptionLink` 物件，其中包含訂用帳戶 ID。

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>為 MPA 計費帳戶創建訂閱

### <a name="prerequisites"></a>Prerequisites

您必須在組織的雲解決方案供應商帳戶中具有全域管理員或管理員代理角色，才能為帳單帳戶創建訂閱。 有關詳細資訊，請參閱[合作夥伴中心 - 分配使用者角色和許可權](https://docs.microsoft.com/partner-center/permissions-overview)。

下面顯示的示例使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>查找您有權訪問的計費帳戶

發出以下請求以列出您有權訪問的所有計費帳戶。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 回應列出計費帳戶。

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
使用`displayName`屬性標識要為其創建訂閱的帳單帳戶。 確保帳戶的同意類型是*Microsoft 合作夥伴協定*。 複製`name`帳戶 的 。 例如，如果要為`Contoso`帳單帳戶創建訂閱，則複製 。 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="find-customers-that-have-azure-plans"></a>查找具有 Azure 計畫的客戶

發出以下請求，`<billingAccountName>`替換為從第一步`name`（```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```） 中複製的，以列出計費帳戶中的所有客戶，您可以為其創建 Azure 訂閱。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 回應列出具有 Azure 計畫的計費帳戶中的客戶。 您可以為這些客戶創建訂閱。

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

使用`displayName`屬性標識要為其創建訂閱的客戶。 複製`id`客戶。 例如，如果要為`Fabrikam toys`創建 訂閱，請複製`/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 將此值粘貼到某處，以在後續步驟中使用它。

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>間接供應商可選：為客戶獲取轉銷商

如果您是 CSP 兩層模型中的間接供應商，則可以在為客戶創建訂閱時指定轉銷商。

發出以下請求，`<customerId>`替換為從第二步`id`（```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```） 中複製的，以列出可供客戶使用的所有轉銷商。

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 回應列出了客戶的轉銷商：

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
使用`description`屬性標識將與訂閱關聯的轉銷商。 複製轉銷商`resellerId`的 。 例如，如果要關聯`Wingtip`，則複製 。 `3xxxxx` 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="create-a-subscription-for-a-customer"></a>為客戶創建訂閱

下面的示例為*Fabrikam 玩具*創建名為*Dev Team 訂閱*的訂閱，並將*Wingtip*轉銷商關聯到訂閱。 T

發出以下請求，`<customerId>`替換為從第二步`id`（）```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```複製的 。 傳遞從 API 請求參數中的第二步複製的可選*轉銷商 Id。*

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
| `skuId` | 是      | String | Azure 計畫的 sKU ID。 對 Microsoft Azure 計畫類型的訂閱使用*0001* |
| `resellerId`      | 否       | String | 與訂閱關聯的轉銷商的 MPN ID。  |

在回應中，會傳回一個 `subscriptionCreationResult` 物件供您監視。 建立完訂用帳戶之後，`subscriptionCreationResult` 物件會傳回 `subscriptionLink` 物件，其中包含訂用帳戶 ID。

## <a name="next-steps"></a>後續步驟

* 有關使用 .NET 創建企業協定 （EA） 訂閱的示例，請參閱[GitHub 上的示例代碼](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 要瞭解有關使用管理組管理大量訂閱的更多資訊，請參閱使用[Azure 管理組組織資源](../../governance/management-groups/overview.md)
