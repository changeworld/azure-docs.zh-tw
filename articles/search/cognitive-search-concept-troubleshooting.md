---
title: AI 擴充設計秘訣
titleSuffix: Azure Cognitive Search
description: 在 Azure 認知搜尋中設定 AI 擴充管線的秘訣和疑難排解。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 1ab366cddbabf7e6d574189892e779ab49f6fad8
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403376"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 認知搜尋的 AI 擴充秘訣

本文包含秘訣和訣竅清單，可讓您在 Azure 認知搜尋中開始使用 AI 擴充功能時，繼續進行移動。 

如果您尚未這麼做，請逐步執行 [本教學課程：瞭解如何呼叫 ai 擴充 api](cognitive-search-quickstart-blob.md) ，以實務將 ai 擴充套用至 blob 資料來源。

## <a name="tip-1-start-with-a-small-dataset"></a>秘訣 1：從小型資料集開始著手
想要快速找出問題，最理想的方式是加快修正問題的速度。 而要想縮短編製索引的時間，最好是藉由減少要編製索引的文件數目來著手。 

一開始請先建立僅含有少量文件/記錄的資料來源。 您的文件範例應適當代表各種要編製索引的文件。 

請透過端對端管線執行您的文件範例，並確認結果符合您的需求。 如果您對結果感到滿意，則可以將更多檔案新增至您的資料來源。

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>秘訣 2：確定您的資料來源認證是正確的
在您定義使用資料來源連線的索引子之前，該連線將沒有效用。 如果您發現有任何錯誤指出索引子無法取得資料，請確定：
- 您的連接字串正確無誤。 特別是當您建立 SAS 權杖時，請務必使用 Azure 認知搜尋所預期的格式。 請參閱[如何指定認證](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)一節，以了解不同的支援格式。
- 您在索引子中的容器名稱是正確的。

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>祕訣 3：即使發生失敗的狀況，也請找出可行的部分
有時候，輕微的失敗狀況也會使索引子停止運作。 如果您做好逐一修正問題的計劃，這就不構成妨礙。 不過，您可能會想要忽略特定類型的錯誤，而讓索引子能繼續執行，以便您查看哪些流程實際上是可運作的。

在此情況下，您可以指示索引子忽略錯誤。 若要這麼做，可以在定義索引子的過程中將 *maxFailedItems* 和 *maxFailedItemsPerBatch* 設定為 -1。

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> 最佳做法是將生產工作負載的 maxFailedItems maxFailedItemsPerBatch 設定為0

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>秘訣4：使用 Debug 會話來識別和解決技能集的問題 

「偵錯工作階段」是一種視覺化編輯器，適用於 Azure 入口網站中的現有技能。 在 debug 會話內，您可以在 AI 擴充管線中找出並解決錯誤、驗證變更，以及認可生產技能集的變更。 這是預覽功能，請 [參閱檔](./cognitive-search-debug-session.md)。 如需概念和開始使用的詳細資訊，請參閱 [Debug 會話](./cognitive-search-tutorial-debug-sessions.md)。

在單一檔上進行的 Debug 會話可讓您以反復方式建立更複雜的擴充管線。

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>秘訣5：在幕後查看擴充的檔 
擴充的文件是在擴充期間建立的暫時性結構，在程序完成後即會刪除。

若要擷取在索引編製期間建立之擴充文件的快照集，請將名為 ```enriched``` 的欄位新增至您的索引。 索引子會自動在該欄位中傾印該文件所有擴充項目的字串表示法。

```enriched``` 欄位將會包含一個字串，作為記憶體內部的擴充文件在 JSON 中的邏輯表示法。  不過，該欄位的值是有效的 JSON 文件。 引號會逸出，因此您必須將 `\"` 取代為 `"`，才能以格式化 JSON 的形式檢視文件。 

擴充欄位的用途是偵錯，只是為了幫助您了解以運算式進行評估之內容的邏輯圖形。 您不應依賴此欄位進行索引編製。

請新增 ```enriched``` 欄位作為索引定義的一部分，以供偵錯之用：

#### <a name="request-body-syntax"></a>要求本文的語法
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>秘訣6：預期內容無法顯示

內容遺失可能是由於文件在索引編製期間遭到捨棄所致。 免費和基本層的文件大小限制較低。 任何超過限制的檔案在索引編製期間都會被捨棄。 您可以在 Azure 入口網站中查看遭到捨棄的文件。 請在搜尋服務儀表板中按兩下索引子圖格。 檢閱成功編製索引的文件比率。 如果不是 100%，您可以按一下該比例以取得更多詳細資料。 

如果問題與檔案大小有關，您可能會看到如下的錯誤：「blob」的 \<file-name> 大小為 \<file-size> 位元組，超過目前服務層級的檔解壓縮大小上限。」 如需關於索引子限制的詳細資訊，請參閱[服務限制](search-limits-quotas-capacity.md)。

無法顯示內容的另一個原因，可能與輸入/輸出對應錯誤有關。 例如，輸出目標名稱為 "People"，但索引欄位名稱為小寫的 "people"。 系統可能會為整個管線傳回 201 成功訊息，因此您認為索引編製成功，而事實上欄位卻是空白的。 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>秘訣7：將處理延伸至超過執行時間上限 (24 小時時間範圍) 

影像分析即使在簡單的情況下也需要密集運算，因此在影像時特別大型或複雜時，處理時間可能會超過允許的時間上限。 

執行時間上限會依層而有所不同：在免費層上只有幾分鐘，而在計費層則有 24 小時的索引編製時間。 如果隨需處理無法在 24 小時的期間內完成，請切換至排程，讓索引子從中斷之處繼續處理。 

對於排程的索引子，索引編製將會依排程從最後一份已知的正常文件繼續進行。 藉由使用週期性的排程，索引子將可持續處理影像待辦項目達數小時或數天，直到所有未處理的影像皆處理完成為止。 如需排程語法的詳細資訊，請參閱 [步驟3：建立-索引子](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) ，或瞭解 [如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

> [!NOTE]
> 如果索引子設定為特定的排程，但每次執行時重複出現在同一份檔中，則索引子會以較不頻繁的間隔開始執行 (最多每24小時) 最多一次，直到它成功地進行進度為止。  如果您認為您已修正導致索引子停滯在某個時間點的任何問題，您可以執行索引子的隨選執行，如果成功，則索引子會再次回到其設定排程間隔。

對於以入口網站為基礎的索引編製 (如快速入門所說明)，選擇 [執行一次] 索引子選項，會將處理時間限制為 1 小時 (`"maxRunTime": "PT1H"`)。 您可以延長處理時間範圍。

## <a name="tip-8-increase-indexing-throughput"></a>秘訣8：提高索引輸送量

對於[平行索引編製](search-howto-large-index.md)，請將您的資料放入多個容器中，或是相同容器內的多個虛擬資料夾中。 接著，請建立多個資料來源和索引子配對。 所有索引子將可使用相同的技能集，並寫入至相同的目標搜尋索引，而讓您的搜尋應用程式無須辨識此分割。

## <a name="see-also"></a>另請參閱

+ [快速入門：在入口網站中建立 AI 擴充管線](cognitive-search-quickstart-blob.md)
+ [教學課程：瞭解 AI 擴充 REST Api](cognitive-search-tutorial-blob.md)
+ [指定資料來源認證](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何將擴充的欄位對應至索引](cognitive-search-output-field-mapping.md)