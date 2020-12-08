---
title: 從 Enterprise Reporting 移轉至 Azure Resource Manager API
description: 本文可協助您了解報告 API 與 Azure Resource Manager API 之間的差異、遷移至 Azure Resource Manager API 時會發生的情況，以及新的 Azure Resource Manager API 提供的新功能。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355809"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>從 Enterprise Reporting 移轉至 Azure Resource Manager API

本文可協助已建立自訂解決方案的開發人員使用[適用於企業客戶的 Azure Reporting API](../manage/enterprise-api.md) 遷移至適用於成本管理的 Azure Resource Manager API。 較新的 Azure Resource Manager API 服務主體支援現已正式推出。 Azure Resource Manager API 正持續進行開發。 請考慮遷移至此服務，而不是針對企業客戶使用舊版的 Azure Reporting API。 舊版 API 即將淘汰。 本文可協助您了解報告 API 與 Azure Resource Manager API 之間的差異、遷移至 Azure Resource Manager API 時會發生的情況，以及新的 Azure Resource Manager API 提供的新功能。

## <a name="api-differences"></a>API 差異

下列資訊說明適用於企業客戶的舊版報告 API 與較新的 Azure Resource Manager API 間的差異。

| **使用** | **Enterprise 合約 API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 驗證 | 在 Enterprise 合約 (EA) 入口網站中佈建的 API 金鑰 | 使用使用者權杖或服務主體的 Azure Active Directory (Azure AD) 驗證。 服務主體會取代 API 金鑰。 |
| 範圍和權限 | 所有要求都在註冊範圍。 API 金鑰權限指派會決定是否傳回整個註冊、部門或特定帳戶的資料。 無需使用者驗證。 | 系統會將註冊、部門或帳戶範圍的存取權指派給使用者或服務主體。 |
| URI 端點 | https://consumption.azure.com | https://management.azure.com |
| 開發狀態 | 處於維護模式。 即將淘汰。 | 正在開發中 |
| 可用的 API | 限制為目前可用的內容 | 可以使用對等 API 取代每個 EA API。 <p> 您也可以使用其他[成本管理 API](/rest/api/cost-management/)，包括： <p> <ul><li>預算</li><li>警示<li>匯出</li></ul> |

## <a name="migration-checklist"></a>移轉檢查清單

