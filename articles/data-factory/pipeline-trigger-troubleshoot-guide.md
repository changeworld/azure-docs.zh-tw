---
title: 針對 Azure Data Factory 中的管線協調流程和觸發程式進行疑難排解
description: 使用不同的方法來針對 Azure Data Factory 中的管線觸發程式問題進行疑難排解。
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589146"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>針對 Azure Data Factory 中的管線協調流程和觸發程式進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 Azure Data Factory 中，「管線執行」可定義管線執行的執行個體。 例如，您有一個在上午8:00 點、9:00 AM 和 10:00 AM 執行的管線。 在此情況下，會有三個個別執行的管線或管線執行。 每個管線執行都有唯一的管線執行識別碼。 執行識別碼是 GUID (全域唯一識別碼) ，可定義該特定管線執行。

將引數傳遞給管線中定義的參數，通常可將管線執行具現化。 您可以用手動方式或使用「觸發程序」來執行管線。 如需詳細資料，請參閱 [Azure Data Factory 中的管線執行和觸發](concepts-pipeline-execution-triggers.md) 程式。

## <a name="common-issues-causes-and-solutions"></a>常見的問題、原因和解決方案

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>具有 Azure 函式的管線擲回私人端點連線錯誤
 
#### <a name="issue"></a>問題
針對某些內容，您會在私人端點上執行 Data Factory 和 Azure 函數應用程式。 您正在嘗試取得與 Azure 函式應用程式互動的管線以運作。 您已嘗試三種不同的方法，但有一個傳回錯誤 `Bad Request` ，其他兩個方法傳回 `103 Error Forbidden` 。

#### <a name="cause"></a>原因 
Data Factory 目前不支援 Azure 函數應用程式的私人端點連接器。 這應該是 Azure 函數應用程式拒絕呼叫的原因，因為它會設定為只允許來自 Private Link 的連接。

#### <a name="resolution"></a>解決方案
您可以建立型別為 **PrivateLinkService** 的私用端點，並提供函數應用程式的 DNS，且連接應該可以運作。

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>已終止管線執行，但監視器仍顯示進度狀態

#### <a name="issue"></a>問題
通常當您終止管線執行時，管線監視仍會顯示進度狀態。 發生這種情況的原因是因為瀏覽器中的快取問題，而您沒有適當的篩選器可進行監視。

#### <a name="resolution"></a>解決方案
重新整理瀏覽器並套用正確的篩選以進行監視。
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>複製管線失敗–找到的資料行數目超過預期的資料行計數 (DelimitedTextMoreColumnsThanDefined) 

#### <a name="issue"></a>問題  
如果您要複製的特定資料夾下的檔案包含具有不同架構的檔案，例如資料行數目不定、不同的分隔符號、引號字元設定或某些資料問題，Data Factory 管線最後會在此錯誤中執行：

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>解決方案
建立資料複製活動時，請選取 [二進位複製] 選項。 如此一來，當您將資料從一個 Data Lake 大量複製或遷移到另一個時，請使用 **binary** 選項，Data Factory 不會開啟檔案來讀取架構，而只會將每個檔案視為二進位檔，並將其複製到另一個位置。

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>當達到整合執行時間的容量限制時，管線執行失敗

#### <a name="issue"></a>問題
錯誤訊息：

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

此錯誤指出每個整合執行時間的限制（目前為50）。 請參閱 [限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) 以取得詳細資料。

如果您同時使用相同的整合執行時間執行大量的資料流程，它可能會造成這類錯誤。

#### <a name="resolution"></a>解決方案 
- 將這些管線與不同的觸發時間分開執行。
- 建立新的整合執行時間，並將這些管線分割到多個整合執行時間。

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>如何監視定期間隔的管線失敗

#### <a name="issue"></a>問題
通常需要依間隔監視 Data Factory 管線，例如5分鐘。 您可以使用端點從資料處理站查詢和篩選管線執行。 

#### <a name="recommendation"></a>建議
1. 設定 Azure 邏輯應用程式，每隔5分鐘查詢一次失敗的管線。
2. 然後，您可以根據每個 [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)，將附隨報告到我們的票證系統。

#### <a name="reference"></a>參考
- [外部-從 Data Factory 傳送通知](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>如何在管線中處理活動層級的錯誤和失敗

#### <a name="issue"></a>問題
Azure Data Factory 協調流程允許條件式邏輯，並且讓使用者根據先前活動的結果來採用不同的路徑。 它允許四個條件式路徑：「成功時 (預設傳遞) 」、「失敗時」、「完成時」和「略過」。 允許使用不同的路徑。

Azure Data Factory 定義管線執行成功和失敗，如下所示：

- 評估所有分葉層級活動的結果。 如果略過分葉活動，我們會改為評估其父活動。
- 只有在所有的離開都成功時，管線結果才會成功。

#### <a name="recommendation"></a>建議
- 遵循 [如何處理管線失敗和錯誤](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)的執行活動層級檢查。
- 使用 Azure 邏輯應用程式 [依 DataFactory 進行查詢，依]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)定期間隔監視管線。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：

*  [Data Factory 部落格](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 問與答頁面](/answers/topics/azure-data-factory.html)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)