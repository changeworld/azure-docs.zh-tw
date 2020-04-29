---
title: 層級和 sku 的服務限制
titleSuffix: Azure Cognitive Search
description: 用於容量規劃的服務限制，以及 Azure 認知搜尋要求和回應的最大限制。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549044"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Azure 認知搜尋中的服務限制

儲存體、工作負載的最大限制，以及索引和其他物件的數量上限，取決於您是否在**免費**、**基本**、**標準**或**儲存體優化**定價層布建[Azure 認知搜尋](search-create-service-portal.md)。

+ **免費** 的是 Azure 訂用帳戶隨附的多租用戶共用服務。 編制索引和查詢要求會在其他租使用者所使用的複本和分割區上執行。

+ **基本**為生產工作負載提供更大規模的專用運算資源，但與其他租使用者共用一些網路基礎結構。

+ **標準**在專用電腦上執行，每個層級都有更多儲存和處理容量。 標準共有四個等級︰S1、S2、S3 及 S3 HD。

+ **儲存體優化**會在儲存體、儲存體頻寬和記憶體總計高於**標準**的專用電腦上執行。 儲存體優化分為兩個層級： L1 和 L2

> [!NOTE]
> 從7月1日起，所有層均已正式運作，包括儲存體優化層。 您可以在 [[定價詳細資料](https://azure.microsoft.com/pricing/details/search/)] 頁面上找到所有定價。

  S3 高密度（S3 HD）是針對特定的工作負載而設計：[多](search-modeling-multitenant-saas-applications.md)租使用者和大量的小型索引（每項服務3000索引）。 這一層不提供[索引子功能](search-indexer-overview.md)。 在 S3 HD 上，資料擷取必須利用推送方法，使用 API 呼叫以將資料從來源推送到索引。 

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
| 每個索引的簡單欄位數目上限 |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| 每個索引的複雜集合欄位數目上限 |40 |40 |40 |40 |40 |40 |40 |40 |
| 每份檔&nbsp;的所有複雜集合的最大元素<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| 複雜欄位的最大深度 |10 |10 |10 |10 |10 |10 |10 |10 |
| 每個索引的[建議工具](https://docs.microsoft.com/rest/api/searchservice/suggesters)上限 |1 |1 |1 |1 |1 |1 |1 |1 |
| 每個索引的[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)上限 |100 |100 |100 |100 |100 |100 |100 |100 |
| 每個設定檔的函式上限 |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup>在2017年12月之前建立的基本服務在索引上有較低的限制（5而不是15）。 基本層是唯一具有較低限制 (每個索引 100 個欄位) 的 SKU。

<sup>2</sup>每份檔的複雜集合中有非常大量的元素，目前會導致高儲存體使用率。 這是已知的問題。 在此同時，限制為3000是所有服務層級的安全上限。 此限制只會針對使用最早正式運作（GA） API 版本的索引編制作業強制執行，而此版本`2019-05-06`可支援複雜類型欄位（）。 若不中斷可能使用舊版預覽 API 版本（支援複雜類型欄位）的用戶端，我們將不會對使用這些預覽 API 版本的索引作業強制執行這項限制。 請注意，預覽 API 版本不適合用于生產案例，我們強烈建議客戶移至最新的 GA API 版本。

> [!NOTE]
> 雖然單一索引的最大容量通常會受限於可用的儲存體，但可儲存在單一索引中的檔總數上會有上限。 針對每個索引的基本、S1、S2 和 S3 搜尋服務，以及每個 S3HD 搜尋服務的2000000000份檔，此限制大約是24000000000個檔。 複雜集合的每個專案都是以不同的檔作為這些限制的用途。

<a name="document-limits"></a>

## <a name="document-limits"></a>文件限制 

從2018年10月開始，任何區域中任何可計費層級（基本、S1、S2、S3、S3 HD）建立的任何新服務已不再有任何檔計數限制。 在2018年10月之前建立的舊版服務仍可能受限於檔計數限制。

若要判斷您的服務是否有檔限制，請使用 [[取得服務統計資料] REST API](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)。 檔限制會反映在回應中， `null`表示沒有限制。

### <a name="document-size-limits-per-api-call"></a>每個 API 呼叫的文件大小限制

呼叫「索引 API」時的文件大小上限大約是 16 MB。

文件大小是索引 API 要求主體大小的實際限制。 由於您可以一次將多份文件整批傳遞給「索引 API」，因此大小限制實際上取決於批次中的文件數量。 針對具有單一文件的批次，文件大小上限為 16 MB 的 JSON。

為了降低文件大小，請記得從要求中排除不可查詢的資料。 影像和其他二進位資料無法執行查詢，而且不應該儲存於索引中。 若要將不可搜尋的資料整合到搜尋結果，請定義不可搜尋的欄位，將 URL 參考儲存於資源中。

## <a name="indexer-limits"></a>索引子限制

最長的執行時間是為了提供整體服務的平衡和穩定性，但較大的資料集所需的索引時間可能比允許的最大值還多。 如果索引作業無法在允許的時間上限內完成，請按照排程嘗試執行索引作業。 排程器會追蹤索引狀態。 如果排定的索引作業因故中斷，索引子可以在下次排定的執行時間繼續從上次停止處進行。


| 資源 | 免費&nbsp;<sup>1</sup> | 基本&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| 索引子上限 |3 |5 或 15|50 |200 |200 |N/A |10 |10 |
| 資料來源上限 |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 技能集上限為 <sup>4</sup> |3 |5 或 15 |50 |200 |200 |N/A |10 |10 |
| 每次叫用的索引編製負載上限 |10,000 份文件 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |僅限制文件上限 |N/A |沒有限制 |沒有限制 |
| 最小排程 | 5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 |5 分鐘 | 5 分鐘 |
| 執行時間上限 <sup>5</sup> | 1-3 分鐘 |24 小時 |24 小時 |24 小時 |24 小時 |N/A  |24 小時 |24 小時 |
| 影像分析的認知搜尋技能集或 Blob 索引適用的執行時間上限 <sup>5</sup> | 3-10 分鐘 |2 小時 |2 小時 |2 小時 |2 小時 |N/A  |2 小時 |2 小時 |
| Blob 索引子︰Blob 大小上限，MB |16 |16 |128 |256 |256 |N/A  |256 |256 |
| Blob 索引子︰從 Blob 擷取的內容字元數上限 |32,000 |64,000 |4&nbsp;百萬 |8&nbsp;百萬 |16&nbsp;百萬 |N/A |4&nbsp;百萬 |4&nbsp;百萬 |

<sup>1</sup> 免費服務有索引子執行時間上限，針對 Blob 來源為 3 分鐘，針對其他所有資料來源為 1 分鐘。 對於呼叫認知服務的 AI 索引，免費服務限制為每天20個免費交易，其中的交易會定義為成功通過擴充管線的檔。

<sup>2</sup> 2017 年12月之前建立的基本服務在索引子、資料來源和技能集上有較低的限制（5而不是15）。

<sup>3</sup> S3 HD 服務不包含索引子支援。

<sup>4</sup> 每個技能集上限為 30 個技術。

<sup>5</sup> 在執行時間方面，Azure Blob 索引中的認知搜尋工作負載和映像分析較一般文字索引短。 影像分析和自然語言處理會耗用大量運算資源，並且需要大量的可用處理能力。 執行時間已減少，佇列中的其他作業才有機會執行。  

> [!NOTE]
> 如[索引限制](#index-limits)所述，從支援複雜類型（`2019-05-06`）的最新 GA API 版本開始，索引子也會在每份檔的所有複雜集合上強制執行3000個元素的上限。 這表示如果您已使用先前的 API 版本建立索引子，則不會受限於此限制。 為了保留最大相容性，使用先前的 API 版本建立的索引子，然後以 API 版本`2019-05-06`或更新版本來更新，仍會從限制中**排除**。 客戶應該留意具有非常龐大複雜集合（如先前所述）的負面影響，而且我們強烈建議使用最新的 GA API 版本來建立任何新的索引子。

## <a name="synonym-limits"></a>同義字限制

允許的同義字對應數目上限會依定價層而有所不同。 每個規則最多可以有20個擴充，其中展開是對等的詞彙。 例如，假設有「cat」，與 "小貓"、"貓科" 和 "felis" （貓的 genus）的關聯會計算為3個擴充。

| 資源 | 免費 | 基本 | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| 同義字地圖的最大值 |3 |3|5 |10 |20 |20 | 10 | 10 |
| 每個對應的規則數目上限 |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>每秒查詢數目 (QPS)

每個客戶必須獨立開發 QPS 估計值。 索引大小和複雜性、查詢大小和複雜性、流量，這三者是 QPS 的主要決定因素。 不知道這些因素，便無法提供有意義的估計值。

計算在專用資源 (基本和標準層) 上執行的服務，更容易預測估計值。 由於可控制較多的參數，所以能更準確地估計 QPS。 如需如何進行估計的指引，請參閱[Azure 認知搜尋效能和優化](search-performance-optimization.md)。

針對儲存體優化層，您應該預期較低的查詢輸送量和比標準層更高的延遲。  評估您將遇到之查詢效能的方法，與標準層相同。

## <a name="data-limits-ai-enrichment"></a>資料限制（AI 擴充）

針對[實體](cognitive-search-skill-entity-recognition.md)辨識、[關鍵字組提取](cognitive-search-skill-keyphrases.md)、[情感分析](cognitive-search-skill-sentiment.md)、[語言偵測](cognitive-search-skill-language-detection.md)和[PII 偵測](cognitive-search-skill-pii-detection.md)呼叫文字分析資源的[AI 擴充管線](cognitive-search-concept-intro.md)，受限於資料限制。 記錄的大小上限應為50000個字元，如所測量[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果您需要先分割資料，再將該資料傳送至情感分析器，請使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="throttling-limits"></a>節流限制

搜尋查詢和索引編制要求會因為系統接近尖峰容量而受到節流。 不同 Api 的節流行為不同。 查詢 Api （搜尋/建議/自動完成）和根據服務的負載動態地建立 Api 節流。 索引 Api 具有靜態要求速率限制。 

與索引相關之作業的靜態速率要求限制：

+ 列出索引（GET/indexes）：每個搜尋單位每秒5個
+ 取得索引（GET/indexes/myindex）：每個搜尋單位每秒10次
+ 建立索引（POST/indexes）：每個搜尋單位每分鐘12次
+ 建立或更新索引（PUT/indexes/myindex）：每個搜尋單位每秒6次
+ 刪除索引（刪除/indexes/myindex）：每個搜尋單位每分鐘12次 

## <a name="api-request-limits"></a>API 要求限制
* 每個要求最多 16 MB <sup>1</sup>
* 最長 8 KB 的 URL 長度
* 每個索引上傳、合併、或刪除批次最多包含 1000 個文件
* $orderby 子句中最多 32 個欄位
* 最大搜尋詞彙的大小是 utf-8 編碼文字的 32,766 個位元組 (32 KB 減 2 個位元組)

<sup>1</sup>在「Azure 認知搜尋」中，要求主體的上限是 16 MB，限制不會受到理論限制約束之個別欄位或集合內容的實際限制（如需有關欄位組合和限制的詳細資訊，請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)）。

## <a name="api-response-limits"></a>API 回應限制
* 每一頁搜尋結果最多傳回 1000 個文件
* 每個建議 API 要求最多傳回 100 個建議

## <a name="api-key-limits"></a>API 金鑰限制
API 金鑰用於服務驗證。 有兩種類型。 系統管理金鑰是在要求標頭中指定，並會授與完整的服務讀寫存取。 查詢金鑰為唯讀並在 URL 上指定，且通常會發佈到用戶端應用程式。

* 每個服務最多 2 個系統管理金鑰
* 每個服務最多 50 個查詢金鑰