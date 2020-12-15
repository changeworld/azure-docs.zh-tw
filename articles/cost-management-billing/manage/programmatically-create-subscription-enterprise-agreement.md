---
title: 使用最新的 API 以程式設計方式建立 Azure Enterprise 合約訂用帳戶
description: 了解如何使用最新版本的 REST API、Azure CLI 和 Azure PowerShell，以程式設計方式建立 Azure Enterprise 合約訂用帳戶。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 0cdd25b2937dd1fb2cc70ef7b1c5a9e9ddaef375
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780598"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>使用最新的 API 以程式設計方式建立 Azure Enterprise 合約訂用帳戶

本文可協助您使用最新的 API 版本，以程式設計方式建立 EA 計費帳戶的 Azure Enterprise 合約 (EA) 訂用帳戶。 如果您仍使用較舊的預覽版本，請參閱[使用預覽 API 以程式設計方式建立 Azure 訂用帳戶](programmatically-create-subscription-preview.md)。 

在本文中，可了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶。

當您以程式設計方式建立 Azure 訂用帳戶時，該訂用帳戶會受合約規範，此合約是您向 Microsoft 或授權經銷商取得 Azure 服務時所簽署的合約。 如需詳細資訊，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

您必須擁有註冊帳戶的「擁有者」角色，才能建立訂用帳戶。 有兩種方式可取得此角色：

* 您註冊中的 Enterprise 管理員可以[讓您成為帳戶擁有者](https://ea.azure.com/helpdocs/addNewAccount) (需登入)，這可讓您成為註冊帳戶的擁有者。
* 註冊帳戶的現有擁有者可以[授與您存取權](grant-access-to-create-subscription.md)。 同樣地，若要使用服務主體來建立 EA 訂用帳戶，您必須[授與該服務主體建立訂用帳戶的能力](grant-access-to-create-subscription.md)。

## <a name="find-accounts-you-have-access-to"></a>尋找您可以存取的帳戶

當您加入與「帳戶擁有者」相關聯的註冊帳戶之後，Azure 會使用帳戶與註冊的關聯性來判斷由誰支付訂用帳戶的費用。 所有在該帳戶下所建立訂用帳戶的費用，都會由該帳戶所在的 EA 註冊支付。 若要建立訂用帳戶，您必須傳入註冊帳戶與使用者主體的相關值，才能擁有訂用帳戶。

若要執行下列命令，您必須登入「帳戶擁有者」的 *主目錄* (建立訂用帳戶時預設的所在目錄)。

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

要求列出您有權存取的所有註冊帳戶：

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API 回應會列出您有權存取的所有註冊帳戶：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

計費範圍和 `id` 的值相同。 註冊帳戶的 `id` 是起始訂用帳戶要求的計費範圍。 請務必記下識別碼，因為您稍後在建立訂用帳戶時會用到此必要參數。

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定的註冊帳戶下建立訂用帳戶

下列範例會在上一個步驟中選取的註冊帳戶中，建立名為「開發小組訂用帳戶」的訂用帳戶。 

### <a name="rest"></a>[REST](#tab/rest-EA)

呼叫 PUT API 以建立訂用帳戶建立要求/別名。

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

在要求本文中，提供您其中一個 `enrollmentAccounts` 的 `id` 作為 `billingScope`。

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

`Workload` 的允許值為 `Production` 和 `DevTest`。

#### <a name="response"></a>回應

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

您可以在相同的 URL 上執行 GET，以取得要求的狀態。

### <a name="request"></a>要求

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>回應

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

此時會傳回進行中狀態，在 `provisioningState` 下顯示為 `Accepted` 狀態。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

若要安裝包含 `New-AzSubscriptionAlias` Cmdlet 的最新版模組，請執行 `Install-Module Az.Subscription`。 若要安裝最新版本的 PowerShellGet，請參閱[取得 PowerShellGet 模組](/powershell/scripting/gallery/installing-psget)。

使用計費範圍 `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`，執行下列 [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) 命令。 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

您可以從命令取得 subscriptionId 作為回應的一部分。

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

首先，藉由執行 `az extension add --name account` 和 `az extension add --name alias` 來安裝延伸模組。

執行下列 [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) 命令，並從您的其中一個 `enrollmentAccounts` 提供 `billing-scope` 和 `id`。 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

您可以從命令取得 subscriptionId 作為回應的一部分。

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Azure 企業版訂用帳戶建立 API 的限制

- 使用此 API 只能建立「Azure 企業版」訂用帳戶。
- 每一註冊帳戶僅限 2000 個訂用帳戶。 之後，您只能在 Azure 入口網站中為帳戶建立更多訂用帳戶。 若要透過此 API 建立更多訂用帳戶，請建立另一個註冊帳戶。 已取消、已刪除和已轉移的訂用帳戶會計入 2000 筆的限制。
- 不是「帳戶擁有者」但已透過 Azure RBAC 新增至註冊帳戶的使用者，並無法在 Azure 入口網站中建立訂用帳戶。
- 您無法選取要作為訂用帳戶建立位置的租用戶。 訂用帳戶一律會建立在「帳戶擁有者」的主租用戶中。 若要將訂用帳戶移至不同的租用戶，請參閱[變更訂用帳戶租用戶](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。


## <a name="next-steps"></a>後續步驟

* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