- 熟悉 [Azure Resource Manager REST API](/rest/api/azure)。
- 判斷您使用的 EA API，並查看要在 [EA API 對應到新的 Azure Resource Manager API](#ea-api-mapping-to-new-azure-resource-manager-apis) 時移動的 Azure Resource Manager API。
- 設定 Azure Resource Manager API 的服務授權和驗證
  - 如果您還沒有使用 Azure Resource Manager API，請[向 Azure AD 註冊您的用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)。 註冊會建立服務主體，供您用來呼叫 API。
  - 將服務主體存取權指派給所需的範圍，如下所述。
  - 將任何程式設計程式碼更新為使用 [Azure AD 驗證](/rest/api/azure/#create-the-request)搭配服務主體。
- 測試 API，然後更新所有程式設計程式碼，以 Azure Resource Manager API 呼叫來取代 EA API 呼叫。
- 將錯誤處理機制更新為使用新的錯誤碼。 考量項目包括：
  - Azure Resource Manager API 的逾時時間為 60 秒。
  - Azure Resource Manager API 已有速率限制。 如果超出速率，會導致 429 節流錯誤。 建置解決方案，讓您在短時間內不會執行過多的 API 呼叫。
- 檢閱其他透過 Azure Resource Manager 提供的成本管理 API，並評估稍後使用。 如需詳細資訊，請參閱[使用其他成本管理 API](#use-additional-cost-management-apis)。

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>將服務主體存取權指派給 Azure Resource Manager API

建立服務主體以程式設計方式呼叫 Azure Resource Manager API 後，您必須指派適當的權權，以在 Azure Resource Manager 中授權並執行要求。 有兩種權限架構適用於不同的案例。

### <a name="azure-billing-hierarchy-access"></a>Azure 計費階層存取

若要將服務主體權限指派給您的企業帳單帳戶、部門或註冊帳戶範圍，請使用[計費權限](/rest/api/billing/2019-10-01-preview/billingpermissions)、[計費角色定義](/rest/api/billing/2019-10-01-preview/billingroledefinitions)，以及 [計費角色指派](/rest/api/billing/2019-10-01-preview/billingroleassignments) API。

- 使用計費權限 API 來識別服務主體在指定範圍 (例如帳單帳戶或部門) 上已有的權限。
- 使用計費角色定義 API 來列舉可指派給服務主體的可用角色。
  - 目前只有唯讀的 EA 管理員和唯讀部門管理員角色可以指派給服務主體。
- 使用計費角色指派 API，將角色指派給您的服務主體。

下列範例示範如何呼叫角色指派 API，將服務主體存取權授與您的計費帳戶。 我們建議使用 [PostMan](https://postman.com) 來執行這些一次性權限組態。

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

新的服務主體支援延伸至 Azure 特定的範圍，例如管理群組、訂用帳戶和資源群組。 您可以直接[在 Azure 入口網站中](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)，或使用 [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role)，將服務主體權限指派給這些範圍。

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>新 Azure Resource Manager API 的 EA API 對應

使用下表找出您目前使用的 EA API，以及要改用的 Azure Resource Manager API 取代項目。

| **案例** | **EA API** | **Azure Resource Manager API** |
| --- | --- | --- |
| 餘額摘要 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| 價位表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – 談判價格 <p> [零售價格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) – 零售價格 |
| 保留執行個體詳細資料 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| 保留執行個體摘要 | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| 保留的執行個體建議 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 保留的執行個體費用 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>依 API 的移轉詳細資料

下列各節顯示包含新取代 API 範例的舊版 API 要求範例。

### <a name="balance-summary-api"></a>餘額摘要 API

呼叫新的餘額摘要 API 時，請使用下列要求 URI。 您的註冊號碼應該用來作為 billingAccountId。

#### <a name="supported-requests"></a>支援的要求

[取得註冊](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>回應主題變更

舊回應主體：

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

新回應主體：

在新 API 回應的 `properties` 欄位中，現在可以使用相同的資料。 某些欄位名稱的拼字可能會有些許變更。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>價位表

呼叫新的價位表 API 時，請使用下列要求 URI。

#### <a name="supported-requests"></a>支援的要求

 您可以使用下列範圍來呼叫 API：

- 註冊：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 訂用帳戶：`subscriptions/{subscriptionId}`

[取得目前計費週期](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[取得指定計費週期](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>回應主題變更

舊回應：

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

新回應：

在新 API 回應的 `pricesheets` 欄位中，現在可以使用舊資料。 同時也會提供計量詳細資料資訊。

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>保留執行個體使用量詳細資料

Microsoft 不會主動處理同步型的保留詳細資料 API。 建議您在遷移過程中，移至較新的非同步 API 呼叫模式 (支援 SPN)。 非同步要求會更妥善地處理大量資料，並減少逾時錯誤。

#### <a name="supported-requests"></a>支援的要求

呼叫新的非同步保留詳細資料 API 時，請使用下列要求 URI。 您的註冊號碼應該用來作為 `billingAccountId`。 您可以使用下列範圍來呼叫 API：

- 註冊：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>產生保留詳細資料報告的範例要求

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>輪詢報告產生狀態的範例要求

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>範例輪詢回應

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>回應主題變更

較舊的同步型保留詳細資料 API 的回應如下所示。

舊回應：

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

新回應：

新的 API 會為您建立 CSV 檔案。 查看以下欄位。

| **舊屬性** | **新屬性** | **注意事項** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | 執行個體彈性的新屬性。 |
|  | InstanceFlexibilityRatio | 執行個體彈性的新屬性。 |
| instanceId | InstanceName |  |
|  | 類型 | 這是新屬性。 值為 `None`、`Reservation` 或 `IncludedQuantity`。 |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>保留執行個體使用量摘要

使用下列要求 URI 來呼叫新的保留摘要 API。

#### <a name="supported-requests"></a>支援的要求

 使用下列範圍來呼叫 API：

- 註冊：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[取得每日保留摘要](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[取得每月保留摘要](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>回應主題變更

舊回應：

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

新回應：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>保留的執行個體建議

使用下列要求 URI 來呼叫新的保留建議 API。

#### <a name="supported-requests"></a>支援的要求

 使用下列範圍來呼叫 API：

- 註冊：`providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- 訂用帳戶：`subscriptions/{subscriptionId}`
- 資源群組：`subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[取得建議](/rest/api/consumption/reservationrecommendations/list)

您可透過此 API 取得共用和單一範圍的建議。 也可以將範圍篩選為選擇性 API 參數。

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>回應主題變更

共用和單一範圍的建議會合併成一個 API。

舊回應：

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

新回應：

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>保留的執行個體費用

使用下列要求 URI 來呼叫新的保留執行個體費用 API。

#### <a name="supported-requests"></a>支援的要求

[依日期範圍取得保留費用](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>回應主題變更

舊回應：

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
新回應：

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>使用額外的成本管理 API

遷移至現有報告案例的 Azure Resource Manager API 後，也可以使用許多其他 API。 您也可透過 Azure Resource Manager 取得 API，而且可以使用服務主體型的驗證來自動化。 以下是您可以使用的新功能快速摘要。

- [預算](/rest/api/consumption/budgets/createorupdate) - 用來設定閾值，以主動監視您的成本、警示相關的利害關係人，以及將動作自動化以回應閾值違規。

- [警示](/rest/api/cost-management/alerts) - 用來檢視警示資訊，包括 (但不限於) 預算警示、發票警示、信用額度警示和配額警示。

- [匯出](/rest/api/cost-management/exports) - 用來排程費用的週期資料並匯出至您選擇的 Azure 儲存體帳戶。 這是建議的解決方案，適用於大量使用 Azure，且想要分析其資料並在自己的內部系統中使用的客戶。

## <a name="next-steps"></a>後續步驟

- 熟悉 [Azure Resource Manager REST API](/rest/api/azure)。
- 若需要，請判斷您使用的 EA API，並查看要在 [EA API 對應到新的 Azure Resource Manager API](#ea-api-mapping-to-new-azure-resource-manager-apis) 時移動的 Azure Resource Manager API。
- 如果您還沒有使用 Azure Resource Manager API，請[向 Azure AD 註冊您的用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 若需要，請將所有程式設計程式碼更新為使用 [Azure AD 驗證](/rest/api/azure/#create-the-request)搭配服務主體。