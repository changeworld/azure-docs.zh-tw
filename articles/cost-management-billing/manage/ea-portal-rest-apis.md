---
title: Azure 企業版 REST API
description: 本文將說明要與您 Azure 企業註冊搭配使用的 REST API。
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d9ea567f3285ad773007a69a651e406eb2c94821
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687428"
---
# <a name="azure-enterprise-rest-apis"></a>Azure 企業版 REST API

本文將說明要與您 Azure 企業註冊搭配使用的 REST API。 其中也會說明如何解決 REST API 的常見問題。

## <a name="consumption-and-usage-apis"></a>耗用量和使用量 API

Microsoft Enterprise Azure 客戶可以透過 REST API 取得使用量和帳單資訊。 角色擁有者 (企業系統管理員、部門系統管理員、帳戶擁有者) 必須從 Azure EA 入口網站產生金鑰，以啟用對 API 的存取。 在此之後，獲得註冊號碼和金鑰的任何人就可以透過 API 存取資料。

### <a name="available-apis"></a>可用的 API

**餘額與摘要 -** [餘額與摘要 API](../../billing/billing-enterprise-api-balance-summary.md) 可提供餘額、最新購買、Azure Marketplace 服務費用、調整，以及超額部分費用的每月摘要資訊。 如需詳細資訊，請參閱[適用於企業客戶的報告 API - 餘額與摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)。

**使用量詳細資料 -** [使用量詳細資料 API](../../billing/billing-enterprise-api-usage-detail.md) 可提供註冊的每日使用量和預估費用明細。 結果也包含執行個體、計量和部門的資訊。 您可以依計費週期或指定開始和結束日期來查詢 API。 如需詳細資訊，請參閱[適用於企業客戶的報告 API - 使用量詳細資料](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)。

**Marketplace 市集費用 -** [Marketplace 市集費用 API](../../billing/billing-enterprise-api-marketplace-storecharge.md) 可針對指定的計費週期或開始和結束日期，傳回以使用量為基礎的 Marketplace 費用每日明細 (不含一次性費用)。 如需詳細資訊，請參閱[適用於企業客戶的報告 API - Marketplace 市集費用](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)。

