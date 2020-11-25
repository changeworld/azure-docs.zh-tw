---
title: 使用最新的 API 以程式設計方式建立 Microsoft 客戶合約的 Azure 訂用帳戶
description: 了解如何使用最新版本的 REST API、Azure CLI 和 Azure PowerShell，以程式設計方式建立 Microsoft 客戶合約的 Azure 訂用帳戶。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1b5fbd5ee2b8db784f4e35a37fe144cbe2c0abeb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94850880"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>使用最新的 API 以程式設計方式建立 Microsoft 客戶合約的 Azure 訂用帳戶

本文可協助您使用最新的 API 版本，以程式設計方式建立 Microsoft 客戶合約的 Azure 訂用帳戶。 如果您仍使用較舊的預覽版本，請參閱[使用預覽 API 以程式設計方式建立 Azure 訂用帳戶](programmatically-create-subscription-preview.md)。 

在本文中，可了解如何使用 Azure Resource Manager 以程式設計方式建立訂用帳戶。

當您以程式設計方式建立 Azure 訂用帳戶時，該訂用帳戶會受合約規範，此合約是您向 Microsoft 或授權經銷商取得 Azure 服務時所簽署的合約。 如需詳細資訊，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

您必須是發票區段上的擁有者、參與者或 Azure 訂用帳戶角色，或是帳單設定檔或計費帳戶上的擁有者或參與者角色，才能建立訂用帳戶。 如需詳細資訊，請參閱[訂用帳戶計費角色和工作](understand-mca-roles.md#subscription-billing-roles-and-tasks)。

如果您不知道自己是否有 Microsoft 客戶合約帳戶的存取權，請參閱[檢查 Microsoft 客戶合約的存取權](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement)。

下列範例會使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

## <a name="find-billing-accounts-that-you-have-access-to"></a>尋找您可以存取的計費帳戶

請提出下列要求，以列出所有計費帳戶。

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
API 回應會列出您有權存取的計費帳戶。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的計費帳戶。 請確定帳戶的 agreementType 是 MicrosoftCustomerAgreement。 複製帳戶的 `name`。  例如，若要建立 `Contoso` 計費帳戶的訂用帳戶，請複製 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 將此值貼在某處，以便您在下一個步驟中使用。

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>尋找帳單設定檔和發票區段以建立訂用帳戶

訂用帳戶的費用將會顯示在帳單設定檔的發票區段。 請使用下列 API 來取得您有權在其中建立 Azure 訂用帳戶的帳單設定檔和發票區段清單。

首先，您應在您可存取的計費帳戶下取得帳單設定檔的清單。

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
API 回應會列出所有您有權在其中建立訂用帳戶的帳單設定檔：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 複製 `id`，以便後續識別帳單設定檔下的發票區段。 例如，複製 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` 並呼叫下列 API。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>回應

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

請使用 `id` 屬性來識別您要為其建立訂用帳戶的發票區段。 請複製整個字串。 例如 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`。 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-an-invoice-section"></a>為發票區段建立訂用帳戶

下列範例會針對「開發」發票區段建立名為「開發小組訂用帳戶」的訂用帳戶。 訂用帳戶的費用會計入「Contoso 帳單設定檔」帳單設定檔，並出現在其發票的「開發」區段中。 您可以使用在先前的步驟中複製的計費範圍：`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`。 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Request body

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

若要安裝包含 `New-AzSubscriptionAlias` Cmdlet 的最新版模組，請執行 `Install-Module Az.Subscription`。 若要安裝最新版本的 PowerShellGet，請參閱[取得 PowerShellGet 模組](/powershell/scripting/gallery/installing-psget)。

執行下列 [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) 和計費範圍 `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`。 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MCA)

首先，藉由執行 `az extension add --name account` 和 `az extension add --name alias` 來安裝延伸模組。

執行下列 [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) 命令。

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="next-steps"></a>後續步驟

* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
