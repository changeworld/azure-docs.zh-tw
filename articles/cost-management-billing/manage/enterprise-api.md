---
title: Azure 計費企業版 API
description: 了解可讓企業 Azure 客戶以程式設計方式提取使用情況資料的報告 API。
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b78b046e2991fa4ebfb575e4f6a1cc900df0d298
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686899"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>適用於企業客戶的報告 API 概觀

> [!Note]
> Microsoft 不會再更新 Azure 計費 - 企業報告 API。 相反地，請改為使用 [Azure 使用量](/rest/api/consumption) API。

報告 API 可讓企業 Azure 客戶以程式設計方式提取使用情況和帳單資料，以使用慣用的資料分析工具進行分析。 企業客戶已經與 Azure 簽署一份 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)，以便進行綁約金協商，並獲得 Azure 資源的自訂價格。

API 所需的所有日期和時間參數都必須以混合的國際標準時間 (UTC) 值表示。 API 所傳回的值會以 UTC 格式顯示。

## <a name="enabling-data-access-to-the-api"></a>啟用對 API 的資料存取
* **產生或擷取 API 金鑰** - 登入企業版入口網站，並瀏覽至 [報告] > [下載使用量] > [API 存取金鑰] 來產生或擷取 API 金鑰。
* **在 API 中傳遞金鑰** - 必須針對每個驗證和授權呼叫傳遞 API 金鑰。 下列屬性必須是針對 HTTP 標頭

|要求標頭金鑰 | 值|
|-|-|
|授權| 以此格式指定值：**bearer {API_KEY}** <br/> 範例：bearer eyr....09|

## <a name="consumption-based-apis"></a>以使用量為基礎的 API
下述 API 的 Swagger 端點可在[這裡](https://consumption.azure.com/swagger/ui/index)取得，它可讓使用者輕鬆進行 API 自我檢查，並且能使用 [AutoRest](https://github.com/Azure/AutoRest) 或 [Swagger CodeGen](https://swagger.io/swagger-codegen/) 產生用戶端 SDK。 自 2014 年 5 月 1 日起的資料可透過此 API 取得。

* **餘額與摘要** - [餘額與摘要 API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) 可提供餘額、新購買、Azure Marketplace 服務費用、調整，以及超額部分費用的每月摘要資訊。

* **使用量詳細資料** - [使用量詳細資料 API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) 可提供註冊之使用量和預估費用的每日明細。 結果也包含執行個體、計量和部門的資訊。 API 可依計費週期或指定開始和結束日期來查詢。

* **Marketplace 市集費用** - [Marketplace 市集費用 API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) 可針對指定的計費週期或開始和結束日期，傳回以使用量為基礎的 Marketplace 費用每日明細 (不含一次性費用)。

* **價位表** - [價位表 API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 可針對指定註冊和計費週期的每個計量提供適用的費率。

* **保留執行個體詳細資料** - [保留執行個體使用量 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) 會傳回所購買保留執行個體的使用量。 [保留執行個體費用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) 會顯示所進行的計費交易。

## <a name="data-freshness"></a>資料有效期限
系統會傳回 Etag 以回應上述所有 API。 Etag 的變更表示資料已重新整理。  在使用相同參數對相同 API 進行的後續呼叫中，傳遞所擷取的 Etag，在其中的 http 要求標頭中包含索引鍵 "If-None-Match"。 如果資料未進一步重新整理且未傳回任何資料，則回應狀態碼會是 "NotModified"。 每當 etag 有變更時，API 就會針對所需期間傳回完整的資料集。

## <a name="helper-apis"></a>協助程式 API
 **列出計費週期** - [計費週期 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 會傳回計費週期清單，其中包含所指定註冊的使用情況資料 (以反向時間順序排列)。 每個週期包含指向四組資料 (BalanceSummary、UsageDetails、Marketplace 費用和價位表) 之 API 路由的屬性。


## <a name="api-response-codes"></a>API 回應碼   
|回應狀態碼|訊息|描述|
|-|-|-|
|200| [確定]|沒有錯誤|
|400| 不正確的要求| 無效的參數 - 資料範圍、EA 編號等。|
|401| 未經授權| API 金鑰找不到、無效或過期等。|
|404| 無法使用| 找不到報告端點|
|429 | TooManyRequests | 要求已節流處理。 請在等候 <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> 標頭中指定的時間之後重試。|
|500| 伺服器錯誤| 處理要求時發生未預期的錯誤|
| 503 | ServiceUnavailable | 這項服務暫時無法使用。 請在等候 <code>Retry-After</code> 標頭中指定的時間之後重試。|