**價位表 -** [價位表 API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 可針對註冊和計費期間的每個計量提供適用的費率。 如需詳細資訊，請參閱[適用於企業客戶的報告 API - 價位表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)。

**列出計費週期 -** [計費週期 API](../../billing/billing-enterprise-api-billing-periods.md) 會傳回計費週期清單，其中包含註冊的使用情況資料 (以反向時間順序排列)。 每個週期包含的屬性會指向四組資料 (BalanceSummary、UsageDetails、MarketplaceCharges 和 PriceSheet) 的 API 路由。 如需詳細資訊，請參閱[適用於企業客戶的報告 API - 計費週期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)。

### <a name="enable-api-data-access"></a>啟用 API 資料存取

角色擁有者可以在 Azure EA 入口網站中執行下列步驟。 瀏覽至 [報告]   > [下載使用量]   > [API 存取金鑰]  。 然後他們可以：

- 產生主要和次要存取金鑰。
- 停用存取金鑰。
- 檢視存取金鑰的開始和結束日期。

### <a name="generate-or-retrieve-the-api-key"></a>產生或取出 API 金鑰

1. 以企業系統管理員身分登入。
2. 按一下左側導覽視窗中的 [報表]  ，然後按一下 [下載使用量]  索引標籤。
3. 按一下 [API 存取金鑰]  。
4. 在 [註冊存取金鑰]  底下，選取產生金鑰符號以產生主要或次要金鑰。
5. 選取 [展開金鑰]  以查看產生的完整 API 存取金鑰。
6. 選取 [複製]  以取得可立即使用的 API 存取金鑰。

![顯示 API 存取金鑰頁面的範例](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

如果您想要授與 API 存取金鑰的人員，不是您註冊中的企業系統管理員，請執行下列步驟：

1. 在左側導覽視窗中，按一下 [管理]  。
2. 按一下 [DA 檢視費用]  \(部門系統管理員檢視費用\) 旁的鉛筆符號。
3. 選取 [啟用]  ，然後按一下 [儲存]  。
4. 按一下 [AO 檢視費用]  \(帳戶擁有者檢視費用\) 旁的鉛筆符號。
5. 選取 [啟用]  ，然後按一下 [儲存]  。

![顯示 DA 和 AO 檢視費用已啟用的範例](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) 上述步驟讓 API 存取金鑰持有者可存取使用量報表中的成本和價格資訊。

### <a name="pass-keys-in-the-api"></a>在 API 中傳遞金鑰

針對每個驗證和授權呼叫傳遞 API 金鑰。 將下列屬性傳遞至 HTTP 標頭：

| 要求標頭金鑰 | 值 |
| --- | --- |
| 授權 | 以此格式指定值：**持有人 {API\_KEY}**
範例：持有人 \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Swagger 端點可在下列 API 的 [Enterprise Reporting v3 API](https://consumption.azure.com/swagger/ui/index) 上使用。 Swagger 有助於檢查 API。 透過 Swagger 使用 [AutoRest](https://github.com/Azure/AutoRest) 或 [Swagger CodeGen](https://swagger.io/swagger-codegen/) 來產生用戶端 SDK。 2014 年 5 月 1 日之後可用的資料可透過 API 取得。

### <a name="api-response-codes"></a>API 回應碼

當您使用 API 時，回應狀態碼會隨之顯示。 下表為這些狀態碼的描述。

| 回應狀態碼 | 訊息 | 描述 |
| --- | --- | --- |
| 200 | [確定] | 沒有錯誤 |
| 401 | 未經授權 | API 金鑰找不到、無效或過期等。 |
| 404 | 無法使用 | 找不到報告端點 |
| 400 | 不正確的要求 | 無效的參數 - 資料範圍、EA 編號等。 |
| 500 | 伺服器錯誤 | 處理要求時發生未預期的錯誤 |

### <a name="usage-and-billing-data-update-frequency"></a>使用量和計費資料的更新頻率

目前計費月份中的使用量和帳單資料檔案會每 24 小時更新一次。 不過，最多可能會有三天的資料延遲發生。 例如，如果發生使用量的時間是星期一，則資料可能會到星期四才出現在資料檔案中。

### <a name="test-enrollment-for-development"></a>測試註冊以用於開發

如果您是沒有 Azure 合約註冊的合作夥伴或開發人員，但想要存取 API，您可以使用測試註冊。 註冊名稱為 _EnrollmentNumber 100_ ，您可以尋找並測試直到 2018 年 6月的使用量資訊。 接著，可以使用下列金鑰來呼叫 API 並查看範例資料。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Azure 服務目錄

所有 Azure 服務都會發佈到 Azure 儲存體部落格中的 CSV 格式目錄。 如果您需要為您的系統建立所有 Azure 服務的規劃目錄，則此目錄就很有用。 目前的目錄位於 [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv)。

### <a name="csv-data-file-details"></a>CSV 資料檔案詳細資料

下列是說明 API 報表屬性的資訊。

#### <a name="usage-summary"></a>使用量摘要

JSON 格式是從 CSV 報表產生的。 因此，此格式與摘要 CSV 格式相同。 資料行名稱已加入，因此當您取用 JSON 摘要資料時，應該還原序列化為資料表。

| CSV 資料行名稱 | JSON 資料行名稱 | JSON 新資料行 | 註解 |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| 帳戶名稱 | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| 訂用帳戶名稱 | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | 顯示服務目錄報表執行的日期。 格式為不含時間戳記的日期字串。 |
| Month | Month | Month |   |
| Day | Day | Day |   |
| Year | Year | Year |   |
| Products | BillableItemName | Products |   |
| 計量識別碼 | ResourceGUID | 計量識別碼 |   |
| 計量類別 | 服務 | MeterCategory | 有助於尋找服務。 適用於具有多個 ServiceType 的服務。 例如：虛擬機器。 |
| 計量子類別 | ServiceType | MeterSubCategory | 提供服務的第二層詳細資料。 例如：A1 VM (非 Windows)。  |
| 計量區域 | ServiceRegion | MeterRegion | 服務所需的第三層詳細資料。 有助於尋找 ResourceGUID 的區域內容。 |
| 計量名稱 | ServiceResource | MeterName | 服務的名稱。 |
| 已耗用的數量 | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| 資源位置 | ServiceSubRegion | ResourceLocation |   |
| 已耗用的服務 | ServiceInfo | ConsumedService |   |
| 執行個體識別碼 | 元件 | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Tags | Tags | Tags |   |
| 儲存體服務識別碼   | OrderNumber | StoreServiceIdentifier   |   |
| 部門名稱 | DepartmentName | DepartmentName |   |
| 成本中心 | CostCenter | CostCenter |   |
| 測量單位 | UnitOfMeasure | UnitOfMeasure | 範例值：小時、GB、事件、推播、單位、單位時數、MB、每日單位 |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace 報表

| CSV 資料行名稱 | JSON 資料行名稱 | JSON 新資料行 |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| 帳戶名稱 | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| 訂用帳戶名稱 | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  日期 (僅限日期字串。 無時間戳記)
| Month | Month |  Month |
| Day | Day |  Day |
| Year | Year |  Year |
| 計量識別碼 | MeterResourceId |  計量識別碼 |
| 發行者名稱 | PublisherFriendlyName |  PublisherName |
| 供應項目名稱 | OfferFriendlyName |  OfferName |
| 方案名稱 | PlanFriendlyName |  PlanName |
| 已耗用的數量 | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| 測量單位 | UnitOfMeasure | UnitOfMeasure |
| 執行個體識別碼 | InstanceId | InstanceId |
| 其他資訊 | AdditionalInfo | AdditionalInfo |
| Tags | Tags | Tags |
| 訂單編號 | OrderNumber | OrderNumber |
| 部門名稱 | DepartmentNames | DepartmentName |
| 成本中心 | CostCenters |  CostCenter |
| 資源群組 | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>價位表

| CSV 資料行名稱 | JSON 資料行名稱 | 註解 |
| --- | --- | --- |
| 服務 | 服務 |  價格不會變更 |
| 測量單位 | UnitOfMeasure |   |
| 超額部分編號 | ConsumptionPartNumber |   |
| 超額單位價格 | ConsumptionPrice |   |
| 貨幣代碼 | CurrencyCode |     |

### <a name="common-api-issues"></a>常見的 API 問題

當您使用 Azure 企業 REST API 時，您可能會遇到下列任何常見的問題。

您可以嘗試使用沒有正確授權類型的 API 金鑰。 API 金鑰產生來源：

- 企業系統管理員
- 部門系統管理員 (DA)
- 帳戶擁有者 (AO)

EA 系統管理員所產生的金鑰，可讓您存取該註冊的所有資訊。 唯讀 EA 系統管理員無法產生 API 金鑰。

DA 或 AO 所產生的金鑰不會提供餘額、費用和價位表資訊的存取權。

API 金鑰每六個月就會過期。 如果過期，您需要重新產生金鑰。

如果您收到逾時錯誤，可以藉由提高逾時的閾值限制來加以解決。

您可能會收到 401 錯誤 (未經授權) 的到期錯誤。 錯誤通常是由過期的金鑰所造成。 如果金鑰已過期，您可以重新產生金鑰。

當選取的日期範圍沒有可用的最新資料時，您可能會收到 API 呼叫所傳回的 400 和 404 (無法使用) 錯誤。 例如，您可能會因為最近剛起始註冊轉移而發生此錯誤。 來自特定日期和該日期之後的資料現在會位於新的註冊中。 此外，如果您使用新的註冊號碼來擷取位於舊註冊中的資訊，則可能會發生此錯誤。

## <a name="next-steps"></a>後續步驟

- Azure EA 入口網站系統管理員應閱讀 [ Azure EA 入口網站的系統管理](ea-portal-administration.md)，以了解常見的管理工作。
- 如果您需要 Azure EA 入口網站問題的疑難排解協助，請參閱[針對 Azure EA 入口網站的存取進行疑難排解](ea-portal-troubleshoot.md)。
