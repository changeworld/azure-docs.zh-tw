---
title: 索引子錯誤和警告
titleSuffix: Azure Cognitive Search
description: 本文提供您在 Azure 認知搜尋中 AI 擴充時可能會遇到的常見錯誤和警告的資訊和解決方案。
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 83c3797cc3d9232f8589527285cc56c5cbff9a8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221307"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>針對 Azure 認知搜尋中的常見索引子錯誤和警告進行疑難排解

本文提供在 Azure 認知搜尋中編制索引和 AI 擴充時可能會遇到的常見錯誤和警告的資訊和解決方案。

當錯誤計數超過[' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)時，就會停止編制索引。 

如果您想要讓索引子忽略這些錯誤（並略過「失敗的檔」），請考慮更新 `maxFailedItems` 和， `maxFailedItemsPerBatch` 如[這裡](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每個失敗的檔及其檔索引鍵（如果有的話）會顯示為索引子執行狀態中的錯誤。 如果您已將索引子設定為容許失敗，您可以利用[index api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)在稍後手動上傳檔。

本文中的錯誤資訊可協助您解決錯誤，讓索引繼續進行。

警告不會停止編制索引，但會指出可能導致未預期結果的狀況。 無論您採取動作與否，都取決於資料和您的案例。

從 API 版本開始 `2019-05-06` ，專案層級索引子錯誤和警告是結構化的，可讓您更清楚地解決原因和後續步驟。 其中包含下列屬性：

| 屬性 | 說明 | 範例 |
| --- | --- | --- |
| 索引鍵 | 受錯誤或警告影響之檔的檔識別碼。 | HTTPs： \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| NAME | 描述發生錯誤或警告之位置的作業名稱。 這是由下列結構所產生： [category]。[子類別]。[resourceType]。ResourceName | DocumentExtraction. azureblob myBlobContainerName 擴充. WebApiSkill. mySkillName. SearchIndex. OutputFieldMapping. myOutputFieldName. SearchIndex. MergeOrUpload。 myIndexName |
| message | 錯誤或警告的高層級描述。 | 因為 Web Api 要求失敗，所以無法執行技能。 |
| 詳細資料 | 可能有助於診斷問題的任何其他詳細資料，例如執行自訂技能失敗時的 WebApi 回應。 | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1來源，Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` .。。堆疊追蹤的其餘部分 .。。 |
| documentationLink | 相關檔的連結，其中包含用來偵測及解決問題的詳細資訊。 此連結通常會指向此頁面上的下列其中一節。 | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>錯誤：無法讀取檔

索引子無法從資料來源讀取檔。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 跨不同檔的欄位類型不一致 | "類型的值與資料行類型不相符。 無法儲存 `'{47.6,-122.1}'` 在作者資料行中。  預期的類型為 JArray。」  「將資料類型 Nvarchar 轉換成 float 時發生錯誤」。  「將 Nvarchar 值 ' 12 個月 ' 轉換成資料類型 int 時，轉換失敗」。  「轉換運算式到資料類型 int 時發生算術溢位錯誤。」 | 請確定每個欄位的類型在不同的檔中都相同。 例如，如果第一個檔 `'startTime'` 欄位是日期時間，而第二個檔是字串，則會遇到此錯誤。 |
| 來自資料來源之基礎服務的錯誤 | （從 Cosmos DB）`{"Errors":["Request rate is large"]}` | 檢查您的儲存體實例，以確保其狀況良好。 您可能需要調整您的縮放/分割。 |
| 暫時性問題 | 從伺服器接收結果時發生傳輸層級錯誤。 （提供者： TCP 提供者，錯誤： 0-遠端主機已強制關閉現有的連接 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>錯誤：無法從您的檔解壓縮內容或中繼資料
具有 Blob 資料來源的索引子無法從檔中解壓縮內容或中繼資料（例如，PDF 檔案）。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| blob 超過大小限制 | 檔是 `'150441598'` 位元組，超過 `'134217728'` 您目前服務層級之檔解壓縮的大小上限（位元組）。 | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 具有不支援的內容類型 | 檔具有不支援的內容類型`'image/png'` | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 已加密 | 無法處理檔-它可能已加密或密碼保護。 | 您可以使用[blob 設定](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)略過 blob。 |
| 暫時性問題 | 「處理 blob 時發生錯誤：要求已中止：要求已取消。」 「檔在處理期間超時。」 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>錯誤：無法剖析檔
索引子從資料來源讀取檔，但將檔內容轉換成指定的欄位對應架構時發生問題。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 缺少檔索引鍵 | 檔索引鍵不能遺失或空白 | 請確定所有檔都有有效的檔索引鍵。 檔索引鍵的判斷方式是將 ' key ' 屬性設定為[索引定義](https://docs.microsoft.com/rest/api/searchservice/create-index#request-body)的一部分。 當特定檔上找不到標示為「金鑰」的屬性時，索引子將會發出此錯誤。 |
| 檔索引鍵無效 | 檔索引鍵的長度不能超過1024個字元 | 修改檔索引鍵以符合驗證需求。 |
| 無法將欄位對應套用至欄位 | 無法將對應函數套用 `'functionName'` 至欄位 `'fieldName'` 。 陣列不可以是 null。 參數名稱：位元組 | 再次檢查索引子上定義的[欄位](search-indexer-field-mappings.md)對應，並與失敗檔之指定欄位的資料進行比較。 可能需要修改欄位對應或檔資料。 |
| 無法讀取域值 | 無法讀取位於索引的資料行值 `'fieldName'` `'fieldIndex'` 。 從伺服器接收結果時發生傳輸層級錯誤。 (提供者: TCP 提供者，錯誤: 0 - 遠端主機已強制關閉一個現存的連線)。 | 這些錯誤通常是因為資料來源的基礎服務發生非預期的連接問題。 請稍後再試著透過索引子執行檔。 |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>錯誤： `xyz` 因為套用對應函數 ' ' 時發生還原序列化問題，所以無法將輸出欄位 ' ' 對應至搜尋索引 `abc`
輸出對應可能失敗，因為輸出資料的格式不正確，因為您使用的是對應函數。 例如，在二進位資料上套用 Base64Encode 對應函數會產生此錯誤。 若要解決此問題，請重新執行索引子，但不指定對應函數，或確定對應函數與輸出欄位資料類型相容。 如需詳細資訊，請參閱[輸出欄位對應](cognitive-search-output-field-mapping.md)。

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>錯誤：無法執行技能
索引子無法在技能集中執行技能。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 暫時性連線問題 | 發生暫時性錯誤。 請稍後再試一次。 | 偶爾會發生非預期的連線問題。 請稍後再試著透過索引子執行檔。 |
| 潛在的產品 bug | 發生意外錯誤。 | 這表示不明的失敗類別，可能表示有產品錯誤。 請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以取得協助。 |
| 技能在執行期間發生錯誤 | （來自合併技能）有一或多個位移值無效，無法剖析。 專案已插入至文字結尾 | 請使用錯誤訊息中的資訊來修正問題。 這種失敗會需要採取動作來解決。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>錯誤：因為 Web API 要求失敗，所以無法執行技能
因為對 Web API 的呼叫失敗，所以技能執行失敗。 當使用自訂技能時，通常會發生這類失敗，在此情況下，您將需要對自訂程式碼進行偵錯工具以解決此問題。 如果不是來自內建技能的失敗，請參閱錯誤訊息，以取得修正問題的協助。

在偵測此問題時，請務必留意此技能的任何[技能輸入警告](#warning-skill-input-was-invalid)。 您的 Web API 端點可能會失敗，因為索引子傳遞了非預期的輸入。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>錯誤：因為 Web API 技能回應無效，所以無法執行技能
技能執行失敗，因為呼叫 Web API 傳回不正確回應。 當使用自訂技能時，通常會發生這類失敗，在此情況下，您將需要對自訂程式碼進行偵錯工具以解決此問題。 如果失敗是來自內建技能，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以取得協助。

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>錯誤：技能未在時間限制內執行
在兩種情況下，您可能會遇到這個錯誤訊息，每個都應該以不同的方式處理。 請依照下列指示，視您為此錯誤傳回的技能而定。

### <a name="built-in-cognitive-service-skills"></a>內建認知服務技能
認知服務 API 端點支援許多內建的認知技能，例如語言偵測、實體辨識或 OCR。 有時候這些端點有暫時性問題，而要求將會超時。針對暫時性問題，除了等待並再試一次以外，沒有任何補救措施。 作為緩和措施，請考慮將您的索引子設定為依[排程執行](search-howto-schedule-indexers.md)。 排定的編制索引會從停止的地方繼續進行。 假設暫時性問題已解決，則索引和認知技能的處理應該能夠在下一次排程執行時繼續進行。

如果您在內建認知技能的同一份檔上繼續看到此錯誤，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以取得協助，因為這不是預期的情況。

### <a name="custom-skills"></a>自訂技能
如果您所建立的自訂技能遇到逾時錯誤，您可以嘗試幾件事。 首先，請檢查您的自訂技能，並確保它不會停滯在無限迴圈中，而且會以一致的方式傳回結果。 一旦您確認是這種情況，請判斷技能的執行時間。 如果您未 `timeout` 在自訂技能定義上明確設定值，則預設值 `timeout` 為30秒。 如果30秒不夠長，無法讓您的技能執行，您可以 `timeout` 在自訂技能定義上指定較高的值。 以下是自訂技能定義的範例，其中的 timeout 設定為90秒：

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

您可以為參數設定的最大值 `timeout` 為230秒。  如果您的自訂技能無法在230秒內一致地執行，您可以考慮減少 `batchSize` 自訂技能的，使其在單一執行中會有較少的檔可處理。  如果您已將設定 `batchSize` 為1，則必須重寫技能，才能在230秒內執行，或將其分割成多個自訂技能，讓任何單一自訂技能的執行時間最多可達230秒。 如需詳細資訊，請參閱[自訂技能檔](cognitive-search-custom-skill-web-api.md)。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>錯誤：無法 ' `MergeOrUpload` ' |`Delete`搜尋索引的 ' ' 檔

已讀取並處理檔，但索引子無法將它新增至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 欄位包含太大的詞彙 | 檔中的詞彙大於[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 您可以確保欄位未設定為可篩選、facetable 或可排序，藉以避免這項限制。
| 檔太大，無法編制索引 | 檔大於[api 要求大小上限](search-limits-quotas-capacity.md#api-request-limits) | [如何為大型資料集編制索引](search-howto-large-index.md)
| 檔在集合中包含太多物件 | 您檔中的集合超過[所有複雜集合限制的元素上限](search-limits-quotas-capacity.md#index-limits)「具有索引鍵的檔 `'1000052'` 具有 `'4303'` 集合中的物件（JSON 陣列）」。 最多可在 `'3000'` 整個檔的集合中使用物件。 請移除集合中的物件，然後嘗試重新編制檔的索引。」 | 我們建議您將檔中複雜集合的大小縮減為低於限制，並避免高儲存體使用率。
| 無法連接到目標索引（在重試後仍會繼續），因為服務正在進行其他負載，例如查詢或索引。 | 無法建立連接以更新索引。 搜尋服務正在負荷過重。 | [相應增加您的搜尋服務](search-capacity-planning.md)
| 搜尋服務正在修補以進行服務更新，或處於拓撲重新設定的過程中。 | 無法建立連接以更新索引。 搜尋服務目前已關閉/搜尋服務正在進行轉換。 | 以至少3個複本設定服務，每個[SLA 檔](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的可用性99.9%
| 基礎計算/網路資源（罕見）中的失敗 | 無法建立連接以更新索引。 發生未知錯誤。 | 設定要依照[排程執行](search-howto-schedule-indexers.md)的索引子，以從失敗狀態中收取。
| 由於網路問題，在超時期間內，對目標索引所做的索引編制要求並未認可。 | 無法及時建立與搜尋索引的連接。 | 設定要依照[排程執行](search-howto-schedule-indexers.md)的索引子，以從失敗狀態中收取。 此外，如果此錯誤狀況持續發生，請嘗試降低索引子的[批次大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>錯誤：無法為檔編制索引，因為部分檔的資料無效

檔是由索引子讀取和處理，但由於索引欄位的設定不相符，以及索引子所解壓縮和處理的資料，因此無法新增至搜尋索引。 發生這種情況的原因可能是：

| 原因 | 詳細資料/範例
| --- | ---
| 索引子所解壓縮之欄位的資料類型與對應 [目標索引] 欄位的資料模型不相容。 | 具有索引鍵 ' 888 ' 之檔中的資料欄位 '_data_' 具有無效值 ' 的類型 ' Edm。字串 ' '。 預期的類型為 ' Collection （Edm. String） '。 |
| 無法從字串值解壓縮任何 JSON 實體。 | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 剖析為 JSON 物件。 錯誤： ' 剖析值後發現未預期的字元： ' '。 路徑 '_path_'，第1行，位置3162。 ' |
| 無法從字串值解壓縮 JSON 實體的集合。  | 無法將欄位 '_data_' 的類型 ' Edm. 字串 ' ' 剖析為 JSON 陣列。 錯誤： ' 剖析值後發現未預期的字元： ' '。 路徑 ' [0] '，第1行，位置 27. ' |
| 在來源文件中發現未知的類型。 | 無法為未知的類型 '_unknown_' 編制索引 |
| 來源文件中使用了地理位置點的不相容標記法。 | 不支援 WKT 點字串常值。 請改用 GeoJson point 常值 |

在所有這些情況下，請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[索引子的資料類型對應](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，以確保您正確地建立索引架構，並設定適當的[索引子欄位](search-indexer-field-mappings.md)對應。 錯誤訊息將包含有助於追蹤不相符之來源的詳細資料。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>錯誤：因為資料表有複合主鍵，所以無法使用整合式變更追蹤原則

這適用于 SQL 資料表，通常會在索引鍵定義為複合索引鍵時，或在資料表已定義唯一的叢集索引（如同在 SQL 索引中，而不是 Azure 搜尋服務索引）時發生。 主要的原因是在[唯一叢集索引](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)的情況下，索引鍵屬性已修改為複合主鍵。 在此情況下，請確定您的 SQL 資料表沒有唯一的叢集索引，或者您將索引鍵欄位對應到保證不會有重複值的欄位。

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>錯誤：無法處理索引子執行時間上限中的檔

當索引子無法在允許的執行時間內從資料來源完成處理單一檔時，就會發生這個錯誤。 使用技能集時，[執行時間上限](search-limits-quotas-capacity.md#indexer-limits)較短。 當此錯誤發生時，如果您將 maxFailedItems 設定為0以外的值，則索引子會在未來執行時略過檔，讓索引可以進行。 如果您無法承受略過任何檔，或如果您一直看到此錯誤，請考慮將檔分解成較小的檔，以便在單一索引子執行中進行部分進度。

<name = "不是專案檔案></a>

## <a name="error-could-not-project-document"></a>錯誤：無法投影檔

當索引子嘗試將[資料投影到知識存放區](knowledge-store-projection-overview.md)，而且嘗試執行這項作業失敗時，就會發生此錯誤。  這項失敗可能是一致且可修復的，或可能是暫時性的失敗，而且需要等候並重試才能解決的預測輸出接收。  以下是一組已知的失敗狀態和可能的解決方法。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 無法更新 `'blobUri'` 容器中的投影 blob`'containerName'` |指定的容器不存在。 | 索引子會檢查先前是否已建立指定的容器，並在必要時建立它，但它只會在每個索引子執行時執行此檢查一次。 此錯誤表示某個專案在此步驟之後刪除了容器。  若要解決此錯誤，請嘗試：單獨保留您的儲存體帳戶資訊，等待索引子完成，然後重新執行索引子。 |
| 無法更新 `'blobUri'` 容器中的投影 blob`'containerName'` |無法將資料寫入傳輸連線：遠端主機已強制關閉現有的連接。 | 這應該是 Azure 儲存體的暫時性失敗，因此應該藉由重新執行索引子來解決。 如果您一直遇到此錯誤，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)，以便進一步調查。  |
| 無法更新 `'projectionRow'` 資料表中的資料列`'tableName'` | 伺服器忙碌中。 | 這應該是 Azure 儲存體的暫時性失敗，因此應該藉由重新執行索引子來解決。 如果您一直遇到此錯誤，請提出[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)，以便進一步調查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>警告：技能輸入無效
技能的輸入遺漏、錯誤的類型或其他無效。 警告訊息會指出影響：
1) 無法執行技能
2) 已執行技能，但可能有非預期的結果

認知技能具有必要的輸入和選擇性輸入。 例如，關鍵字組的[提取技能](cognitive-search-skill-keyphrases.md)有兩個必要的輸入 `text` ， `languageCode` 且沒有選擇性的輸入。 自訂技能輸入全都視為選擇性輸入。

如果遺漏任何必要的輸入，或是任何輸入不是正確的型別，就會略過此技能並產生警告。 略過的技能不會產生任何輸出，因此，如果其他技能使用略過的技能輸出，他們可能會產生額外的警告。

如果遺漏了選擇性輸入，技能仍然會執行，但可能會因為遺漏輸入而產生非預期的輸出。

在這兩種情況下，可能會因為您的資料圖形而預期此警告。 例如，如果您有一份檔，其中包含具有欄位、和的人員相關資訊， `firstName` `middleName` `lastName` 您可能會有一些檔沒有的專案 `middleName` 。 如果您要將 `middleName` 做為輸入傳遞給管線中的技能，則預期此技能輸入可能會在某些時間內遺失。 您將需要評估您的資料和案例，以判斷是否因此警告而需要任何動作。

如果您想要在遺漏輸入的情況下提供預設值，您可以使用[條件式技能](cognitive-search-skill-conditional.md)來產生預設值，然後使用[條件式技能](cognitive-search-skill-conditional.md)的輸出做為技能輸入。


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
| 技能輸入的類型錯誤 | 「所需的技能輸入不是預期的類型 `String` 。 名稱： `text` ，Source： `/document/merged_content` . "  「所需的技能輸入不是預期的格式。 名稱： `text` ，Source： `/document/merged_content` . "  「無法逐一查看非陣列 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。」  「無法 `0` 在非陣列中選取」 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` | 某些技能預期特定類型的輸入，例如[情感技能](cognitive-search-skill-sentiment.md)預期 `text` 會是字串。 如果輸入指定非字串值，則技能不會執行，而且不會產生任何輸出。 請確定您的資料集具有類型的輸入值一致，或使用[自訂 WEB API 技能](cognitive-search-custom-skill-web-api.md)來前置處理輸入。 如果您要逐一查看陣列的技能，請檢查技能內容和輸入是否 `*` 在正確的位置。 通常內容和輸入來源都應該以 `*` 為數組結尾。 |
| 缺少技能輸入 | 「缺少必要的技能輸入。 名稱： `text` ，來源： `/document/merged_content` "" 遺漏值 `/document/normalized_images/0/imageTags` 。 "  「無法 `0` 在長度陣列中選取」 `/document/pages` `0` 。 | 如果您的所有檔都收到此警告，表示輸入路徑中可能出現錯誤，而且您應該再次檢查路徑中的屬性名稱大小寫、額外或遺失 `*` ，並確定資料來源中的檔提供必要的輸入。 |
| 技能語言代碼輸入無效 | 技能輸入 `languageCode` 具有下列語言代碼 `X,Y,Z` ，其中至少有一個無效。 | 請參閱[下方](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的詳細資料 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告：技能輸入 ' languageCode ' 具有下列語言代碼 ' X，Y，Z '，其中至少有一個無效。
不支援傳遞至下游技能選擇性輸入的一個或多個值 `languageCode` 。 如果您將[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的輸出傳遞給後續技能，而輸出所包含的語言比這些下游技能所支援的更多，就會發生這種情況。

如果您知道您的資料集全都使用一種語言，則應該移除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 技能輸入，並 `defaultLanguageCode` 改為使用該技能的技能參數，前提是該技能支援該語言。

如果您知道您的資料集包含多個語言，因而需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 輸入，請考慮新增[ConditionalSkill](cognitive-search-skill-conditional.md) ，以使用不受支援的語言來篩選文字，然後再將文字傳遞給下游技能。  以下是此 EntityRecognitionSkill 可能顯示的範例：

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

以下是每個可能產生此錯誤訊息之技能的目前支援語言參考：
* [文字分析支援的語言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（適用于[KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、 [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)、 [SentimentSkill](cognitive-search-skill-sentiment.md)和[PIIDetectionSkill](cognitive-search-skill-pii-detection.md)）
* [翻譯工具支援的語言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（適用于[文字 TranslationSkill](cognitive-search-skill-text-translation.md)）
* [文字 SplitSkill](cognitive-search-skill-textsplit.md)支援的語言：`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>警告：已截斷技能輸入
認知技能對於可以一次分析的文字長度有限制。 如果這些技能的文字輸入超過該限制，我們會截斷文字以符合限制，然後在該截斷的文字上執行擴充。 這表示技能會執行，但不是您所有的資料。

在下面的範例 LanguageDetectionSkill 中， `'text'` 如果輸入欄位超過字元限制，則可能會觸發此警告。 您可以在[技能檔](cognitive-search-predefined-skills.md)中找到技能的輸入限制。

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

如果您想要確保所有文字都經過分析，請考慮使用[分割技能](cognitive-search-skill-textsplit.md)。

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告： Web API 技能回應包含警告
索引子可以在技能集中執行技能，但是來自 Web API 要求的回應表示在執行期間出現警告。 請參閱警告以瞭解資料受到影響的方式，以及是否需要採取動作。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告：目前的索引子設定不支援增量進度

只有 Cosmos DB 資料來源才會發生此警告。

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷，索引子仍能夠在下次執行時從中斷的部分繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。

繼續未完成之索引工作的功能，是前提在具有資料行排序的檔上 `_ts` 。 索引子會使用時間戳來決定下一個要挑選的檔。 如果資料 `_ts` 行遺失，或索引子無法判斷自訂查詢是否依其排序，則索引子會在開始時啟動，而您會看到這個警告。

您可以覆寫此行為，啟用累加進度，並使用 configuration 屬性來隱藏這個警告 `assumeOrderByHighWatermarkColumn` 。

如需詳細資訊，請參閱[增量進度和自訂查詢](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告：某些資料在投射期間遺失。 資料表 ' Y ' 中的資料列 ' X ' 有太長的字串屬性 ' Z '。

[表格儲存體服務](https://azure.microsoft.com/services/storage/tables)對於大型[實體屬性](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types)可以有多大的限制。 字串可以有32000個字元或更少。 如果投射字串屬性長度超過32000個字元的資料列，則只會保留前32000個字元。 若要解決此問題，請避免投射字串屬性長度超過32000個字元的資料列。

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告：已將解壓縮的文字截斷成 X 個字元
索引子會限制可以從任何一份檔中解壓縮多少文字。 此限制取決於定價層：免費層的32000個字元、[基本] 的 [64000]、[標準] 的 [4000000]、[標準 S2 的 8000000] 和 [標準 S3 的 16000000]。 已截斷的文字將不會編制索引。 若要避免這個警告，請嘗試將包含大量文字的檔分解成多個小型的檔。 

如需詳細資訊，請參閱[索引子限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告：無法將輸出欄位 ' X ' 對應至搜尋索引
參考不存在/null 資料的輸出欄位對應會產生每份檔的警告，並產生空的索引欄位。 若要解決此問題，請仔細檢查輸出欄位對應來源路徑是否可能出現錯誤，或使用[條件式技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)來設定預設值。 如需詳細資訊，請參閱[輸出欄位對應](cognitive-search-output-field-mapping.md)。

| 原因 | 詳細資料/範例 | 解決方案 |
| --- | --- | --- |
| 無法重複執行非陣列 | 「無法逐一查看非陣列 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。」 | 當輸出不是陣列時，就會發生這個錯誤。 如果您認為輸出應該是陣列，請檢查指定的輸出來源欄位路徑是否有錯誤。 例如，您 `*` 的來源功能變數名稱中可能遺漏或多餘的。 這項技能的輸入也可能是 null，因此會產生空的陣列。 在 [[技能輸入無效](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)] 區段中尋找類似的詳細資料。    |
| 無法 `0` 在非陣列中選取 | 「無法 `0` 在非陣列中選取」 `/document/pages` 。 | 如果技能輸出不會產生陣列，而且輸出來源功能變數名稱具有陣列索引或其路徑，就會發生這種情況 `*` 。 請再次檢查輸出來源功能變數名稱中所提供的路徑，以及指定功能變數名稱的域值。 在 [[技能輸入無效](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)] 區段中尋找類似的詳細資料。  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告：資料變更偵測原則已設定為使用索引鍵資料行 ' X '
[資料變更偵測原則](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies)具有其用來偵測變更之資料行的特定需求。 其中一個需求是每次變更來源專案時，就會更新此資料行。 另一個需求是此資料行的新值大於先前的值。 索引鍵資料行無法滿足這項需求，因為每個更新都不會變更。 若要解決此問題，請為變更偵測原則選取不同的資料行。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告：檔文字看起來是 UTF-16 編碼，但遺漏了位元組順序標記

在剖析之前，[索引子剖析模式](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters)必須知道文字的編碼方式。 編碼文字的兩個最常見方式是 UTF-16 和 UTF-8。 UTF-8 是可變長度的編碼，其中的每個字元長度介於1個位元組和4個位元組之間。 UTF-16 是固定長度的編碼，其中每個字元的長度為2個位元組。 UTF-16 有兩個不同的變體：「大 endian」和「小 endian」。 文字編碼取決於「位順序標記」，這是文字之前的一系列位元組。

| 編碼 | 位元組順序標記 |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 小 Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果沒有位元組順序標記，則會假設文字會編碼為 UTF-8。

若要解決此警告，請判斷此 blob 的文字編碼方式，並新增適當的位元組順序標記。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告： Cosmos DB 集合 ' X ' 具有延遲索引編制原則。 某些資料可能會遺失

具有[延遲](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)索引編制原則的集合無法以一致的方式查詢，因而導致您的索引子遺失資料。 若要解決此警告，請將您的編制索引原則變更為一致。
