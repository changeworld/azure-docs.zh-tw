---
title: 索引子錯誤和警告
titleSuffix: Azure Cognitive Search
description: 本文提供在 Azure 認知搜尋的 AI 擴充期間，您可能會遇到的常見錯誤和警告的資訊和解決方案。
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 8ceb6d4dddb76148be1e82ebc8c1994886a11da3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362809"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中常見的索引子錯誤和警告進行疑難排解

本文提供您在 Azure 認知搜尋中編制索引和 AI 擴充時可能遇到的常見錯誤和警告的資訊和解決方案。

當錯誤計數超過 [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)時，就會停止索引。 

如果您想要讓索引子忽略這些錯誤 (並略過「失敗的檔」 ) ，請考慮更新 `maxFailedItems` 和， `maxFailedItemsPerBatch` 如下[here](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每個失敗的檔以及其檔索引鍵 (當可用) 將會在索引子執行狀態中顯示為錯誤。 如果您已將索引子設定為容許失敗，您可以利用 [索引 api](/rest/api/searchservice/addupdate-or-delete-documents) ，稍後再手動上傳檔。

本文中的錯誤資訊可協助您解決錯誤，讓您可以繼續編制索引。

警告不會停止索引，但會指出可能導致非預期結果的狀況。 您是否採取動作，而不是取決於資料和您的案例。

從 API 版本開始 `2019-05-06` ，專案層級的索引子錯誤和警告會結構化，以提供更清楚的原因和後續步驟。 它們包含下列屬性：

| 屬性 | 說明 | 範例 |
| --- | --- | --- |
| 索引鍵 | 受錯誤或警告影響之檔的檔識別碼。 | HTTPs： \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| NAME | 描述發生錯誤或警告之位置的作業名稱。 這是由下列結構產生： [category]。[子類別]。[resourceType]。CoNtext.resourcename | DocumentExtraction. azureblob. myBlobContainerName 擴充. WebApiSkill. mySkillName 投射. SearchIndex. OutputFieldMapping. myOutputFieldName SearchIndex. MergeOrUpload. myIndexName |
| 訊息 | 錯誤或警告的高層級描述。 | 因為 Web Api 要求失敗，所以無法執行技能。 |
| 詳細資料 | 可能有助於診斷問題的任何其他詳細資料，例如 WebApi 回應（如果執行自訂技能失敗）。 | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1來源、Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` .。。其餘的堆疊追蹤 .。。 |
| documentationLink | 相關檔的連結，其中包含偵測和解決問題的詳細資訊。 此連結通常會指向此頁面的下列其中一節。 | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>錯誤：無法讀取檔

索引子無法從資料來源讀取檔。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 跨不同檔的欄位類型不一致 | 「值的類型與資料行類型不相符。 無法儲存 `'{47.6,-122.1}'` 在作者資料行中。  預期的類型為 JArray。」  「將資料類型 Nvarchar 轉換成 float 時發生錯誤」。  「將 Nvarchar 值 ' 12 months ' 轉換成資料類型 int 時，轉換失敗。」  「轉換運算式到資料類型 int 時發生算術溢位錯誤。」 | 確定每個欄位的類型在不同的檔之間都相同。 例如，如果第一個檔 `'startTime'` 欄位是日期時間，而第二份檔是一個字串，就會發生此錯誤。 |
| 來自資料來源基礎服務的錯誤 | 從 Cosmos DB)  (`{"Errors":["Request rate is large"]}` | 檢查您的儲存體實例，以確定其狀況良好。 您可能需要調整您的調整/分割。 |
| 暫時性問題 | 從伺服器接收結果時發生傳輸層級錯誤。  (提供者： TCP 提供者，錯誤： 0-遠端主機已強制關閉現有的連接 | 有時會發生未預期的連線問題。 稍後再試一次使用您的索引子執行檔。 |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>錯誤：無法從您的檔中解壓縮內容或中繼資料
具有 Blob 資料來源的索引子無法從檔中解壓縮內容或中繼資料 (例如，) 的 PDF 檔案。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| blob 超過大小限制 | 檔是 `'150441598'` 位元組，超過 `'134217728'` 目前服務層級的檔解壓縮大小上限位元組。 | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob 具有不支援的內容類型 | 檔有不支援的內容類型 `'image/png'` | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob 已加密 | 無法處理檔-可能已加密或密碼受到保護。 | 您可以略過具有 [blob 設定](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)的 blob。 |
| 暫時性問題 | 「處理 blob 時發生錯誤：要求已中止：要求已取消。」 「處理期間的檔超時」。 | 有時會發生未預期的連線問題。 稍後再試一次使用您的索引子執行檔。 |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>錯誤：無法剖析檔
索引子從資料來源讀取檔，但是將檔內容轉換成指定的欄位對應架構時發生問題。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 檔索引鍵遺失 | 檔索引鍵不能遺失或空白 | 確定所有檔都有有效的檔索引鍵。 檔索引鍵的決定方式是將 ' key ' 屬性設定為 [索引定義](/rest/api/searchservice/create-index#request-body)的一部分。 在特定檔上找不到標示為 ' key ' 的屬性時，索引子會發出此錯誤。 |
| 檔索引鍵無效 | 檔索引鍵不能超過1024個字元 | 修改檔金鑰以符合驗證需求。 |
| 無法將欄位對應套用至欄位 | 無法將對應函數套用 `'functionName'` 至欄位 `'fieldName'` 。 陣列不能是 null。 參數名稱：位元組 | 再次檢查索引子上定義的 [欄位](search-indexer-field-mappings.md) 對應，並與失敗檔的指定欄位資料進行比較。 可能需要修改欄位對應或檔資料。 |
| 無法讀取域值 | 無法讀取索引處的資料行值 `'fieldName'` `'fieldIndex'` 。 從伺服器接收結果時發生傳輸層級錯誤。 (提供者: TCP 提供者，錯誤: 0 - 遠端主機已強制關閉一個現存的連線)。 | 這些錯誤通常是因為資料來源的基礎服務發生非預期的連接問題。 稍後再試一次使用您的索引子執行檔。 |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>錯誤： `xyz` 因為套用對應函數 ' ' 時發生還原序列化問題，所以無法將輸出欄位 ' ' 對應到搜尋索引 `abc`
輸出對應可能失敗，因為輸出資料的格式不正確，無法用於您所使用的對應函數。 例如，在二進位資料上套用 Base64Encode 對應函數會產生此錯誤。 若要解決此問題，請重新執行索引子，但不要指定對應函數，或確定對應函式與輸出欄位資料類型相容。 如需詳細資料，請參閱 [輸出欄位對應](cognitive-search-output-field-mapping.md) 。

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>錯誤：無法執行技能
索引子無法在技能集中執行技能。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 暫時性連接問題 | 發生暫時性錯誤。 請稍後再試一次。 | 有時會發生未預期的連線問題。 稍後再試一次使用您的索引子執行檔。 |
| 潛在的產品 bug | 發生意外錯誤。 | 這表示不明類別的失敗，可能表示有產品錯誤。 請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) 以取得協助。 |
| 技能在執行期間發生錯誤 | 從合併技能 () 有一或多個位移值無效，而且無法剖析。 專案已插入至文字的結尾 | 使用錯誤訊息中的資訊來修正問題。 這類失敗將需要採取動作才能解決問題。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>錯誤：因為 Web API 要求失敗，所以無法執行技能
因為對 Web API 的呼叫失敗，所以技能執行失敗。 一般而言，使用自訂技能時，就會發生這個失敗類別，在這種情況下，您必須將自訂程式碼進行調試以解決問題。 如果從內建的技能改為失敗，請參閱錯誤訊息，以取得修正問題的協助。

在偵測此問題時，請務必留意此技能的任何 [技能輸入警告](#warning-skill-input-was-invalid) 。 因為索引子正在傳遞非預期的輸入，所以您的 Web API 端點可能會失敗。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>錯誤：因為 Web API 技能回應無效，所以無法執行技能
因為對 Web API 的呼叫傳回的回應無效，所以技能執行失敗。 一般而言，使用自訂技能時，就會發生這個失敗類別，在這種情況下，您必須將自訂程式碼進行調試以解決問題。 如果從內建的技能改為失敗，請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) 以取得協助。

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>錯誤：技能未在時間限制內執行
在兩種情況下，您可能會遇到這個錯誤訊息，每一個都應該以不同的方式處理。 請依照下列指示進行，視您的技能傳回此錯誤而定。

### <a name="built-in-cognitive-service-skills"></a>內建認知服務技能
許多內建的認知技能（例如語言偵測、實體辨識或 OCR）都是由認知服務 API 端點所支援。 有時候這些端點有暫時性的問題，而且要求會超時。針對暫時性問題，除了等待並再試一次，沒有任何補救。 在風險降低的情況下，請考慮將您的索引子設定為依 [排程執行](search-howto-schedule-indexers.md)。 排程的索引編制會在中斷的地方繼續進行。 假設暫時性問題已解決，索引編制和認知技能處理應該可以在下一次排程執行時繼續執行。

如果您在同一份檔中持續看到內建認知技能的這個錯誤，請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) 以取得協助，因為這不是預期的情況。

### <a name="custom-skills"></a>自訂技能
如果您所建立的自訂技能遇到逾時錯誤，您可以嘗試幾個動作。 首先，請檢查您的自訂技能，並確定其不會陷入無限迴圈，並以一致的方式傳回結果。 一旦您確認了這種情況，請判斷您的技能執行時間。 如果您未 `timeout` 在自訂技能定義上明確設定值，則預設值 `timeout` 為30秒。 如果30秒的時間不夠長，無法執行您的技能，您可以 `timeout` 在自訂技能定義上指定更高的值。 以下是自訂技能定義的範例，其中的 timeout 設定為90秒：

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

您可以為參數設定的最大值 `timeout` 為230秒。  如果您的自訂技能無法在230秒內一致地執行，您可以考慮減少 `batchSize` 自訂技能的，使其在單一執行中會有較少的檔處理。  如果您已將設定 `batchSize` 為1，則必須重新撰寫技能，才能在230秒內執行，或將其分割成多個自訂技能，讓任何單一自訂技能的執行時間最多可達230秒。 請參閱 [自訂技能檔](cognitive-search-custom-skill-web-api.md) 以取得詳細資訊。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>錯誤：無法 ' `MergeOrUpload` ' |' `Delete` ' 檔到搜尋索引

檔已讀取和處理，但索引子無法將它加入至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 欄位包含太大的詞彙 | 檔中的詞彙大於 [32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 您可以確保欄位未設定為可篩選、可 facet 或可排序，藉以避免這項限制。
| 檔太大，無法編制索引 | 檔大於 [api 要求大小上限](search-limits-quotas-capacity.md#api-request-limits) | [如何為大型資料集編制索引](search-howto-large-index.md)
| 檔在集合中包含太多物件 | 檔中的集合超過 [所有複雜集合的最大專案數限制](search-limits-quotas-capacity.md#index-limits) 「具有索引鍵的 `'1000052'` 檔 `'4303'` 在集合中有物件 (JSON 陣列) 。 大部分 `'3000'` 的物件都允許位於整個檔的集合中。 請移除集合中的物件，然後再次嘗試編制檔索引。」 | 建議您將檔中複雜集合的大小縮減為低於限制，並避免高儲存空間的使用率。
| 因為服務處於其他負載（例如查詢或編制索引），所以無法連接到在重試後保存的目標索引 () 。 | 無法建立連接來更新索引。 搜尋服務負載過重。 | [擴大您的搜尋服務](search-capacity-planning.md)
| 搜尋服務正在修補以進行服務更新，或處於拓撲重新設定的中間。 | 無法建立連接來更新索引。 搜尋服務目前已關閉/搜尋服務正在進行轉換。 | 使用至少3個複本來設定服務，以達每一 SLA 99.9% 的可用性 [檔](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| 基礎計算/網路資源的失敗 (罕見的)  | 無法建立連接來更新索引。 發生未知錯誤。 | 設定要依 [排程執行](search-howto-schedule-indexers.md) 的索引子，以便從失敗狀態中收取。
| 由於網路問題，不會在超時期間內確認對目標索引提出的索引要求。 | 無法及時建立與搜尋索引的連接。 | 設定要依 [排程執行](search-howto-schedule-indexers.md) 的索引子，以便從失敗狀態中收取。 此外，如果此錯誤狀況持續發生，請嘗試降低索引子 [批次大小](/rest/api/searchservice/create-indexer#parameters) 。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>錯誤：無法為檔編制索引，因為某些檔的資料無效

檔是由索引子讀取和處理，但由於索引欄位的設定不相符，以及索引子所解壓縮和處理的資料不相符，因此無法將它加入至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例
| --- | ---
| 索引子所解壓縮)  (s 的資料類型與對應目標索引欄位的資料模型不相容。 | 檔中索引鍵為 ' 888 ' 的資料欄位 '_data_' 具有類型 ' Edm ' 的無效值 '。 預期的類型為 ' Collection (Edm. String) '。 |
| 無法從字串值解壓縮任何 JSON 實體。 | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 的值 ' 剖析為 JSON 物件。 錯誤： ' 剖析值之後發現未預期的字元： ' '。 路徑 '_path_'、行1、位置3162。 |
| 無法從字串值解壓縮 JSON 實體集合。  | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 的值 ' 剖析為 JSON 陣列。 錯誤： ' 剖析值之後發現未預期的字元： ' '。 路徑 ' [0] '、行1、位置 27. ' |
| 在來源文件中發現未知的類型。 | 無法編制未知的類型「_未知_」 |
| 來源文件中使用了地理位置點的不相容標記法。 | 不支援 WKT 點字串常值。 請改為使用 GeoJson 點常值 |

在所有這些情況下，請參閱 [支援的資料類型](/rest/api/searchservice/supported-data-types) 和 [索引子的資料類型對應](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) ，以確定您已正確建立索引架構，並已設定適當的 [索引子欄位](search-indexer-field-mappings.md)對應。 錯誤訊息會包含有助於追蹤不相符之來源的詳細資料。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>錯誤：因為資料表具有複合主鍵，所以無法使用整合式變更追蹤原則

這適用于 SQL 資料表，通常會在索引鍵定義為複合索引鍵時，或是當資料表已將唯一的叢集索引定義 (的 SQL 索引中（而非 Azure 搜尋服務索引) ）時發生。 主要的原因是在 [唯一叢集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)的情況下，索引鍵屬性會修改為複合主鍵。 在這種情況下，請確定您的 SQL 資料表沒有唯一的叢集索引，或您將索引鍵欄位對應至保證不會有重複值的欄位。

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>錯誤：無法在索引子執行時間上限中處理檔

當索引子無法在允許的執行時間內完成處理來自資料來源的單一檔時，就會發生這個錯誤。 使用技能集時，[執行時間上限](search-limits-quotas-capacity.md#indexer-limits)較短。 當發生這個錯誤時，如果您將 maxFailedItems 設定為0以外的值，則索引子會在未來執行時略過檔，讓索引可以進行。 如果您無法容忍略過任何檔，或如果您持續看到此錯誤，請考慮將檔分解成較小的檔，以便在單一索引子執行中進行部分進度。

<name = "無法進行專案檔案></a>

## <a name="error-could-not-project-document"></a>錯誤：無法投影檔

當索引子嘗試將 [資料投射到知識存放區](knowledge-store-projection-overview.md) ，而且嘗試進行這項操作時，就會發生這個錯誤。  此失敗可能是一致且可修復的，或可能是因為投射輸出接收的暫時性失敗，而您可能需要等待再重試，才能解決問題。  以下是一組已知的失敗狀態和可能的解決方式。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 無法更新 `'blobUri'` 容器中的投射 blob `'containerName'` |指定的容器不存在。 | 索引子會檢查先前是否已建立指定的容器，並在必要時加以建立，但它只會針對每個索引子執行執行一次檢查。 此錯誤表示在此步驟之後刪除容器的內容。  若要解決此錯誤，請嘗試下列動作：單獨保留您的儲存體帳戶資訊，等候索引子完成，然後重新執行索引子。 |
| 無法更新 `'blobUri'` 容器中的投射 blob `'containerName'` |無法將資料寫入傳輸連接：遠端主機已強制關閉現有的連接。 | 這應該是暫時性的 Azure 儲存體失敗，因此應該透過重新執行索引子來解決。 如果您一致地遇到此錯誤，請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) ，讓它可以進一步調查。  |
| 無法更新 `'projectionRow'` 資料表中的資料列 `'tableName'` | 伺服器忙碌中。 | 這應該是暫時性的 Azure 儲存體失敗，因此應該透過重新執行索引子來解決。 如果您一致地遇到此錯誤，請提出 [支援票證](https://ms.portal.azure.com/#create/Microsoft.Support) ，讓它可以進一步調查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>警告：技能輸入無效
缺少技能的輸入、錯誤的類型或其他不正確問題。 警告訊息會指出影響：
1) 無法執行技能
2) 執行的技能，但可能有非預期的結果

認知技能有必要的輸入和選擇性輸入。 例如， [關鍵字組提取技能](cognitive-search-skill-keyphrases.md) 有兩個必要的輸入 `text` ，以及 `languageCode` 無選擇性輸入。 自訂技能輸入全都視為選擇性輸入。

如果遺漏任何必要的輸入，或是任何輸入不是正確的類型，就會略過技能並產生警告。 略過的技能不會產生任何輸出，因此，如果其他技能使用略過之技能的輸出，可能會產生額外的警告。

如果遺漏選擇性的輸入，則技能仍會執行，但可能會因為遺漏輸入而產生未預期的輸出。

在這兩種情況下，可能會因為您的資料形狀而預期會出現此警告。 例如，如果您有一份檔，其中包含具有欄位、和的人員相關資訊， `firstName` `middleName` `lastName` 您可能會有一些檔沒有的專案 `middleName` 。 如果您要以 `middleName` 輸入的形式傳遞至管線中的技能，則預期這項技能的輸入可能會在某些時間內遺失。 您必須評估您的資料和案例，以判斷是否因此警告而需要任何動作。

如果您想要在遺失輸入時提供預設值，您可以使用 [條件式技能](cognitive-search-skill-conditional.md) 來產生預設值，然後使用 [條件式技能](cognitive-search-skill-conditional.md) 的輸出作為技能輸入。


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 技能輸入錯誤的類型 | 「所需的技能輸入不是預期的類型 `String` 。 Name： `text` ，Source： `/document/merged_content` . "  「所需的技能輸入不是預期的格式。 Name： `text` ，Source： `/document/merged_content` . "  「無法反復查看非陣列」 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。  「無法 `0` 在非陣列中選取」 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` | 某些技能預期會有特定類型的輸入，例如 [情感技能](cognitive-search-skill-sentiment.md) 預期 `text` 會是字串。 如果輸入指定非字串值，則技能不會執行，也不會產生任何輸出。 確定您的資料集在類型中具有統一的輸入值，或使用 [自訂 WEB API 技能](cognitive-search-custom-skill-web-api.md) 來預先處理輸入。 如果您要逐一查看陣列的技能，請檢查技能內容，並輸入 `*` 正確的位置。 通常內容和輸入來源的結尾都應該是 `*` 陣列。 |
| 缺少技能輸入 | 「缺少必要的技能輸入。 名稱： `text` ，來源：「 `/document/merged_content` 遺漏值」 `/document/normalized_images/0/imageTags` 。  「無法選取 `0` 長度為數組 `/document/pages` 的陣列」 `0` 。 | 如果您的所有檔都收到這項警告，則輸入路徑中可能會出現打字錯誤，您應該在路徑中重複檢查屬性名稱大小寫、額外或遺漏 `*` ，並確定資料來源中的檔提供必要的輸入。 |
| 技能語言代碼輸入無效 | 技能輸入 `languageCode` 具有下列語言代碼 `X,Y,Z` ，其中至少有一個是不正確。 | 請參閱[下方](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的詳細資料 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告：技能輸入 ' languageCode ' 具有下列語言代碼 ' X，Y，Z '，至少其中一個無效。
不支援傳遞至下游技能選擇性輸入的一或多個值 `languageCode` 。 如果您將 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 的輸出傳遞給後續技能，而輸出所包含的語言比這些下游技能所支援的語言還多，就會發生這種情況。

如果您知道您的資料集全都採用一種語言，您應該移除 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 和 `languageCode` 技能輸入，並改為使用該技能的 `defaultLanguageCode` 技能參數，並假設該技能支援該語言。

如果您知道您的資料集包含多個語言，因此需要 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 和 `languageCode` 輸入，請考慮新增 [ConditionalSkill](cognitive-search-skill-conditional.md) ，以篩選出具有不支援之語言的文字，然後再將文字傳遞至下游技能。  以下是這類 >microsoft.skills.text.entityrecognitionskill 的範例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

以下是針對每個可能產生此錯誤訊息之技能的目前支援語言參考：
* 針對[KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、 [>microsoft.skills.text.entityrecognitionskill](cognitive-search-skill-entity-recognition.md)、 [SentimentSkill](cognitive-search-skill-sentiment.md)和[PIIDetectionSkill](cognitive-search-skill-pii-detection.md) [文字分析支援的語言](../cognitive-services/text-analytics/language-support.md) () 
*  ([文字 TranslationSkill](cognitive-search-skill-text-translation.md)的[翻譯工具支援語言](../cognitive-services/translator/language-support.md)) 
* [文字 SplitSkill](cognitive-search-skill-textsplit.md) 支援的語言： `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>警告：技能輸入已被截斷
認知技能具有可一次分析的文字長度限制。 如果這些技能的文字輸入超過該限制，我們將會截斷文字以符合限制，然後對截斷的文字執行擴充。 這表示技能會執行，但不會在您的所有資料上執行。

在下面的範例 LanguageDetectionSkill 中， `'text'` 如果輸入欄位超過字元限制，則可能會觸發此警告。 您可以在 [技能檔](cognitive-search-predefined-skills.md)中找到技能輸入限制。

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

如果您想要確保所有文字都經過分析，請考慮使用 [分割技能](cognitive-search-skill-textsplit.md)。

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告： Web API 技能回應包含警告
索引子可以在技能集中執行技能，但 Web API 要求的回應指出執行期間發生警告。 請檢查警告以瞭解您的資料受到影響的方式，以及是否需要採取動作。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告：目前的索引子設定不支援增量進度

只有 Cosmos DB 資料來源才會出現此警告。

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷，索引子仍能夠在下次執行時從中斷的部分繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。

繼續進行未完成的索引編制工作的能力，前提于擁有依資料行排序的檔 `_ts` 。 索引子會使用時間戳來決定接下來要挑選的檔。 如果資料 `_ts` 行遺失，或索引子無法判斷自訂查詢是否依該資料行排序，則索引子會在開始時開始，您會看到這個警告。

您可以覆寫此行為，並使用 configuration 屬性來啟用累加進度和隱藏此警告 `assumeOrderByHighWatermarkColumn` 。

如需詳細資訊，請參閱 [增量進度和自訂查詢](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告：某些資料在投射期間遺失。 資料表 ' Y ' 中的資料列 ' X ' 具有太長的字串屬性 ' Z '。

[資料表儲存體服務](https://azure.microsoft.com/services/storage/tables)對於大型[實體屬性](/rest/api/storageservices/understanding-the-table-service-data-model#property-types)的可以有限制。 字串可以有32000個字元或更少。 如果投射了字串屬性超過32000個字元的資料列，則只會保留前32000個字元。 若要解決這個問題，請避免使用長度超過32000個字元的字串屬性來投射資料列。

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告：已將解壓縮的文字截斷為 X 個字元
索引子會限制可從任何一份檔中解壓縮的文字數量。 此限制取決於定價層：適用于免費層的32000個字元、適用于 4000000 Basic 的64000、適用于 standard 的、標準 S2 的8000000，以及標準 S3 的16000000。 截斷的文字將不會進行索引。 若要避免這個警告，請嘗試將含有大量文字的檔細分為多個較小的檔。 

如需詳細資訊，請參閱 [索引子限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告：無法將輸出欄位 ' X ' 對應到搜尋索引
參考不存在/null 資料的輸出欄位對應會產生每份檔的警告，並產生空的索引欄位。 若要解決此問題，請仔細檢查輸出欄位對應來源路徑是否有可能的錯誤，或使用 [條件式技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)來設定預設值。 如需詳細資料，請參閱 [輸出欄位對應](cognitive-search-output-field-mapping.md) 。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 無法反復查看非陣列 | 「無法反復查看非陣列」 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。 | 當輸出不是陣列時，就會發生此錯誤。 如果您認為輸出應該是陣列，請檢查指定的輸出來源欄位路徑是否有錯誤。 例如，您可能會 `*` 在來源功能變數名稱中遺失或額外的資源。 這項技能的輸入也可能為 null，因此會產生空的陣列。 在 [技能輸入](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 中尋找類似的詳細資料不正確區段。    |
| 無法 `0` 在非陣列中選取 | 「無法 `0` 在非陣列中選取」 `/document/pages` 。 | 如果技能輸出未產生陣列，且輸出來源功能變數名稱具有陣列索引或其路徑，就可能發生這種情況 `*` 。 請再次檢查輸出來源功能變數名稱中提供的路徑，以及所指定功能變數名稱的域值。 在 [技能輸入](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 中尋找類似的詳細資料不正確區段。  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告：資料變更偵測原則設定為使用索引鍵資料行 ' X '
[資料變更偵測原則](/rest/api/searchservice/create-data-source#data-change-detection-policies) 對於用來偵測變更的資料行有特定需求。 其中一個需求是每次變更來源專案時，都會更新此資料行。 另一個需求是此資料行的新值大於前一個值。 索引鍵資料行不會滿足這項需求，因為它們不會在每次更新時變更。 若要解決此問題，請為變更偵測原則選取不同的資料行。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告：檔文字看似 UTF-16 編碼，但遺漏位元組順序標記

[索引子剖析模式](/rest/api/searchservice/create-indexer#blob-configuration-parameters)必須知道文字在剖析之前的編碼方式。 編碼文字的兩個最常見方式是 UTF-16 和 UTF-8。 UTF-8 是可變長度的編碼，其中每個字元的長度都介於1個位元組和4個位元組之間。 UTF-16 是固定長度的編碼，其中每個字元的長度為2個位元組。 UTF-16 有兩種不同的變體：「big endian」和「位元組由小到小」。 文字編碼是由「位元組順序標記」、文字之前的一連串位元組所決定。

| 編碼 | 位元組順序標記 |
| --- | --- |
| UTF-16 位元組由大到小 | 0xFE 0xFF |
| UTF-16 位元組由大到小 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果沒有位元組順序標記，則會假設文字會編碼為 UTF-8。

若要解決此警告，請判斷此 blob 的文字編碼方式，並新增適當的位元組順序標記。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告： Cosmos DB 集合 ' X ' 有延遲編制索引原則。 某些資料可能會遺失

具有 [延遲](/azure/cosmos-db/index-policy#indexing-mode) 編制索引原則的集合無法一致地查詢，導致您的索引子遺漏資料。 若要解決此警告，請將您的編制索引原則變更為一致。

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>警告：檔包含很長的單字 (超過64個字元) 。 這些字組可能會導致截斷及/或不可靠的模型預測。

此警告會從文字分析服務傳遞。  在某些情況下，您可以放心地忽略此警告，例如，當您的檔包含很長的 URL (可能不是關鍵字組或駕駛情感等 ) 。  請注意，當單字的長度超過64個字元時，它會被截斷為可能影響模型預測的64個字元。  
