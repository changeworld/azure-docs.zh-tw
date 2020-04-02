---
title: 層與 sus 的服務限制
titleSuffix: Azure Cognitive Search
description: 用於容量規劃的服務限制以及 Azure 認知搜索的請求和回應的最大限制。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549044"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Azure 認知搜尋中的服務限制

儲存、工作負荷、索引和其他物件數量的最大限制取決於您是在**免費**、**基本**、**標準**或**儲存最佳化**定價層[預配 Azure 認知搜尋](search-create-service-portal.md)。

+ **免費** 的是 Azure 訂用帳戶隨附的多租用戶共用服務。 索引和查詢請求在其他租戶使用的副本和分區上執行。

+ **Basic**為較小規模的生產工作負載提供專用計算資源,但與其他租戶共用一些網路基礎結構。

+ **標準**運行在專用計算機上,具有更多存儲和處理能力。 標準共有四個等級︰S1、S2、S3 及 S3 HD。

+ **儲存優化**在具有更多總儲存、儲存頻寬和記憶體的專用電腦上執行,比**標準**。 儲存優化有兩個等級:L1 和 L2

> [!NOTE]
> 自 7 月 1 日起,所有層通常可用,包括"存儲優化" 層。 所有定價都可以在[「定價詳細資訊」](https://azure.microsoft.com/pricing/details/search/)頁上找到。

  S3 高密度 (S3 HD) 專為特定工作負載而設計:[多租戶](search-modeling-multitenant-saas-applications.md)和大量小索引(每個服務三千個索引)。 這一層不提供[索引子功能](search-indexer-overview.md)。 在 S3 HD 上，資料擷取必須利用推送方法，使用 API 呼叫以將資料從來源推送到索引。 

> [!NOTE]
> 服務會佈建在特定層。 跨層以取得容量牽涉到佈建新服務 (未提供就地升級)。 如需詳細資訊，請參閱[選擇 SKU 或階層](search-sku-tier.md)。 若要深入了解如何在已佈建的服務內調整容量，請參閱[調整適用於查詢和編製索引工作負載的資源等級](search-capacity-planning.md)。
>

## <a name="subscription-limits"></a>訂用帳戶限制
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>儲存體限制
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>索引限制

| 資源 | 免費 | 基本&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| 索引上限 |3 |5 或 15 |50 |200 |200 |每個分割區 1000 個或每個服務 3000 個 |10 |10 |
| 每個索引的最大簡易欄位 |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| 每個索引的最大複雜集合欄位 |40 |40 |40 |40 |40 |40 |40 |40 |
| 每個文件&nbsp;<sup>2</sup>的所有複雜集合的最大元素 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| 複雜欄位的最大深度 |10 |10 |10 |10 |10 |10 |10 |10 |
| 每個索引的[建議工具](https://docs.microsoft.com/rest/api/searchservice/suggesters)上限 |1 |1 |1 |1 |1 |1 |1 |1 |
| 每個索引的[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)上限 |100 |100 |100 |100 |100 |100 |100 |100 |
| 每個設定檔的函式上限 |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> 2017 年 12 月之前創建的基本服務對索引的限制較低(5 而不是 15)。 基本層是唯一具有較低限制 (每個索引 100 個欄位) 的 SKU。

<sup>2</sup>每個文件的複雜集合中具有非常大量的元素,目前會導致高存儲利用率。 這是已知的問題。 同時,限制 3000 是所有服務層的安全上限。 此限制僅適用於使用支援複雜類型欄位 ()`2019-05-06`的最早可用 (GA) API 版本的索引操作。 為了不中斷可能使用早期預覽 API 版本(支援複雜類型欄位)的用戶端,我們不會對使用這些預覽 API 版本的索引操作強制實施此限制。 請注意,預覽 API 版本不用於生產方案,我們強烈建議客戶遷移到最新的 GA API 版本。

> [!NOTE]
> 雖然單個索引的最大容量通常受可用存儲的限制,但可以存儲在單個索引中的文檔總數上存在最大上限。 此限制是基本、S1、S2 和 S3 搜索服務的每個索引大約 240 億個文檔,S3HD 搜索服務每個索引的 20 億個文檔。 為了這些限制的目的,複雜集合的每個元素都算作單獨的文檔。

<a name="document-limits"></a>

## <a name="document-limits"></a>文件限制 

自 2018 年 10 月起,任何區域(基本、S1、S2、S3、S3 HD)在任何計費層(基本、S1、S2、S3、S3 HD)上創建的任何新服務都不再有任何文檔計數限制。 2018 年 10 月之前創建的舊服務仍可能受文檔計數限制。

要確定服務是否具有文件限制,請使用[GET 服務統計資訊 REST API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)。 文檔限制反映在回應中,`null`指示沒有限制。

### <a name="document-size-limits-per-api-call"></a>每個 API 呼叫的文件大小限制

呼叫「索引 API」時的文件大小上限大約是 16 MB。

文件大小是索引 API 要求主體大小的實際限制。 由於您可以一次將多份文件整批傳遞給「索引 API」，因此大小限制實際上取決於批次中的文件數量。 針對具有單一文件的批次，文件大小上限為 16 MB 的 JSON。

為了降低文件大小，請記得從要求中排除不可查詢的資料。 影像和其他二進位資料無法執行查詢，而且不應該儲存於索引中。 若要將不可搜尋的資料整合到搜尋結果，請定義不可搜尋的欄位，將 URL 參考儲存於資源中。

## <a name="indexer-limits"></a>索引子限制

存在最大運行時間,以為整個服務提供平衡和穩定性,但較大的數據集可能需要比最大值允許的更多索引時間。 如果索引作業無法在允許的時間上限內完成，請按照排程嘗試執行索引作業。 排程器會追蹤索引狀態。 如果排定的索引作業因故中斷，索引子可以在下次排定的執行時間繼續從上次停止處進行。


| 資源 | 免費&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| 索引子上限 |3 |5 或 15|50 |200 |200 |N/A |10 |10 |
| 資料來源上限 |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 技能集上限為 <sup>4</sup> |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 每次叫用的索引編製負載上限 |10,000 份文件 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |N/A |沒有限制 |沒有限制 |
| 最小時間表 | 5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 | 5 分鐘 |
| 執行時間上限 <sup>5</sup> | 1-3 分鐘 |24 小時 |24 小時 |24 小時 |24 小時 |N/A  |24 小時 |24 小時 |
| 影像分析的認知搜尋技能集或 Blob 索引適用的執行時間上限 <sup>5</sup> | 3-10 分鐘 |2 小時 |2 小時 |2 小時 |2 小時 |N/A  |2 小時 |2 小時 |
| Blob 索引子︰Blob 大小上限，MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Blob 索引子︰從 Blob 擷取的內容字元數上限 |32,000 |64,000 |400&nbsp;萬 |800&nbsp;萬 |1600&nbsp;萬 |N/A |400&nbsp;萬 |400&nbsp;萬 |

<sup>1</sup> 免費服務有索引子執行時間上限，針對 Blob 來源為 3 分鐘，針對其他所有資料來源為 1 分鐘。 對於調用認知服務的 AI 索引,免費服務限制為每天 20 個免費事務,其中事務定義為成功通過豐富管道的文檔。

<sup>2</sup> 2017 年 12 月之前創建的基本服務對索引器、數據源和技能集的限制較低(5 而不是 15)。

<sup>3</sup> S3 HD 服務不包含索引子支援。

<sup>4</sup> 每個技能集上限為 30 個技術。

<sup>5</sup> 在執行時間方面，Azure Blob 索引中的認知搜尋工作負載和映像分析較一般文字索引短。 影像分析和自然語言處理會耗用大量運算資源，並且需要大量的可用處理能力。 執行時間已減少，佇列中的其他作業才有機會執行。  

> [!NOTE]
> 如[索引限制](#index-limits)所述,索引器還將從支援複雜類型`2019-05-06`() 的最新 GA API 版本開始,對每個文檔的所有複雜集合強制實施 3000 個元素的上限。 這意味著,如果您已使用以前的 API 版本創建了索引器,則不受此限制的約束。 為了保持最大相容性,使用以前的 API 版本建立然後使用`2019-05-06`API 版本 更新的索引器仍將從限制**中排除**。 客戶應瞭解具有非常大的複雜集合(如前所述)的不利影響,我們強烈建議使用最新的 GA API 版本創建任何新的索引器。

## <a name="synonym-limits"></a>同義字限制

允許的最大同義詞映射數因定價層而異。 每個規則最多可以有 20 個擴展,其中擴展是等效術語。 例如,給定"貓",與"貓","貓"和"felis"(貓的屬)的關聯將算作3個擴展。

| 資源 | 免費 | 基本 | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| 最大同義詞貼圖 |3 |3|5 |10 |20 |20 | 10 | 10 |
| 每個地圖的最大規則數 |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>每秒查詢數目 (QPS)

每個客戶必須獨立開發 QPS 估計值。 索引大小和複雜性、查詢大小和複雜性、流量，這三者是 QPS 的主要決定因素。 不知道這些因素，便無法提供有意義的估計值。

計算在專用資源 (基本和標準層) 上執行的服務，更容易預測估計值。 由於可控制較多的參數，所以能更準確地估計 QPS。 有關如何接近估計的指導,請參閱 Azure[認知搜尋性能和優化](search-performance-optimization.md)。

對於「存儲優化」層,應期望查詢輸送量更低,延遲率應高於標準層。  估計您將遇到的查詢性能的方法與標準層相同。

## <a name="data-limits-ai-enrichment"></a>資料限制(AI 擴充)

AI[擴充導管](cognitive-search-concept-intro.md),用於實體[識別](cognitive-search-skill-entity-recognition.md)、[關鍵短語提取](cognitive-search-skill-keyphrases.md)、[情緒分析](cognitive-search-skill-sentiment.md)、[語言偵測](cognitive-search-skill-language-detection.md)和[PII 檢測](cognitive-search-skill-pii-detection.md)的文本分析資源受資料限制。 紀錄的最大大小應為 50,000 個字元[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)(以 ) 如果您需要先分割資料，再將該資料傳送至情感分析器，請使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="throttling-limits"></a>限制限制

隨著系統接近峰值容量,搜索查詢和索引請求將受到限制。 對於不同的 API,限制行為方式不同。 查詢 API(搜索/建議/自動完成)和索引 API 會根據服務上的負載動態限制。 索引 API 具有靜態請求速率限制。 

與索引相關的操作的靜態速率請求限制:

+ 清單索引(GET /索引):每個搜尋單元每秒 5 個
+ 取得索引(GET /index/myindex):每個搜尋單元每秒 10 個
+ 建立索引 (POST /索引):每個搜尋單元每分鐘 12 個
+ 建立或更新索引(PUT /index/myindex):每個搜尋單位每秒 6 個
+ 刪除索引(刪除/索引/myindex):每個搜尋單元每分鐘 12 個 

## <a name="api-request-limits"></a>API 要求限制
* 每個要求最多 16 MB <sup>1</sup>
* 最長 8 KB 的 URL 長度
* 每個索引上傳、合併、或刪除批次最多包含 1000 個文件
* $orderby 子句中最多 32 個欄位
* 最大搜尋詞彙的大小是 utf-8 編碼文字的 32,766 個位元組 (32 KB 減 2 個位元組)

<sup>1</sup>在 Azure 認知搜索中,請求正文的上限為 16 MB,對不受理論限制的單個字段或集合的內容施加實際限制(有關欄位組合和限制的詳細資訊,請參閱[支持數據類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types))。

## <a name="api-response-limits"></a>API 回應限制
* 每一頁搜尋結果最多傳回 1000 個文件
* 每個建議 API 要求最多傳回 100 個建議

## <a name="api-key-limits"></a>API 金鑰限制
API 金鑰用於服務身份驗證。 有兩種類型。 系統管理金鑰是在要求標頭中指定，並會授與完整的服務讀寫存取。 查詢金鑰為唯讀並在 URL 上指定，且通常會發佈到用戶端應用程式。

* 每個服務最多 2 個系統管理金鑰
* 每個服務最多 50 個查詢金鑰