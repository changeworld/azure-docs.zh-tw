---
title: 索引子錯誤和警告
titleSuffix: Azure Cognitive Search
description: 本文提供有關在 Azure 認知搜索中的 AI 豐富過程中可能會遇到的常見錯誤和警告的資訊和解決方案。
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed10e998ea05b6687190b1f87095f8bc28265905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086603"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>在 Azure 認知搜尋中排除常見索引器錯誤和警告的故障

本文提供有關在 Azure 認知搜索中索引和 AI 充實期間可能會遇到的常見錯誤和警告的資訊和解決方案。

當錯誤計數超過[「最大失敗專案」](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)時,索引將停止。 

如果希望索引器忽略這些錯誤(並跳過"失敗的文檔"),請考慮`maxFailedItems`更新`maxFailedItemsPerBatch`和 ,如[此處](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每個失敗的文件及其文件密鑰(如果可用)將顯示為索引器執行狀態中的錯誤。 如果將[索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)器設置為允許故障,則可以在以後的點手動上載文檔。

本文中的錯誤資訊可以説明您解決錯誤,從而允許繼續索引。

警告不會停止索引,但它們確實指示可能導致意外結果的情況。 是否執行操作取決於數據和方案。

從 API`2019-05-06`版本開始,專案級索引器錯誤和警告的結構是為了提高有關原因和後續步驟的清晰度。 它們包含以下屬性:

| 屬性 | 描述 | 範例 |
| --- | --- | --- |
| 索引鍵 | 受錯誤或警告影響的文件的文件 ID。 | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| NAME | 描述錯誤或警告發生位置的操作名稱。 這由以下結構生成:[類別]。[子類別]。[資源類型]。【 資源名稱 】 | 文件提取.azureblob.myBlob 容器名稱豐富.WebApiskill.myskillname 投影.搜索索引.輸出欄位映射.myoutputfieldname 投影.搜索索引.mergeorupload.myindexname 投影.知識商店.表.mytablename |
| message | 錯誤或警告的高級描述。 | 無法執行技能,因為 Web Api 請求失敗。 |
| 詳細資料 | 可能有助於診斷問題的任何其他詳細資訊,如執行自定義技能時 WebApi 回應失敗。 | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 源`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`, 豐克 ...堆疊追蹤的其餘部分... |
| 文件連結 | 指向相關文件的連結,包含用於調試和解決問題的詳細資訊。 此連結通常指向此頁面上的以下部分之一。 | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>錯誤:無法讀取文件

索引器無法從數據源讀取文檔。 這可能是由於:

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 不同文件中的欄位類型不一致 | 值類型與列類型不匹配。 無法存儲在`'{47.6,-122.1}'`作者列中。  預期類型為 JArray。  將數據類型 nvarchar 轉換為浮動數據時出錯。  將 nvarchar 值「12 個月」轉換為數據類型 int 時,轉換失敗。  「轉換運算式到資料類型 int 時發生算術溢位錯誤。」 | 確保每個欄位的類型在不同的文件中相同。 例如,如果第一個文`'startTime'`檔 欄位是 DateTime,而在第二個文檔中,它是一個字串,則將命中此錯誤。 |
| 資料來源的基礎服務中的錯誤 | (來自宇宙 DB)`{"Errors":["Request rate is large"]}` | 檢查存儲實例以確保其正常運行。 您可能需要調整縮放/分區。 |
| 瞬態問題 | 從伺服器接收結果時發生傳輸層級錯誤。 (提供者:TCP 提供者,錯誤:0 - 遠端主機強制關閉了現有連線 | 有時存在意外的連接問題。 稍後嘗試通過索引器運行文檔。 |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>錯誤:無法從文件中提取內容或中繼資料
具有 Blob 資料來源的索引器無法從文件中提取內容或中繼資料(例如,PDF 檔案)。 這可能是由於:

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| Blob 超過大小限制 | 文件是`'150441598'`位元組,超過當前服務層的`'134217728'`文檔提取的最大大小位元組。 | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 具有不支援的內容類型 | 文件具有不支援的內容型態`'image/png'` | [blob 索引錯誤](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob 已加密 | 無法處理文件 - 文檔可能已加密或受密碼保護。 | 您可以使用 blob 設定跳過[Blob。](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 瞬態問題 | 錯誤處理 blob:請求已中止:請求已取消。 "處理過程中超時的文檔。 | 有時存在意外的連接問題。 稍後嘗試通過索引器運行文檔。 |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>錯誤:無法剖析文件
索引器從資料源讀取文檔,但存在將文檔內容轉換為指定欄位映射架構的問題。 這可能是由於:

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 遺失文件金鑰 | 文件金鑰不能遺失或為空 | 確保所有文件都具有合法的文件金鑰 |
| 文件金鑰無效 | 文件金鑰不能超過 1024 個字元 | 修改文檔金鑰以滿足驗證要求。 |
| 無法將欄位映射套用於欄位 | 無法將映射函數`'functionName'`套用於`'fieldName'`欄位 。 陣列不能為空。 參數名稱:位元組 | 仔細檢查索引器上定義的[欄位映射](search-indexer-field-mappings.md),並與失敗文檔的指定欄位的數據進行比較。 可能需要修改欄位映射或文件數據。 |
| 無法讀取欄位值 | 無法讀取索引`'fieldName'``'fieldIndex'`處列的值。 從伺服器接收結果時發生傳輸層級錯誤。 (提供者: TCP 提供者，錯誤: 0 - 遠端主機已強制關閉一個現存的連線)。 | 這些錯誤通常是由於數據源的基礎服務出現意外的連接問題。 稍後嘗試通過索引器運行文檔。 |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>錯誤:無法執行技能
索引器無法在技能集中運行技能。

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 瞬態連接問題 | 發生暫時性錯誤。 請稍後再試一次。 | 有時存在意外的連接問題。 稍後嘗試通過索引器運行文檔。 |
| 潛在的產品錯誤 | 發生意外錯誤。 | 這表示故障類別未知,可能意味著存在產品 Bug。 請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以獲得説明。 |
| 技能執行過程中遇到錯誤 | ( 從合併技能 )一個或多個偏移值無效,無法解析。 專案插入到文字的末尾 | 使用錯誤消息中的資訊來解決此問題。 此類故障需要採取措施來解決。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>錯誤:由於 Web API 請求失敗,無法執行技能
技能執行失敗,因為對 Web API 的調用失敗。 通常,當使用自定義技能時,會發生此類故障,在這種情況下,您需要調試自定義代碼以解決此問題。 如果故障來自內置技能,請參閱錯誤消息以尋求説明以修復問題。

除錯此問題時,請務必注意此[技能的任何技能輸入警告](#warning-skill-input-was-invalid)。 Web API 終結點可能失敗,因為索引器傳遞它意外的輸入。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>錯誤:無法執行技能,因為 Web API 技能回應無效
技能執行失敗,因為對 Web API 的調用返回了無效的回應。 通常,當使用自定義技能時,會發生此類故障,在這種情況下,您需要調試自定義代碼以解決此問題。 相反,如果失敗來自內置技能,請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以獲得説明。

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>錯誤:技能未在時間限制內執行
有兩種情況,您可能會遇到此錯誤消息,每個錯誤消息都應以不同的方式對待。 請按照以下說明操作,具體取決於您返回此錯誤的技能。

### <a name="built-in-cognitive-service-skills"></a>內建認知服務技能
許多內建認知技能(如語言檢測、實體識別或 OCR)由認知服務 API 終結點支援。 有時這些終結點存在暫時性問題,請求超時。對於暫時性問題,除了等待和重試外,沒有其他補救措施。 作為緩解措施,請考慮將索引器設定為[按計劃執行](search-howto-schedule-indexers.md)。 計劃索引在關閉的位置回升。 假設暫時性問題已解決,索引和認知技能處理應該能夠在下一次計劃運行中繼續。

如果您在同一文檔中繼續看到此錯誤,以獲得內置認知技能,請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support)以獲得幫助,因為這是預料之中的。

### <a name="custom-skills"></a>自訂技能
如果您使用已創建的自定義技能遇到超時錯誤,您可以嘗試以下幾項操作。 首先,查看自定義技能,並確保不會卡在無限迴圈中,並且它一致返回結果。 確認情況后,確定技能的執行時間。 如果未在自定義技能定義上顯式設置`timeout`值,則預設值`timeout`為30秒。 如果 30 秒不夠長,無法執行技能,則可以在自定義技能`timeout`定義上 指定較高的值。 下面是自定義技能定義的範例,其中超時設置為90秒:

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

可以為`timeout`參數設置最大值為 230 秒。  如果您的自定義技能無法在 230 秒內持續執行,則可以考慮減少自`batchSize`定義技能 ,以便在單個執行中處理的文檔更少。  如果已設置為`batchSize`1,則需要重寫技能,以便能夠在 230 秒內執行,或者將其拆分為多個自定義技能,以便任何單個自定義技能的執行時間最多為 230 秒。 有關詳細資訊,請檢視[自訂技能文件](cognitive-search-custom-skill-web-api.md)。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>錯誤:`MergeOrUpload`無法 ' |'`Delete`文件到搜尋索引

文件已讀取和處理,但索引器無法將其添加到搜索索引中。 這可能是由於:

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 欄位包含太大的術語 | 文件中的字語大於[32 KB 限制](search-limits-quotas-capacity.md#api-request-limits) | 可以通過確保欄位未配置為可篩選、可面可或可排序來避免此限制。
| 文件太大,無法編制索引 | 文件大於最大 api[請求大小](search-limits-quotas-capacity.md#api-request-limits) | [如何索引大型資料集](search-howto-large-index.md)
| 文件包含的集合中的物件太多 | 文檔中的集合超過了[所有複雜集合的最大元素限制](search-limits-quotas-capacity.md#index-limits)`'1000052'`"具有鍵 的`'4303'`文檔包含集合中的物件 (JSON 陣列)。 最多`'3000'`允許物件在整個文檔中集合中。 請從集合中刪除物件,然後嘗試再次對文檔進行索引。 | 我們建議將文檔中複雜集合的大小減小到低於限制,並避免高存儲利用率。
| 連接到目標索引(重試后仍然存在)時遇到問題,因為服務處於其他負載下,如查詢或索引。 | 無法建立更新索引的連接。 搜索服務負載沉重。 | [延伸搜尋服務](search-capacity-planning.md)
| 正在修補搜索服務以進行服務更新,或者正在進行拓撲重新配置。 | 無法建立更新索引的連接。 搜索服務當前正在關閉/搜索服務正在進行過渡。 | 設定至少 3 個複本的服務,使[SLA 文件](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的可用性達到 99.9%
| 基礎計算/網路資源失敗(罕見) | 無法建立更新索引的連接。 發生未知錯誤。 | 將索引器配置為[按計劃運行](search-howto-schedule-indexers.md),以便從失敗狀態中選取。
| 由於網路問題,在超時期間未確認對目標索引的索引請求。 | 無法及時建立與搜索索引的連接。 | 將索引器配置為[按計劃運行](search-howto-schedule-indexers.md),以便從失敗狀態中選取。 此外,如果此錯誤條件仍然存在,請嘗試降低索引器[批次型大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>錯誤:無法索引文件,因為文件的某些資料無效

索引器讀取和處理文檔,但由於索引欄位的配置不匹配以及索引器提取和處理的數據不匹配,無法將其添加到搜索索引中。 這可能是由於:

| 原因 | 詳細資訊/範例
| --- | ---
| 索引器提取的欄位的數據類型與相應目標索引欄位的數據模型不相容。 | 數據欄位「_資料_」 的鍵「888」 具有無效值。 預期類型為"集合(Edm.String)」。 |
| 無法從字串值中提取任何 JSON 實體。 | 無法將欄位「_資料_」 的值「Edm.String」解析為 JSON 物件。 錯誤:'在分析值後遇到意外字元:"'"。 路徑 '_路徑_', 線 1, 位置 3162. |
| 無法從字串值中提取 JSON 實體的集合。  | 無法將欄位「_資料_」 的值「Edm.String」解析為 JSON 陣列。 錯誤:'在分析值後遇到意外字元:"'"。 路徑"{0}",第 1 行,位置 27。 |
| 在源文件中發現了未知類型。 | 未知型態「_未知_」 無法編制索引 |
| 源文檔中使用了地理點的不相容表示法。 | 不支援 WKT POINT 字串文字。 請使用 GeoJson 點文字代替 |

在所有這些情況下,請參閱索引器[的支援資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[資料類型映射](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search),以確保正確建構索引架構並設定了適當的[索引器欄位映射](search-indexer-field-mappings.md)。 錯誤消息將包括可幫助跟蹤不匹配源的詳細資訊。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>錯誤:無法使用整合的變更追蹤策略,因為表具有複合主鍵

這適用於 SQL 表,通常當鍵定義為複合鍵時,或者當表定義了唯一的群集索引(如 SQL 索引中,而不是 Azure 搜索索引)時發生。 主要原因是在[唯一的聚類索引](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)的情況下,鍵屬性被修改為複合主鍵。 在這種情況下,請確保 SQL 表沒有唯一的群集索引,或者將鍵欄位映射到保證不具有重複值的欄位。

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>錯誤:無法在索引器最大執行時處理文件

當索引器無法在允許的執行時間內完成從數據源處理單個文檔時,將發生此錯誤。 使用技能集時[,最長運行時間](search-limits-quotas-capacity.md#indexer-limits)較短。 發生此錯誤時,如果將 maxFailedItem 設置為值小於 0,則索引器將繞過文檔,以便將來運行,以便索引可以進行。 如果無法跳過任何文檔,或者一致看到此錯誤,請考慮將文檔分解為較小的文檔,以便在單個索引器執行中進行部分進度。

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>錯誤:無法投影文件

當索引器嘗試將數據[投影到知識存儲中](knowledge-store-projection-overview.md)時,將發生此錯誤,並且我們嘗試這樣做時失敗。  此故障可能是一致且可修復的,或者可能是投影輸出接收器的暫時性故障,您可能需要等待和重試才能解決。  下面是一組已知的故障狀態和可能的解決方案。

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 無法更新容器中的投影`'blobUri'`Blob`'containerName'` |指定的容器不存在。 | 索引器將檢查指定容器是否以前已創建,並在必要時創建該容器,但它僅執行此檢查一次,每個索引器運行。 此錯誤表示在此步驟後刪除了某個容器。  要解決此錯誤,請嘗試以下錯誤:僅保留存儲帳戶資訊,等待索引器完成,然後重新運行索引器。 |
| 無法更新容器中的投影`'blobUri'`Blob`'containerName'` |無法將資料寫入傳輸連接:遠端主機強制關閉了現有連接。 | 這預計將是 Azure 儲存的暫時性故障,因此應通過重新運行索引器來解決。 如果您一貫遇到此錯誤,請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support),以便進一步調查。  |
| 無法更新表中的`'projectionRow'`列`'tableName'` | 伺服器忙碌中。 | 這預計將是 Azure 儲存的暫時性故障,因此應通過重新運行索引器來解決。 如果您一貫遇到此錯誤,請提交[支援票證](https://ms.portal.azure.com/#create/Microsoft.Support),以便進一步調查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>警告: 技能輸入無效
缺少對技能的輸入、錯誤的類型或其他無效。 警告訊息將指示影響:
1) 無法執行技能
2) 技能已執行,但可能有意外的結果

認知技能需要輸入和可選輸入。 例如,[鍵短語提取技能](cognitive-search-skill-keyphrases.md)有兩個`text`必需`languageCode`的輸入 ,但沒有可選的輸入。 自定義技能輸入都被視為可選輸入。

如果缺少任何必需的輸入,或者任何輸入不是正確的類型,則技能將跳過並生成警告。 跳過的技能不會生成任何輸出,因此,如果其他技能使用跳過的技能的輸出,它們可能會生成其他警告。

如果缺少可選輸入,則技能仍將運行,但由於缺少輸入,可能會生成意外輸出。

在這兩種情況下,由於數據的形狀,可能需要使用此警告。 例如,如果您有一個文檔,其中包含有關`firstName`具有欄位人的`middleName`資訊`lastName`,和,可能有一些文檔`middleName`沒有的條目。 如果要將輸入作為`middleName`輸入傳遞到管道中的技能,則預計此技能輸入可能會錯過一些時間。 您需要評估您的數據和方案,以確定是否需要執行此警告。

如果要在缺少輸入的情況下提供預設值,可以使用[條件技能](cognitive-search-skill-conditional.md)生成預設值,然後將[條件技能](cognitive-search-skill-conditional.md)的輸出用作技能輸入。


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

| 原因 | 詳細資訊/範例 | 解決方案 |
| --- | --- | --- |
| 技能輸入類型錯誤 | 「所需的技能輸入不是預期類型`String`。 名稱: `text``/document/merged_content`, 來源: .  所需的技能輸入不是預期格式。 名稱: `text``/document/merged_content`, 來源: .  「不能反覆運算非陣列`/document/normalized_images/0/imageCelebrities/0/detail/celebrities`。  "無法`0`在非陣`/document/normalized_images/0/imageCelebrities/0/detail/celebrities`列 中選擇" | 某些技能需要特定類型的輸入,例如[情緒技能](cognitive-search-skill-sentiment.md)預期`text`是字串。 如果輸入指定非字串值,則技能不會執行,也不會生成輸出。 確保資料集的輸入值在類型上是一致的,或者使用[自訂 Web API 技能](cognitive-search-custom-skill-web-api.md)預處理輸入。 如果要在陣列上反覆運算技能,請檢查技能上下文和輸入`*`位於正確位置。 通常,對於陣列,上下文和輸入源都應`*`以陣組結尾。 |
| 缺少技能輸入 | 缺少所需的技能輸入。 名稱: `text``/document/merged_content`, 來源`/document/normalized_images/0/imageTags`:" 缺少 值 .  無法以長度`0``/document/pages``0`數組進行選擇。 | 如果所有文檔都收到此警告,則最有可能輸入路徑中存在拼寫錯誤,您應該仔細檢查屬性名稱大小寫,在路徑中額外或`*`缺失 ,並確保數據源中的文檔提供所需的輸入。 |
| 技能語言代碼輸入無效 | 技能輸入`languageCode`具有以下語言`X,Y,Z`代碼 ,其中至少有一個無效。 | 檢視[以下](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)更多詳細資訊 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告: 技能輸入"語言代碼"具有以下語言代碼"X、Y、Z",其中至少有一個語言無效。
不支援傳遞到下游技能的可選`languageCode`輸入中的一個或多個值。 如果將[語言檢測技能](cognitive-search-skill-language-detection.md)的輸出傳遞給後續技能,並且輸出包含的語言比下游技能中支持的語言多,則可能會發生這種情況。

如果您知道數據集全部採用一種語言,則應刪除[語言檢測技能](cognitive-search-skill-language-detection.md)以及`languageCode`技能輸入,並`defaultLanguageCode`改為使用 技能參數,假設該技能支援該語言。

如果您知道數據集包含多種語言,因此需要[語言檢測技能](cognitive-search-skill-language-detection.md)`languageCode`並輸入,請考慮添加[條件技能](cognitive-search-skill-conditional.md),在將文本傳遞到下游技能之前,使用不支援的語言篩選出文本。  下面是實體識別技能的外觀範例:

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

以下是當前支援的語言的一些參考,這些技能可能會生成此錯誤訊息:
* [文字分析支援的語言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)(用於[關鍵短語提取技能](cognitive-search-skill-keyphrases.md)、[實體識別技能](cognitive-search-skill-entity-recognition.md)、[情感技能](cognitive-search-skill-sentiment.md)與[PII 檢測技能](cognitive-search-skill-pii-detection.md))
* [翻譯支援的語言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)(用於[文字翻譯技能](cognitive-search-skill-text-translation.md))
* [文字拆分技能](cognitive-search-skill-textsplit.md)支援的語言:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>警告: 技能輸入被截斷
認知技能對可同時分析的文本長度有限制。 如果這些技能的文本輸入超過該限制,我們將截斷文本以達到限制,然後對截斷的文本執行擴充。 這意味著技能被執行,但不是在所有數據上執行。

在下面的示例語言檢測技能中,如果`'text'`輸入欄位超過字元限制,則可能會觸發此警告。 您可以在[技能文件中](cognitive-search-predefined-skills.md)找到技能輸入限制。

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

如果要確保所有文字都得到分析,請考慮使用[分割技能](cognitive-search-skill-textsplit.md)。

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告: Web API 技能回應包含警告
索引器能夠在技能集中運行技能,但來自 Web API 請求的回應表明在執行過程中存在警告。 查看警告以了解數據受到的影響以及是否需要執行操作。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告: 目前索引器設定不支援增量進度

此警告僅適用於Cosmos DB資料來源。

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷，索引子仍能夠在下次執行時從中斷的部分繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。

恢復未完成的索引作業的能力取決於`_ts`由列訂購文檔。 索引器使用時間戳來確定接下來要選取的文檔。 如果缺少`_ts`列,或者索引器無法確定是否按該列排序了自定義查詢,則索引器從開頭開始,您將看到此警告。

可以重寫此行為,啟用增量進度,並使用`assumeOrderByHighWatermarkColumn`配置屬性禁止此警告。

有關詳細資訊,請參閱[增量進度和自訂查詢](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告: 某些數據在投影過程中丟失。 表"Y"中的"X"行具有字串屬性"Z",該字串屬性太長。

[表存儲服務](https://azure.microsoft.com/services/storage/tables)對[實體屬性](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types)可以有多大有限制。 字串可以有 32,000 個字元或更少。 如果投影字串屬性超過 32,000 個字元的行,則僅保留前 32,000 個字元。 要解決此問題,請避免投影字串屬性超過 32,000 個字元的行。

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告: 截斷的抽取文字到 X 字元
索引器限制可以從任何一個文件中提取的文本量。 此限制取決於定價層:免費套餐為 32,000 個字元,基本套餐為 64,000 個字元,標準版為 400 萬字元,標準 S2 為 800 萬字元,標準 S3 為 1600 萬字元。 截斷的文本將不會編制索引。 為了避免此警告,請嘗試將大量文本的文檔分解為多個較小的文檔。 

有關詳細資訊,請參閱[索引器限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告: 無法將輸出欄位 X 映射到搜尋索引
引用不存在/空資料的輸出欄位映射將為每個文檔生成警告,並導致空索引欄位。 要解決此問題,請仔細檢查輸出欄位映射源路徑中可能存在的拼寫錯誤,或使用[條件技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)設置預設值。

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告: 資料更改檢測策略配置為使用鍵列"X"
[數據更改檢測策略](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies)對於用於檢測更改的列有特定要求。 這些要求之一是每次更改源項時更新此列。 另一個要求是此列的新值大於上一個值。 鍵列不符合此要求,因為它們不會在每個更新時更改。 要解決此問題,請為更改檢測策略選擇其他列。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告: 文件文字似乎已編碼為 UTF-16,但缺少位元順序標記

[索引器解析模式](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters)在分析文本之前需要知道文本的編碼方式。 對文本進行編碼的兩種最常見的方法是 UTF-16 和 UTF-8。 UTF-8 是一種可變長度編碼,其中每個字元的長度在 1 位元組和 4 位元組之間。 UTF-16 是一種固定長度的編碼,其中每個字元長 2 個字節。 UTF-16有兩種不同的變體,"大末人"和"小末人"。 文字編碼由"位元組順序標記"確定,該標記是文本前的一系列位元組。

| 編碼 | 位元組順序標記 |
| --- | --- |
| UTF-16 大恩迪安 | 0xFE 0xFF |
| UTF-16 小恩迪安 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果不存在位元組順序標記,則假定文本編碼為UTF-8。

要解決此警告,請確定此 Blob 的文本編碼是什麼,並添加相應的位元組順序標記。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告: Cosmos 資料庫集合「X」具有延遲索引策略。 某些資料可能會遺失

無法一致地查詢具有[延遲](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)索引策略的集合,從而導致索引器丟失數據。 要解決此警告,請將索引策略更改為"一致"。
