---
title: 將 EA 遷移至 Microsoft 客戶合約 Api-Azure
description: 本文可協助您瞭解將 Microsoft Enterprise 合約（EA）遷移至 Microsoft 客戶合約的後果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: b0c30cd35efd598767d21dbdafc5100d8896380c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514709"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>從 Enterprise 合約遷移至 Microsoft 客戶合約 Api

本文可協助您瞭解 Enterprise 合約（EA）與 Microsoft 客戶合約（MCA）帳戶之間的資料結構、API 和其他系統整合差異。 Azure 成本管理支援這兩種帳戶類型的 Api。 請先參閱 Microsoft 客戶合約的[安裝帳單帳戶](../manage/mca-setup-account.md)一文，再繼續進行。

具有現有 EA 帳戶的組織應該與設定 MCA 帳戶一起檢查這篇文章。 先前，更新 EA 帳戶需要一些最少的工作，才能從舊的註冊移至新的註冊。 不過，遷移至 MCA 帳戶需要額外的人力。 額外的工作是因為基礎計費子系統中的變更，這會影響所有成本相關的 Api 和服務供應專案。

## <a name="mca-apis-and-integration"></a>MCA Api 和整合

MCA Api 和新的整合可讓您：

- 透過原生 Azure Api 提供完整的 API 可用性。
- 在單一帳單帳戶中設定多個發票。
- 使用 Azure 服務使用量、協力廠商 Marketplace 使用量和 Marketplace 購買來存取結合的 API。
- 使用 Azure 成本管理來查看帳單設定檔（與註冊相同）的成本。
- 存取新的 Api 以顯示成本、在成本超過預先定義的臨界值時收到通知，並自動匯出原始資料。

## <a name="migration-checklist"></a>遷移檢查清單

下列專案可協助您轉換至 MCA Api。

