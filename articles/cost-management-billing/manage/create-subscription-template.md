---
title: 使用 Azure Resource Manager 範本以程式設計方式建立 Azure 訂用帳戶
description: 了解如何使用 Azure Resource Manager 範本以程式設計方式建立 Azure 訂用帳戶。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 21791b096d66b73c522c50f38cae7fd8a1b9fb70
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507552"
---
# <a name="programmatically-create-azure-subscriptions-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本以程式設計方式建立 Azure 訂用帳戶

本文可協助您使用 Azure Resource Manager 範本 (ARM 範本) 以程式設計方式建立 Azure 訂用帳戶。 Azure 客戶若使用下列合約類型的計費帳戶，則可使用 ARM 範本建立訂用帳戶：

- Enterprise 合約 (EA)
    - 如需詳細資訊，請參閱 [Azure 企業版 REST API](ea-portal-rest-apis.md)
- Microsoft 客戶合約 (MCA)
    - [Azure 計費 REST API](/rest/api/billing)
    - [Azure 訂用帳戶 API](/rest/api/subscription)
- Microsoft 合作夥伴合約 (MPA)
    - [Azure 計費 REST API](/rest/api/billing)
    - [Azure 訂用帳戶 API](/rest/api/subscription)

當您使用範本以程式設計方式建立 Azure 訂用帳戶時，該訂用帳戶會受合約規範 (您向 Microsoft 或授權經銷商取得 Azure 服務時所簽署的合約)。 如需詳細資訊，請參閱 [Microsoft Azure 法律資訊](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-using-arm-templates"></a>使用 ARM 範本建立訂用帳戶

您可以使用 Azure Resource Manager 範本 (ARM 範本) 建立訂用帳戶，以便將生產/測試部署程序自動化。 在下列範例中，您會使用 ARM 範本來建立 Azure 訂用帳戶和 Azure 資源群組。

## <a name="prerequisites"></a>Prerequisites

若要建立訂用帳戶，您必須具有下列其中一個角色： 

- 發票區段上的 Azure 訂用帳戶擁有者
- 發票區段上的 Azure 訂用帳戶參與者
- 發票區段上的 Azure 訂用帳戶建立者角色
- 帳單設定檔或計費帳戶上的 Azure 訂用帳戶擁有者
- 帳單設定檔或帳單帳戶上的 Azure 訂用帳戶參與者角色

如需詳細資訊，請參閱[訂用帳戶計費角色和工作](understand-mca-roles.md#subscription-billing-roles-and-tasks)。

此外，由於您將執行 ARM 範本部署，因此必須具有根物件的寫入權限。 若要在管理群組下建立 ARM 部署，您必須具有管理群組的寫入權限。 此動作純粹是用來建立 ARM 部署。 如果建立訂用帳戶，該訂用帳戶只會建立於 ARM 範本所指定的管理群組中。

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

請使用 `displayName` 屬性來識別您要為其建立訂用帳戶的計費帳戶。 請確定帳戶的 agreementType 是 MicrosoftCustomerAgreement。 複製帳戶的 `name`。 例如，若要建立 `Contoso` 計費帳戶的訂用帳戶，請複製 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 將此值貼在某處，以便您在下一個步驟中使用。

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

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-and-resource-group-with-a-template"></a>使用範本建立訂用帳戶和資源群組

下列 ARM 範本會針對「開發」發票區段建立名為「開發小組訂用帳戶」的訂用帳戶。 訂用帳戶的費用會計入「Contoso 帳單設定檔」帳單設定檔，並出現在其發票的「開發」區段中。 您可以使用在先前的步驟中複製的計費範圍：`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`。 

### <a name="request"></a>要求

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="request-body"></a>Request body

```json
{
  "properties":
    {
      "location": "westus",
      "properties": {
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {
            "uniqueAliasName": "sampleAlias"
          },
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "name": "sampleTemplate",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "variables": {
                    "uniqueAliasName": "sampleAlias"
                  },
                  "resources": [
                    {
                      "name": "[variables('uniqueAliasName')]",
                      "type": "Microsoft.Subscription/aliases",
                      "apiVersion": "2020-09-01",
                      "properties": {
                        "workLoad": "Production",
                        "displayName": "Dev Team subscription",
                        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                      },
                      "dependsOn": [],
                      "tags": {}
                    }
                  ],
                  "outputs": {
                    "subscriptionId": {
                      "type": "string",
                      "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                    }
                  }
                }
              }
            },
            {
              "name": "sampleOuterResource",
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "parameters": {
                  "subscriptionId": {
                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                  }
                },
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
                  "parameters": {
                    "subscriptionId": {
                      "type": "string"
                    }
                  },
                  "variables": {},
                  "resources": [
                    {
                      "name": "sampleInnerResource",
                      "type": "Microsoft.Resources/deployments",
                      "subscriptionId": "[parameters('subscriptionId')]",
                      "apiVersion": "2019-10-01",
                      "location": "westus",
                      "properties": {
                        "expressionEvaluationOptions": {
                          "scope": "inner"
                        },
                        "mode": "Incremental",
                        "parameters": {},
                        "template": {
                          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                          "contentVersion": "1.0.0.0",
                          "parameters": {},
                          "variables": {},
                          "resources": [
                            {
                              "type": "Microsoft.Resources/resourceGroups",
                              "apiVersion": "2020-05-01",
                              "location": "[deployment().location]",
                              "name": "sampleRG",
                              "properties": {},
                              "tags": {}
                            }
                          ],
                          "outputs": {}
                        }
                      }
                    }
                  ],
                  "outputs": {}
                }
              }
            }
          ],
          "outputs": {
            "messageFromLinkedTemplate": {
              "type": "string",
              "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
            }
          }
        },
        "mode": "Incremental"
      }
    }
}
```

### <a name="response"></a>回應

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

您可以取得部署狀態以監視進度。

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="response"></a>回應

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

在上述範例中，您可以看到建立的訂用帳戶為 `16edf959-11fd-48bb-9a46-85190963ead9`，而建立的 RG 為 `sampleRG`。

## <a name="next-steps"></a>後續步驟

* 由於您已建立訂用帳戶，您可以將這項功能授與其他使用者和服務主體。 如需詳細資訊，請參閱 [ Azure Enterprise 訂用帳戶 (預覽)](grant-access-to-create-subscription.md)。
* 若要深入了解如何使用管理群組來管理大量訂用帳戶，請參閱[使用 Azure 管理群組來組織資源](../../governance/management-groups/overview.md)。
