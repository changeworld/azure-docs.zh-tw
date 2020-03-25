---
title: 將 EA 遷移至 Microsoft 客戶合約 API - Azure
description: 本文可協助您了解將 Microsoft Enterprise 合約 (EA) 遷移至 Microsoft 客戶合約的結果。
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 397e0a21b1ba11b3bdd74c2030ff358c1ce159d8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77201028"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>從 Enterprise 合約遷移至 Microsoft 客戶合約 API

本文可協助您了解 Enterprise 合約 (EA) 與 Microsoft 客戶合約 (MCA) 帳戶之間的資料結構、API 和其他系統整合差異。 Azure 成本管理同時支援這兩種帳戶類型的 API。 請先檢閱[設定 Microsoft 客戶合約的計費帳戶](../manage/mca-setup-account.md)一文，再繼續進行。

目前已有 EA 帳戶的組織應該檢閱這篇文章以及設定 MCA 帳戶的文章。 以前在更新 EA 帳戶時，只需要進行最少量的一些工作就能從舊註冊移至新註冊。 不過，遷移至 MCA 帳戶則需要進行額外的工作。 需要進行額外的工作是因為基礎計費子系統有所變更，因此會影響所有成本相關的 API 和服務供應項目。

## <a name="mca-apis-and-integration"></a>MCA API 和整合

MCA API 和新的整合可讓您：

- 透過原生 Azure API 獲得完整的 API 可用性。
- 在單一計費帳戶中設定多個發票。
- 存取結合了 Azure 服務使用量、第三方 Marketplace 使用量和 Marketplace 購買的 API。
- 使用 Azure 成本管理來檢視所有帳單設定檔 (與註冊相同) 的成本。
- 存取新的 API 以顯示成本、在成本超過預先定義的閾值時收到通知，並自動匯出原始資料。

## <a name="migration-checklist"></a>移轉檢查清單

下列項目可協助您轉換至 MCA API。