- 熟悉新的[Microsoft 客戶合約帳單帳戶](../understand/mca-overview.md)。
- 判斷您使用的 Api，並查看下一節中已取代的 Api。
- 熟悉[AZURE RESOURCE MANAGER REST api](/rest/api/azure)。
- 如果您還沒有使用 Azure Resource Manager Api，請向[Azure AD 註冊您的用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 更新任何程式設計程式碼，以[使用 Azure AD 驗證](/rest/api/azure/#create-the-request)。
- 更新任何程式設計程式碼，以使用 MCA API 呼叫來取代 EA API 呼叫。
- 更新錯誤處理以使用新的錯誤碼。
- 查看其他的整合供應專案，例如 Cloudyn 和 Power BI，以進行其他所需的動作。

## <a name="ea-apis-replaced-with-mca-apis"></a>以 MCA Api 取代的 EA Api

EA Api 會使用 API 金鑰來進行驗證和授權。 MCA Api 會使用 Azure AD 驗證。

| 目的 | EA API | MCA API |
| --- | --- | --- |
| 餘額與信用額度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft 帳單/billingAccounts/billingProfiles/availableBalanceussae |
| 使用方式（JSON） | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft. 耗用量/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用方式（CSV） | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. 耗用量/usageDetails/下載](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 使用量（CSV） | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. 耗用量/usageDetails/下載](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 計費期間 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft 帳單/billingAccounts/billingProfiles/發票 |
| 價位表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft 帳單/billingAccounts/billingProfiles/pricesheet/default/下載格式 = json|csv Microsoft 帳單/billingAccounts/.../billingProfiles/.../發票/... /pricesheet/default/download 格式 = json|csv Microsoft 帳單/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 保留購買 | [/reservatioNcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft 帳單/billingAccounts/billingProfiles/交易 |
| 保留建議 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. 耗用量/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 保留使用量 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [ReservationDetails](/rest/api/consumption/reservationsdetails)[的 Microsoft. 耗用量/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure 服務和協力廠商 Marketplace 使用量適用于[使用量詳細資料 API](/rest/api/consumption/usagedetails)。

以下是 MCA 計費帳戶可用的 Api：

| 目的 | Microsoft 客戶合約（MCA） API |
| --- | --- |
| 帳單帳戶<sup>2</sup> | Microsoft 帳單/billingAccounts |
| 帳單設定檔<sup>2</sup> | Microsoft 帳單/billingAccounts/billingProfiles |
| 發票區段<sup>2</sup> | Microsoft 帳單/billingAccounts/invoiceSections |
| 發票 | Microsoft 帳單/billingAccounts/billingProfiles/發票 |
| 帳單訂閱 | {scope}/billingSubscriptions |

<sup>2</sup>個 api 會傳回物件的清單，這些是範圍，Azure 入口網站和 api 中的成本管理體驗都會在其中運作。 如需成本管理範圍的詳細資訊，請參閱[瞭解並使用範圍](understand-work-scopes.md)。

如果您使用任何現有的 EA Api，您必須更新它們以支援 MCA 帳單帳戶。 下表顯示其他整合變更：

| 目的 | 舊提供 | 新增供應專案 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 使用量見解](/power-bi/desktop-connect-azure-consumption-insights)內容套件和連接器 |  [Azure 使用量見解連接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>取得餘額和信用額度的 Api

[取得餘額摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)API 會提供您每月的摘要：

- 餘額
- 新購買
- Azure Marketplace 服務費用
- 調整
- 服務超額費用

所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。

取得餘額摘要 API 會由 Microsoft 帳單/billingAccounts/billingProfiles/availableBalance API 取代。

若要使用可用的餘額 API 取得可用餘額：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>取得成本和使用量的 Api

使用下列 Api，取得 Azure 服務使用量、協力廠商 Marketplace 使用量和其他 Marketplace 購買費用的每日明細。 已針對 Azure 服務和協力廠商 Marketplace 使用方式合併下列個別的 Api。 舊的 Api 會由 Microsoft 取用[/usageDetails](/rest/api/consumption/usagedetails) api 取代。 它會新增 Marketplace 購買，先前只會在餘額摘要中顯示到目前為止。

- [取得使用量詳細資料/下載](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用量詳細資料/提交](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用量詳細資料/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得使用量詳細資料/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得 marketplace 商店費用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [取得 marketplace 商店費用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。

所有先前的 Api 都會由取用/使用量詳細資料 API 取代。

使用使用量詳細資料 API 取得使用量詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

使用量詳細資料 API 與所有成本管理 Api 一樣，可以在多個範圍中使用。 針對已開票的成本，如同傳統上在註冊層級收到的費用，請使用帳單設定檔範圍。  如需成本管理範圍的詳細資訊，請參閱[瞭解並使用範圍](understand-work-scopes.md)。

| 類型 | 識別碼格式 |
| --- | --- |
| 計費帳戶 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 帳單設定檔 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 訂閱 | `/subscriptions/{subscriptionId}` |
| 資源群組 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

使用下列 querystring 參數來更新任何程式設計程式碼。

| 舊參數 | 新參數 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支援 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

回應的主體也會變更。

舊的回應主體：

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

新的回應主體：

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

屬性名稱，其中包含從資料變更為_值_的使用量記錄陣列。 每筆記錄都用來擁有詳細屬性的一般清單。 不過，每一筆記錄現在都會在名為_properties_的嵌套屬性中，但標記除外。 新的結構與其他 Azure Api 一致。 某些屬性名稱已變更。 下表顯示對應的屬性。

| 舊屬性 | 新增屬性 | 注意 |
| --- | --- | --- |
| AccountId | N/A | 不會追蹤訂用帳戶建立者。 使用 invoiceSectionId （與 departmentId 相同）。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | N/A | 不會追蹤訂用帳戶建立者。 使用 invoiceSectionName （與 departmentName 相同）。 |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 請注意，這些屬性是相反。 如果 isAzureCreditEnabled 為 true，則 ChargesBilledSeparately 會是 false。 |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 確切的字串值可能會不同。 |
| ConsumedServiceId | 無 | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 日期和 usageStartDate | date | &nbsp;  |
| 天 | 無 | 剖析日期的日。 |
| DepartmentId | invoiceSectionId | 確切的值不同。 |
| DepartmentName | invoiceSectionName | 確切的字串值可能會不同。 如有需要，設定發票區段以符合部門。 |
| ExtendedCost 和成本 | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 為週期性費用 | 無 | &nbsp;  |
| 位置 | location | &nbsp;  |
| MeterCategory | meterCategory | 確切的字串值可能會不同。 |
| 計量識別碼 | meterId | 確切的字串值不同。 |
| MeterName | meterName | 確切的字串值可能會不同。 |
| MeterRegion | meterRegion | 確切的字串值可能會不同。 |
| MeterSubCategory | meterSubCategory | 確切的字串值可能會不同。 |
| 月 | 無 | 從日期剖析月份。 |
| 優惠名稱 | 無 | 使用 publisherName 和 productOrderName。 |
| OfferID | 無 | &nbsp;  |
| 訂單編號 | 無 | &nbsp;  |
| PartNumber | 無 | 使用 meterId 和 productOrderName 來唯一識別價格。 |
| 方案名稱 | productOrderName | &nbsp;  |
| 產品 | 產品 |   |
| ProductId | productId | 確切的字串值不同。 |
| 發行者名稱 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 確切的字串值不同。 |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | 無 | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 確切的字串值可能會不同。 |
| ServiceTier | meterSubCategory | 確切的字串值可能會不同。 |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 標籤 | tags | [標記] 屬性會套用至根物件，而非 [嵌套屬性] 屬性。 |
| UnitOfMeasure | unitOfMeasure | 確切的字串值不同。 |
| usageEndDate | date | &nbsp;  |
| 年 | 無 | 剖析日期的年份。 |
| (新增) | billingCurrency | 用於收費的貨幣。 |
| (新增) | billingProfileId | 帳單設定檔的唯一識別碼（與註冊相同）。 |
| (新增) | billingProfileName | 帳單設定檔的名稱（與註冊相同）。 |
| (新增) | chargeType | 使用來區分 Azure 服務使用量、Marketplace 使用量和購買。 |
| (新增) | invoiceId | 發票的唯一識別碼。 空白表示目前的、開啟的月份。 |
| (新增) | publisherType | 購買的發行者類型。 空白表示使用方式。 |
| (新增) | serviceFamily | 購買的類型。 空白表示使用方式。 |
| (新增) | servicePeriodEndDate | 已購買服務的結束日期。 |
| (新增) | servicePeriodStartDate | 已購買服務的開始日期。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>計費週期 API 已由發票 API 取代

MCA 計費帳戶不會使用計費期間。 相反地，他們會使用發票將成本範圍限定在特定計費期間。 [計費週期 api](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)會由發票 API 取代。 所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。

若要使用發票 API 取得發票：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>價位表 Api

本節將討論現有的價位表 Api，並提供移至 Microsoft 客戶合約之價位表 API 的建議。 它也會討論 Microsoft 客戶合約的價位表 API，並說明價位表中的欄位。 [企業取得價位表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)和[企業取得計費週期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)api 會由適用于 Microsoft 客戶合約的價位表 api （microsoft 帳單/billingAccounts/billingProfiles/pricesheet）取代。 新的 API 以非同步 REST 格式支援 JSON 和 CSV 格式。 所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。

### <a name="billing-enterprise-apis"></a>計費企業 Api

您已使用計費企業 Api 搭配企業註冊來取得價格和計費週期資訊。 Azure Active Directory web 權杖所使用的驗證和授權。

若要使用價位表和計費週期 Api 取得指定企業註冊適用的價格：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>適用于 Microsoft 客戶合約的價位表 API

使用 Microsoft 客戶合約的價位表 API 來查看所有 Azure 耗用量和 Marketplace 耗用量服務的價格。 針對帳單設定檔所顯示的價格會套用到屬於帳單設定檔的所有訂用帳戶。

使用價位表 API 來以 CSV 格式來查看所有 Azure 使用量服務價位表資料：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

使用價位表 API 以 JSON 格式來查看所有 Azure 使用量服務價位表資料：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用 API 會傳回整個帳戶的價位表。 不過，您也可以取得 PDF 格式之價位表的精簡版本。 此摘要包含以特定發票計費的 Azure 耗用量和 Marketplace 耗用量服務。 發票是由 {invoiceId} 所識別，這與發票摘要 PDF 檔案中顯示的**發票編號**相同。 範例如下。

![顯示對應至 InvoiceId 之發票編號的範例影像](./media/migrate-cost-management-api/invoicesummary.png)

若要以 CSV 格式使用價位表 API 來查看發票資訊：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要以 JSON 格式使用價位表 API 來查看發票資訊：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

您也可以在目前開啟的計費週期或服務期間，查看任何 Azure 耗用量或 Marketplace 耗用量服務的預估價格。

若要以 CSV 格式使用價位表 API 來查看耗用量服務的預估價格：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要以 JSON 格式使用價位表 API 來查看耗用量服務的預估價格：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客戶合約價位表 Api 是*非同步 REST api*。 Api 的回應會從較舊的同步 Api 變更。 API 回應的主體也會變更。

#### <a name="old-response-body"></a>舊的回應主體

以下是同步 REST API 回應的範例：

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
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
    ]
```

#### <a name="new-response-body"></a>新的回應主體

Api 支援[AZURE REST 非同步](../../azure-resource-manager/management/async-operations.md)格式。 使用 GET 呼叫 API，您會收到下列回應：

```
No Response Body

HTTP Status 202 Accepted
```

下列標頭會隨著輸出的位置傳送：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

對位置進行另一個 GET 呼叫。 GET 呼叫的回應是相同的，直到作業達到完成或失敗狀態為止。 完成時，GET 呼叫位置的回應會傳回下載 URL。 就像是同時執行作業一樣。 以下是範例：

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

用戶端也可以進行 `Azure-AsyncOperation`的 GET 呼叫。 端點會傳回作業的狀態。

下表顯示較舊企業取得價位表 API 中的欄位。 其包含新價位表中的對應欄位，適用于 Microsoft 客戶合約：

| 舊屬性 | 新增屬性 | 注意 |
| --- | --- | --- |
| billingPeriodId  | _不適用_ | 不適用。 若為 Microsoft 客戶合約，發票和相關聯的價位表會取代 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 確切的字串值可能會不同。 |
| includedQuantity  | includedQuantity | 不適用於 Microsoft 客戶合約中的服務。 |
| partNumber  | _不適用_ | 相反地，請使用 productOrderName 的組合（與 offerID 相同）和 meterID。 |
| unitPrice  | unitPrice | 單價適用于 Microsoft 客戶合約所使用的服務。 |
| currencyCode  | pricingCurrency | Microsoft 客戶合約具有定價貨幣和計費貨幣的價格標記法。 CurrencyCode 會對應至 Microsoft 客戶合約中的 pricingCurrency。 |
| offerID | productOrderName | 除了 OfferID，您可以使用 productOrderName，但不能與 OfferID 相同。 不過，productOrderName 和計量會決定與 meterId 和 OfferID 在舊版註冊中相關的 Microsoft 客戶合約定價。 |

## <a name="consumption-price-sheet-api-operations"></a>耗用量價位表 API 作業

針對 Enterprise 合約，您使用了「取用價位表 API[取得](/rest/api/consumption/pricesheet/get)」和「依訂用。」計費週期[取得](/rest/api/consumption/pricesheet/getbybillingperiod)範圍作業。 此 API 會使用 Azure 資源管理驗證。

使用價位表 API 取得範圍的價位表資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

若要使用價位表 API 以計費週期取得價位表資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

請使用下列 Microsoft 客戶合約，而不是上述 API 端點：

**適用于 Microsoft 客戶合約的價位表 API （非同步 REST API）**

此 API 適用于 Microsoft 客戶合約，並提供其他屬性。

**帳單帳戶中帳單設定檔範圍的價位表**

這個 API 是現有的 API。 它已更新為在帳單帳戶中提供帳單設定檔的價位表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>依帳單帳戶的範圍價位表

當您在帳單帳戶的註冊範圍中取得價位表時，就會使用 Azure Resource Manager 驗證。

若要取得帳單帳戶中註冊帳戶的價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

如需 Microsoft 客戶合約，請使用下一節中的資訊。 它會提供用於 Microsoft 客戶合約的欄位屬性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>帳單帳戶中帳單設定檔範圍的價位表

計費帳戶 API 更新的價位表會取得 CSV 格式的價位表。 若要取得 MCA 的帳單設定檔範圍的價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 的註冊範圍中，API 回應和屬性完全相同。 屬性會對應至相同的 MCA 屬性。

下表列出[Azure Resource Manager 價位表 api](/rest/api/consumption/pricesheet)的舊版屬性和相同的新屬性。

| 舊 Azure Resource Manager 價位表 API 屬性  | 新的 Microsoft 客戶合約價位表 API 屬性   | 說明 |
| --- | --- | --- |
| 計量識別碼 | _meterId_ | 計量的唯一識別碼。 與 meterID 相同。 |
| 計量名稱 | meterName | 計量的名稱。 計量代表可部署 Azure 服務的資源。 |
| 計量類別目錄  | 服務 | 計量的分類類別名稱。 與 Microsoft 客戶合約價位表中的服務相同。 確切的字串值不同。 |
| 計量子類別 | meterSubCategory | 計量的子分類類別名稱。 根據服務中的高階功能集差異分類。 例如，基本 SQL DB 與標準 SQL DB。 |
| 計量區域 | meterRegion | &nbsp;  |
| 單位 | _不適用_ | 可以從 unitOfMeasure 進行剖析。 |
| 測量單位 | unitOfMeasure | &nbsp;  |
| 部分編號 | _不適用_ | 請使用 productOrderName 和 MeterID 來唯一識別帳單設定檔的價格，而不是元件編號。 欄位會列在 MCA 發票，而不是 MCA 發票中的零件編號。 |
| 單位價格 | unitPrice | Microsoft 客戶合約單價。 |
| 貨幣代碼 | pricingCurrency | Microsoft 客戶合約代表定價貨幣和計費貨幣的價格。 貨幣代碼與 Microsoft 客戶合約中的 pricingCurrency 相同。 |
| 內含數量 | includedQuantity | 不適用於 Microsoft 客戶合約中的服務。 顯示值為零的。 |
|  供應項目識別碼  | productOrderName | 請改用 productOrderName，而不是 OfferID。 不同于 OfferID，不過 productOrderName 和計量會決定 Microsoft 客戶合約中的定價。 與舊版註冊中的 meterId 和 OfferID 相關。 |

Microsoft 客戶合約的價格與 Enterprise 合約的定義方式不同。 企業註冊中的服務價格對於產品、零件編號、計量及供應專案而言是唯一的。 部分編號不會用於 Microsoft 客戶合約。

屬於 Microsoft 客戶合約的 Azure 耗用量服務價格，對於 productOrderName 和 meterID 而言是唯一的。 它們代表服務計量和產品方案。

若要在使用方式詳細資料 API 中協調價位表和使用量，您可以使用 productOrderName 和 meterID。

具有帳單設定檔擁有者、參與者、讀者和發票管理員許可權的使用者可以下載價位表。

價位表包含價格以使用量為基礎之服務的價格。 這些服務包括 Azure 耗用量和 Marketplace 耗用量。 在每個服務期間結束時，最新的價格會被鎖定，並套用至單一服務期間的使用量。 針對 Azure 耗用量服務，服務期間通常是日曆月份。

### <a name="retired-price-sheet-api-fields"></a>已淘汰的價位表 API 欄位

下欄欄位可能無法在 Microsoft 客戶合約價位表 Api 中使用，或具有相同的欄位。

|已淘汰欄位| 說明|
|---|---|
| billingPeriodId | 不適用。 對應至 MCA 的 InvoiceId。 |
| offerID | 不適用。 對應至 MCA 中的 productOrderName。 |
| meterCategory  | 不適用。 對應至 MCA 中的服務。 |
| unit | 不適用。 可以從 unitOfMeasure 進行剖析。 |
| currencyCode | 與 MCA 中的 pricingCurrency 相同。 |
| meterLocation | 與 MCA 中的 meterRegion 相同。 |
| partNumber partnumber | 不適用，因為 MCA 發票中未列出零件編號。 請使用 meterId 和 productOrderName 組合來唯一識別價格，而不是元件編號。 |
| totalIncludedQuantity | 不適用。 |
| pretaxStandardRate  | 不適用。 |

## <a name="reservation-instance-charge-api-replaced"></a>保留實例費用 API 已取代

您可以使用[保留實例費用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)取得保留購買的帳單交易。 新的 API 包含所有購買專案，包括協力廠商 Marketplace 供應專案。 所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。 保留實例費用 API 已由交易 API 取代。

若要使用交易 API 取得保留購買交易：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>已取代的建議 Api

保留實例購買建議 Api 可提供過去7、30或60天的虛擬機器使用量。 Api 也會提供保留購買建議。 其中包括：

- [共用保留實例建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [單一保留實例建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。 先前所列的保留建議 Api 會由[Microsoft 取用/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API 取代。

若要使用保留建議 API 取得保留建議：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>保留使用量 Api 已取代

您可以使用保留實例使用方式 API 來取得註冊中的保留使用量。 如果註冊中有一個以上的保留實例，您也可以使用此 API 來取得所有保留實例購買的使用量。

其中包括：

- [保留實例使用量詳細資料](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [保留實例使用摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有耗用量 Api 都會由使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 rest](/rest/api/azure/#create-the-request)。 先前所列的保留建議 Api 會由[reservationDetails](/rest/api/consumption/reservationsdetails)和[Microsoft 取用/reservationSummaries](/rest/api/consumption/reservationssummaries) api 取代。

使用保留詳細資料 API 取得保留詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要使用保留摘要 API 取得保留摘要：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>從 Cloudyn 移至成本管理

使用[Cloudyn](https://cloudyn.com)的組織應該開始使用[Azure 成本管理](https://azure.microsoft.com/services/cost-management/)以滿足任何成本管理需求。 成本管理可在 Azure 入口網站中取得，而且沒有上線和8小時的延遲。 如需詳細資訊，請參閱[成本管理檔](../index.yml)。

利用 Azure 成本管理，您即可：

- 針對預先定義的預算，查看一段時間的成本。 分析每日成本模式來找出並停止異常支出。 依據標記、資源群組、服務和位置來細分成本。
- 建立預算來設定使用量和成本的限制，並在接近重要臨界值時收到通知。 使用動作群組設定自動化以觸發自訂事件，並對您的條款施行嚴格限制。
- 使用來自 Azure Advisor 的建議來優化成本和使用量。 探索包含保留的採購優化、縮減未充分運用的虛擬機器，以及刪除未使用的資源以維持在預算內。
- 排程成本和使用量資料匯出，每天將 CSV 檔案發佈到您的儲存體帳戶。 自動化與外部系統的整合，讓帳單資料保持同步和最新狀態。

## <a name="power-bi-integration"></a>Power BI 整合

您也可以使用 Power BI 來進行成本報告。 適用于 Power BI Desktop 的[Azure 成本管理連接器](/power-bi/desktop-connect-azure-cost-management)可用來建立功能強大的自訂報告，以協助您更瞭解 Azure 費用。 Azure 成本管理連接器目前支援具有 Microsoft 客戶合約或 Enterprise 合約（EA）的客戶。

## <a name="next-steps"></a>後續步驟

- 閱讀[成本管理檔](../index.yml)，以瞭解如何監視及控制 Azure 費用。 或者，如果您想要利用成本管理優化資源使用。
