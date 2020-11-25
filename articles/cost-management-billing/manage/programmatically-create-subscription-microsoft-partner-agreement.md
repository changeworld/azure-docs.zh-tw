---
title: 使用最新的 API 以程式設計方式建立 Microsoft 合作夥伴合約的 Azure 訂用帳戶
description: 了解如何使用最新版本的 REST API、Azure CLI 和 Azure PowerShell，以程式設計方式建立 Microsoft 合作夥伴合約的 Azure 訂用帳戶。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ab5e51186fba7fc0dc8264026d314c32ce81d73f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94850877"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>使用最新的 API 以程式設計方式建立 Microsoft 合作夥伴合約的 Azure 訂用帳戶

本文可協助您使用最新的 API 版本，以程式設計方式建立 Microsoft 合作夥伴合約的 Azure 訂用帳戶。 如果您仍使用較舊的預覽版本，請參閱[使用預覽 API 以程式設計方式建立 Azure 訂用帳戶](programmatically-create-subscription-preview.md)。 

在本文中，可了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶。

當您以程式設計方式建立 Azure 訂用帳戶時，該訂用帳戶會受合約規範，此合約是您向 Microsoft 或授權經銷商取得 Azure 服務時所簽署的合約。 如需詳細資訊，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

在您組織的雲端解決方案提供者帳戶中，您必須是全域管理員或管理員代理者角色，才能為計費帳戶建立訂用帳戶。 如需詳細資訊，請參閱[合作夥伴中心 - 指派使用者角色和權限](/partner-center/permissions-overview)。

如果您不知道自己是否有 Microsoft 合作夥伴合約帳戶的存取權，請參閱[檢查 Microsoft 合作夥伴合約的存取權](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement)。

下列範例會使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>尋找您可以存取的計費帳戶

請提出下列要求，以列出您有權存取的所有計費帳戶。

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

API 回應會列出計費帳戶。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的計費帳戶。 請確定帳戶的 agreementType 是 MicrosoftPartnerAgreement。 複製帳戶的 `name`。 例如，若要建立 `Contoso` 計費帳戶的訂用帳戶，請複製 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 將此值貼在某處，以便您在下一個步驟中使用。

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts-MPA)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-customers-that-have-azure-plans"></a>尋找具有 Azure 方案的客戶

使用從第一個步驟複製的 `name` (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) 提出下列要求，以列出計費帳戶中您可以為其建立 Azure 訂用帳戶的所有客戶。

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

API 回應會列出計費帳戶中具有 Azure 方案的客戶。 您可以為這些客戶建立訂用帳戶。

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的客戶。 複製客戶的 `id`。 例如，若要建立 `Fabrikam toys` 的訂用帳戶，請複製 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 將此值貼到某個位置，以便在後續步驟中使用。

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getCustomers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="get-the-resellers-for-a-customer"></a>為客戶指定轉銷商

對於間接提供者，此節內容是選擇性的。

如果您是 CSP 兩層型模型中的間接提供者，您可以在為客戶建立訂用帳戶時指定轉銷商。

使用在第二個步驟中複製的 `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) 提出下列要求，以列出客戶適用的所有轉銷商。

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
API 回應會列出客戶適用的轉售商：

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

請使用 `description` 屬性來識別與訂用帳戶相關聯的轉銷商。 複製轉銷商的 `resellerId`。 例如，若要建立 `Wingtip` 的關聯，請複製 `3xxxxx`。 將此值貼在某處，以便您在下一個步驟中使用。

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getIndirectResellers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-a-customer"></a>為客戶建立訂用帳戶

下列範例會為 *Fabrikam toys* 建立名為「開發小組訂用帳戶」的訂用帳戶，並讓 *Wingtip* 轉銷商與訂用帳戶建立關聯。 您可以使用在先前的步驟中複製的計費範圍：`/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 

### <a name="rest"></a>[REST](#tab/rest-MPA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Request body

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>回應

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

在 API 的要求本文中，傳遞從第二個步驟中複製的選擇性 *resellerId*。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MPA)

若要安裝包含 `New-AzSubscriptionAlias` Cmdlet 的最新版模組，請執行 `Install-Module Az.Subscription`。 若要安裝最新版本的 PowerShellGet，請參閱[取得 PowerShellGet 模組](/powershell/scripting/gallery/installing-psget)。

使用計費範圍 `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`，執行下列 [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) 命令。 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

您可以從命令取得 subscriptionId 作為回應的一部分。

```azurepowershell
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

在 `New-AzSubscriptionAlias` 呼叫中，傳遞從第二個步驟中複製的選擇性 *resellerId*。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MPA)

首先，藉由執行 `az extension add --name account` 和 `az extension add --name alias` 來安裝延伸模組。

執行下列 [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) 命令。 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
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

在 `az account alias create` 呼叫中，傳遞從第二個步驟中複製的選擇性 *resellerId*。

---

## <a name="next-steps"></a>後續步驟

* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