- 熟悉新的 [Microsoft 客戶合約計費帳戶](../understand/mca-overview.md)。
- 確定您使用的 API，並在下一節查看哪些 API 已遭到取代。
- 熟悉 [Azure Resource Manager REST API](/rest/api/azure)。
- 如果您還沒有使用 Azure Resource Manager API，請[向 Azure AD 註冊您的用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 將任何程式設計程式碼更新為[使用 Azure AD 驗證](/rest/api/azure/#create-the-request)。
- 更新任何程式設計程式碼，以使用 MCA API 呼叫來取代 EA API 呼叫。
- 將錯誤處理機制更新為使用新的錯誤碼。
- 檢閱其他整合供應項目，例如 Cloudyn 和 Power BI，以進行其他所需的動作。

## <a name="ea-apis-replaced-with-mca-apis"></a>已由 MCA API 取代的 EA API

EA API 使用 API 金鑰來進行驗證和授權。 MCA API 則使用 Azure AD 驗證。

| 目的 | EA API | MCA API |
| --- | --- | --- |
| 餘額與信用額度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 使用量 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用量 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 使用量 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 計費期間 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 價位表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 保留購買 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 保留建議 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 保留使用量 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure 服務和第三方 Marketplace 的使用量可透過[使用量詳細資料 API](/rest/api/consumption/usagedetails) 來獲得。

以下是 MCA 計費帳戶可用的 API：

| 目的 | Microsoft 客戶合約 (MCA) API |
| --- | --- |
| 計費帳戶<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 帳單設定檔<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 發票區段<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 發票 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 計費訂用帳戶 | {scope}/billingSubscriptions |

<sup>2</sup> API 會傳回物件 (亦即範圍) 清單，Azure 入口網站中的成本管理體驗以及 API 會在這些範圍中運作。 如需成本管理範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

如果您使用任何現有的 EA API，則必須將其更新為支援 MCA 計費帳戶。 下表顯示其他整合變更：

| 目的 | 舊供應項目 | 新供應項目 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 使用量見解](/power-bi/desktop-connect-azure-consumption-insights)內容套件和連接器 |  [Azure 使用量見解連接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>用來取得餘額和信用額度的 API

[取得餘額摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API 會向您提供下列項目的每月摘要：

- 餘額
- 新的購買項目
- Azure Marketplace 服務費用
- 調整
- 服務超額費用

所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

取得餘額摘要 API 會由 Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API 加以取代。

若要使用可用餘額 API 來取得可用餘額：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>用來取得成本和使用量的 API

使用下列 API 來取得 Azure 服務使用量、第三方 Marketplace 使用量和其他 Marketplace 購買成本的每日明細。 下列適用於 Azure 服務和第三方 Marketplace 使用量的個別 API 已合併。 舊 API 會由 [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API 加以取代。 此 API 新增了 Marketplace 購買，以前只會在至今為止的餘額摘要中顯示。

- [取得使用量詳細資料/下載](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用量詳細資料/提交](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用量詳細資料/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得使用量詳細資料/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得市集商店費用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [取得市集商店費用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

所有先前的 API 都會由使用量/使用量詳細資料 API 加以取代。

若要使用使用量詳細資料 API 來取得使用量詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

與所有成本管理 API 一樣，使用量詳細資料 API 也可以在多個範圍中使用。 至於已開立發票的成本，請和傳統上在註冊層級所收到的一樣，使用帳單設定檔範圍即可。  如需成本管理範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

| 類型 | 識別碼格式 |
| --- | --- |
| 計費帳戶 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 帳單設定檔 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 訂用帳戶 | `/subscriptions/{subscriptionId}` |
| 資源群組 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

使用下列 querystring 參數來更新任何程式設計程式碼。

| 舊參數 | 新參數 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支援 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

回應的主體也已變更。

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

包含使用量記錄陣列的屬性名稱已從資料變更為「值」  。 每一筆記錄以前會有詳細屬性的一般清單。 不過，現在每一筆記錄的所有詳細資料都會在名為「屬性」  的巢狀屬性中，但標籤除外。 新的結構會與其他 Azure API 保持一致。 某些屬性名稱已變更。 下表會顯示對應的屬性。

| 舊屬性 | 新屬性 | 注意 |
| --- | --- | --- |
| AccountId | N/A | 不會追蹤訂用帳戶建立者。 使用 invoiceSectionId (與 departmentId 相同)。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | N/A | 不會追蹤訂用帳戶建立者。 使用 invoiceSectionName (與 departmentName 相同)。 |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 請注意，這些屬性互相對立。 如果 isAzureCreditEnabled 為 true，則 ChargesBilledSeparately 會是 false。 |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 確切的字串值可能會不同。 |
| ConsumedServiceId | None | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 日期和 usageStartDate | date | &nbsp;  |
| Day | None | 剖析開始日期的日子。 |
| DepartmentId | invoiceSectionId | 確切的值會不同。 |
| DepartmentName | invoiceSectionName | 確切的字串值可能會不同。 如有需要，請將發票區段設定為符合部門。 |
| ExtendedCost 和成本 | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 是定期費用 | None | &nbsp;  |
| Location | location | &nbsp;  |
| MeterCategory | meterCategory | 確切的字串值可能會不同。 |
| 計量識別碼 | meterId | 確切的字串值會不同。 |
| MeterName | meterName | 確切的字串值可能會不同。 |
| MeterRegion | meterRegion | 確切的字串值可能會不同。 |
| MeterSubCategory | meterSubCategory | 確切的字串值可能會不同。 |
| Month | None | 剖析開始日期的月份。 |
| 供應項目名稱 | None | 使用 publisherName 和 productOrderName。 |
| OfferID | None | &nbsp;  |
| 訂單編號 | None | &nbsp;  |
| PartNumber | None | 使用 meterId 和 productOrderName 來唯一識別價格。 |
| 方案名稱 | productOrderName | &nbsp;  |
| Products | Products |   |
| ProductId | productId | 確切的字串值會不同。 |
| 發行者名稱 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 確切的字串值會不同。 |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | None | &nbsp;  |
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
| Tags | tags | 標籤屬性會套用至根物件，而非套用至巢狀屬性屬性。 |
| UnitOfMeasure | unitOfMeasure | 確切的字串值會不同。 |
| usageEndDate | date | &nbsp;  |
| Year | None | 剖析開始日期的年份。 |
| (新增) | billingCurrency | 費用的計價貨幣。 |
| (新增) | billingProfileId | 帳單設定檔的唯一識別碼 (與註冊相同)。 |
| (新增) | billingProfileName | 帳單設定檔的名稱 (與註冊相同)。 |
| (新增) | chargeType | 用來區分 Azure 服務使用量、Marketplace 使用量和購買。 |
| (新增) | invoiceId | 發票的唯一識別碼。 空白代表當前所處的月份。 |
| (新增) | publisherType | 購買的發行者類型。 空白代表使用量。 |
| (新增) | serviceFamily | 購買的類型。 空白代表使用量。 |
| (新增) | servicePeriodEndDate | 已購買服務的結束日期。 |
| (新增) | servicePeriodStartDate | 已購買服務的開始日期。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>計費週期 API 已由發票 API 加以取代

MCA 計費帳戶不會使用計費週期。 相反地，這些帳戶會使用發票將成本範圍限定在特定計費週期。 [計費週期 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 已由發票 API 加以取代。 所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

若要使用發票 API 來取得發票：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>價位表 API

本節將討論現有的價位表 API，並針對 Microsoft 客戶合約提供移至價位表 API 的建議。 文中也會討論適用於 Microsoft 客戶合約的價位表 API，並說明價位表中的欄位。 [Enterprise 取得價位表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)和 [Enterprise 取得計費週期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API 已由適用於 Microsoft 客戶合約的價位表 API (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet) 加以取代。 新 API 會以非同步 REST 格式同時支援 JSON 和 CSV 格式。 所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

### <a name="billing-enterprise-apis"></a>計費企業版 API

您以前使用計費企業版 API 搭配 Enterprise 註冊來取得價格和計費週期資訊。 驗證和授權則使用 Azure Active Directory Web 權杖。

若要使用價位表和計費週期 API 來取得指定 Enterprise 註冊的適用價格：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>適用於 Microsoft 客戶合約的價位表 API

使用適用於 Microsoft 客戶合約的價位表 API 來檢視所有 Azure 使用量和 Marketplace 使用量服務的價格。 針對帳單設定檔所顯示的價格適用於屬於該帳單設定檔的所有訂用帳戶。

使用價位表 API 來以 CSV 格式檢視所有 Azure 使用量服務價位表資料：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

使用價位表 API 來以 JSON 格式檢視所有 Azure 使用量服務價位表資料：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用此 API 會傳回整個帳戶的價位表。 不過，您也可以取得 PDF 格式的精簡版價位表。 摘要內會包含特定發票中的計費 Azure 使用量和 Marketplace 使用量服務。 發票可透過 {invoiceId} 來加以識別，這與發票摘要 PDF 檔案中顯示的**發票號碼**相同。 範例如下。

![顯示 InvoiceId 所對應發票編號的範例影像](./media/migrate-cost-management-api/invoicesummary.png)

若要使用價位表 API 以 CSV 格式檢視發票資訊：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要使用價位表 API 以 JSON 格式檢視發票資訊：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

您也可以在目前開啟的計費週期或服務期間，查看任何 Azure 使用量或 Marketplace 使用量服務的預估價格。

若要使用價位表 API 以 CSV 格式檢視使用量服務的預估價格：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要使用價位表 API 以 JSON 格式檢視使用量服務的預估價格：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客戶合約價位表 API 是「非同步的 REST API」  。 API 的回應已與較舊的同步 API 有所變化。 API 回應的主體也已變更。

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

API 支援 [Azure REST 非同步](../../azure-resource-manager/management/async-operations.md)格式。 使用 GET 呼叫 API，然後便會收到下列回應：

```
No Response Body

HTTP Status 202 Accepted
```

下列標頭會隨著輸出的位置一起傳送：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

對該位置發出另一個 GET 呼叫。 GET 呼叫的回應會相同，直到作業達到完成或失敗狀態為止。 完成時，GET 呼叫位置的回應會傳回下載 URL。 情形就好像該作業在同時間執行一樣。 以下是範例：

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

用戶端也可以針對 `Azure-AsyncOperation` 發出 GET 呼叫。 端點會傳回作業的狀態。

下表顯示較舊 Enterprise 取得價位表 API 中的欄位。 其包含適用於 Microsoft 客戶合約的新價位表中所對應的欄位：

| 舊屬性 | 新屬性 | 注意 |
| --- | --- | --- |
| billingPeriodId  | _不適用_ | 不適用。 若為 Microsoft 客戶合約，發票和相關聯的價位表已取代 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 確切的字串值可能會不同。 |
| includedQuantity  | includedQuantity | 不適用於 Microsoft 客戶合約中的服務。 |
| partNumber  | _不適用_ | 相反地，請使用 productOrderName (與 offerID 相同) 和 meterID 的組合。 |
| unitPrice  | unitPrice | 單價適用於 Microsoft 客戶合約所使用的服務。 |
| currencyCode  | pricingCurrency | Microsoft 客戶合約具有以定價貨幣和計費貨幣表示的價格。 currencyCode 會對應至 Microsoft 客戶合約中的 pricingCurrency。 |
| offerID | productOrderName | 除了 OfferID，您也可以使用 productOrderName，但這與 OfferID 不同。 不過，productOrderName 和計量會決定舊版註冊中與 meterId 和 OfferID 相關的 Microsoft 客戶合約定價。 |

## <a name="consumption-price-sheet-api-operations"></a>使用量價位表 API 作業

針對 Enterprise 合約，您以前會依 subscriptionId 或計費週期來針對某個範圍使用「使用量價位表 API」的[取得](/rest/api/consumption/pricesheet/get)和[依計費週期取得](/rest/api/consumption/pricesheet/getbybillingperiod)作業。 該 API 會使用 Azure 資源管理驗證。

若要使用價位表 API 來取得某個範圍的價位表資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

若要使用價位表 API 來取得依計費週期顯示的價位表資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

請使用下列 Microsoft 客戶合約的端點，而不要使用上述 API 端點：

**適用於 Microsoft 客戶合約的價位表 API (非同步 REST API)**

此 API 適用於 Microsoft 客戶合約，且會提供其他屬性。

**計費帳戶中某個帳單設定檔範圍的價位表**

這是現有的 API。 其已更新為會針對計費帳戶中的帳單設定檔來提供價位表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>依計費帳戶的範圍價位表

當您在計費帳戶的註冊範圍中取得價位表時，就會使用 Azure Resource Manager 驗證。

若要在計費帳戶的註冊帳戶中取得價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

若為 Microsoft 客戶合約，請使用下一節中的資訊。 其會提供用於 Microsoft 客戶合約的欄位屬性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>計費帳戶中某個帳單設定檔範圍的價位表

已更新的「依計費帳戶的價位表 API」會取得 CSV 格式的價位表。 若要在 MCA 的帳單設定檔範圍取得價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 的註冊範圍中，API 回應和屬性完全相同。 屬性會對應至相同的 MCA 屬性。

[Azure Resource Manager 價位表 API](/rest/api/consumption/pricesheet) 的舊版屬性和相同的新屬性如下表所示。

| 舊的 Azure Resource Manager 價位表 API 屬性  | 新的 Microsoft 客戶合約價位表 API 屬性   | 描述 |
| --- | --- | --- |
| 計量識別碼 | _meterId_ | 計量的唯一識別碼。 與 meterID 相同。 |
| 計量名稱 | meterName | 計量的名稱。 計量代表 Azure 服務的可部署資源。 |
| 計量類別  | 服務 | 計量的分類類別名稱。 與 Microsoft 客戶合約價位表中的服務相同。 確切的字串值會不同。 |
| 計量子類別 | meterSubCategory | 計量的子分類類別名稱。 根據服務中的高階功能集差異分類。 例如，基本 SQL DB 與標準 SQL DB。 |
| 計量區域 | meterRegion | &nbsp;  |
| 單位 | _不適用_ | 可以從 unitOfMeasure 進行剖析。 |
| 測量單位 | unitOfMeasure | &nbsp;  |
| 組件編號 | _不適用_ | 請使用 productOrderName 和 MeterID (而不是組件編號) 來唯一識別帳單設定檔的價格。 欄位會列在 MCA 發票上，而不是 MCA 發票中的組件編號上。 |
| 單位價格 | unitPrice | Microsoft 客戶合約單價。 |
| 貨幣代碼 | pricingCurrency | Microsoft 客戶合約會以定價貨幣和計費貨幣來表示價格。 貨幣代碼與 Microsoft 客戶合約中的 pricingCurrency 相同。 |
| 包含的數量 | includedQuantity | 不適用於 Microsoft 客戶合約中的服務。 會與零值一起顯示。 |
|  供應項目識別碼  | productOrderName | 請改用 productOrderName，而不是 OfferID。 不同於 OfferID，不過 productOrderName 和計量會決定 Microsoft 客戶合約中的定價。 與舊版註冊中的 meterId 和 OfferID 有關。 |

Microsoft 客戶合約價格的定義方式與 Enterprise 合約不同。 Enterprise 註冊中的服務價格對於產品、組件編號、計量及供應項目而言是唯一的。 組件編號不會用於 Microsoft 客戶合約。

屬於 Microsoft 客戶合約的 Azure 使用量服務價格，對於 productOrderName 和 meterID 而言是唯一的。 這兩項代表服務計量和產品方案。

若要讓使用量詳細資料 API 中的價位表和使用量協調一致，您可以使用 productOrderName 和 meterID。

具有帳單設定檔擁有者、參與者、讀者和發票管理者權限的使用者可以下載價位表。

服務若以使用量作為其價格依據，價位表便會包含該服務。 這些服務包括 Azure 使用量和 Marketplace 使用量。 在每個服務期間結束時，最新價格將會鎖定起來，並套用至單一服務期間的使用量。 針對 Azure 使用量服務，服務期間通常是行事曆月份。

### <a name="retired-price-sheet-api-fields"></a>已淘汰的價位表 API 欄位

下列欄位要不就是無法在 Microsoft 客戶合約價位表 API 中使用，就是已有相同的欄位。

|已淘汰的欄位| 描述|
|---|---|
| billingPeriodId | 不適用。 對應至 MCA 的 InvoiceId。 |
| offerID | 不適用。 對應至 MCA 的 productOrderName。 |
| meterCategory  | 不適用。 對應至 MCA 的服務。 |
| unit | 不適用。 可以從 unitOfMeasure 進行剖析。 |
| currencyCode | 與 MCA 的 pricingCurrency 相同。 |
| meterLocation | 與 MCA 的 meterRegion 相同。 |
| partNumber partnumber | 不適用，因為 MCA 發票中未列出組件編號。 請使用 meterId 和 productOrderName 組合來唯一識別價格，而不要使用組件編號。 |
| totalIncludedQuantity | 不適用。 |
| pretaxStandardRate  | 不適用。 |

## <a name="reservation-instance-charge-api-replaced"></a>保留執行個體費用 API 已遭到取代

您可以使用[保留執行個體費用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) 來取得保留購買的計費交易。 新的 API 包含所有購買項目，包括第三方 Marketplace 供應項目。 所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 保留執行個體費用 API 已由交易 API 加以取代。

若要使用交易 API 來取得保留購買交易：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>建議 API 已遭到取代

保留執行個體購買建議 API 可提供過去 7 天、30 天或 60 天的虛擬機器使用量。 某些 API 也會提供保留購買建議。 其中包括：

- [共用保留執行個體建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [單一保留執行個體建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 以前列出的保留建議 API 已由 [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API 加以取代。

若要使用保留建議 API 來取得保留建議：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>保留使用量 API 已遭到取代

您可以使用保留執行個體使用量 API 來取得註冊中的保留使用量。 如果註冊中有多個保留執行個體，您也可以使用此 API 來取得所有保留執行個體購買的使用量。

其中包括：

- [保留執行個體使用量詳細資料](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [保留執行個體使用量摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有使用量 API 都會由使用 Azure AD 來進行驗證和授權的原生 Azure API 加以取代。 如需如何呼叫 Azure REST API 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 以前列出的保留建議 API 已由 [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) 和 [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API 加以取代。

若要使用保留詳細資料 API 來取得保留詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要使用保留摘要 API 來取得保留摘要：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>從 Cloudyn 移至成本管理

使用 [Cloudyn](https://cloudyn.com) 的組織應該開始使用 [Azure 成本管理](https://azure.microsoft.com/services/cost-management/)來滿足任何成本管理需求。 成本管理可在 Azure 入口網站中取得，不必上線，也不會有 8 小時的延遲。 如需詳細資訊，請參閱[成本管理文件](../index.yml)。

透過 Azure 成本管理，您可以：

- 針對預先定義的預算，檢視一段時間的成本。 分析每日成本模式，以找出並停止異常支出。 依據標籤、資源群組、服務和位置來細分成本。
- 建立預算來對使用量和成本設下限制，並在接近重要閾值時收到通知。 使用動作群組來設定自動化，以觸發自訂事件並對您的條件強制執行固定限制。
- 使用 Azure Advisor 的建議來將成本和使用量最佳化。 探索如何使用保留來將購買最佳化、縮減未充分運用的虛擬機器，以及刪除未使用的資源以免超出預算。
- 排定成本和使用量資料的匯出時間，每天將 CSV 檔案發佈到您的儲存體帳戶。 自動與外部系統整合，以讓計費資料保持在同步且最新的狀態。

## <a name="power-bi-integration"></a>Power BI 整合

您也可以使用 Power BI 來報告成本。 適用於 Power BI Desktop 的 [Azure 成本管理連接器](/power-bi/desktop-connect-azure-cost-management)可用來建立功能強大的自訂報告，以協助您更加了解 Azure 的支出。 Azure 成本管理連接器目前支援具有 Microsoft 客戶合約或 Enterprise 合約 (EA) 的客戶。

## <a name="next-steps"></a>後續步驟

- 請閱讀[成本管理文件](../index.yml)，以了解如何監視及控制 Azure 費用。 或者，如果您想要利用成本管理來將資源使用最佳化的話。
