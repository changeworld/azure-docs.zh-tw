---
title: 針對資料流程進行疑難排解
description: 了解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.openlocfilehash: 6f2bf98e1c527be27ba0f08a43785ae7d3aea726
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594146"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>針對 Azure Data Factory 中的資料流程進行疑難排解

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文將探討 Azure Data Factory 中資料流程的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>錯誤碼：DF-Executor-SourceInvalidPayload
- **訊息**：資料預覽、偵錯和管線資料流程執行失敗，因為容器不存在
- **原因**：當資料集包含不存在於儲存體中的容器時
- **建議**：請確定您資料集內所參考的容器存在或可供存取。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>錯誤碼：DF-Executor-SystemImplicitCartesian

- **訊息**：不支援內部聯結的隱含笛卡兒乘積，請改用交叉聯結。 聯結中使用的資料行應該為資料列建立唯一的索引鍵。
- **原因**：邏輯計畫之間不支援內部聯結的隱含笛卡兒乘積。 如果聯結中使用的資料行建立了唯一索引鍵，則需要關聯性兩端的至少一個資料行。
- **建議**：針對非相等型聯結，您必須選擇自訂交叉聯結。

### <a name="error-code-df-executor-systeminvalidjson"></a>錯誤碼：DF-Executor-SystemInvalidJson

- **訊息**：JSON 剖析錯誤、不支援的編碼或多行
- **原因**：JSON 檔案可能有問題：不支援的編碼、損毀的位元組，或在許多巢狀行上使用 JSON 來源作為單一文件
- **建議**：確認可支援 JSON 檔案的編碼。 在使用 JSON 資料集的來源轉換中，展開 [JSON 設定]，然後開啟 [單一文件]。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>錯誤碼：DF-Executor-BroadcastTimeout

- **訊息**：廣播聯結逾時錯誤，請確定廣播串流會在執行偵錯的 60 秒內產生資料，以及在執行作業的 300 秒內產生資料
- **原因**：在執行偵錯時，廣播的預設逾時為 60 秒，而在執行作業時則為 300 秒。 選擇要廣播的串流似乎太大，所以無法在此限制內產生資料。
- **建議**：在資料流程轉換上查看 [最佳化] 索引標籤中的 [聯結]、[存在] 和 [查閱]。 [廣播] 的預設選項是 [自動]。 若已設定，或如果您手動將左側或右側設定為 [固定] 底下的廣播，則可以設定較大的 Azure Integration Runtime 設定，或關閉廣播。 要在資料流程中獲得最佳效能的建議方法是允許 Spark 使用「自動」進行廣播，並使用「記憶體最佳化的 Azure IR」。

如果您是從 debug 管線執行的 debug 測試執行中執行資料流程，您可能會更頻繁地遇到此狀況。 這是因為 ADF 會將廣播等待時間節流至60秒，以維持更快的 debug 體驗。 如果您想要將其從觸發的執行延伸至300秒的時間，您可以使用 Debug > Use 活動 Runtime 選項來利用執行資料流程管線活動中定義的 Azure IR。

### <a name="error-code-df-executor-conversion"></a>錯誤碼：DF-Executor-Conversion

- **訊息**：轉換成日期或時間失敗，因為字元無效
- **原因**：資料未使用預期的格式
- **建議**：使用正確的資料類型

### <a name="error-code-df-executor-invalidcolumn"></a>錯誤碼：DF-Executor-InvalidColumn

- **訊息**：必須在查詢中指定資料行名稱，並在使用 SQL 函式時設定別名
- **原因**：未指定資料行名稱
- **建議**：如果使用 min()/max() 等 SQL 函式，請設定別名。

### <a name="error-code-getcommand-outputasync-failed"></a>錯誤碼：GetCommand OutputAsync 失敗

- **訊息**：在資料流程偵錯和資料預覽期間：GetCommand OutputAsync 失敗，並出現...
- **原因**：這是後端服務錯誤。 您可以重試此作業，也可以重新啟動您的偵錯工作階段。
- **建議**：如果重試和重新啟動無法解決問題，請連絡客戶支援。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>錯誤碼：遇到非預期的例外狀況，執行失敗

- **訊息**：在資料流程活動執行期間：遇到非預期的例外狀況，執行失敗。
- **原因**：這是後端服務錯誤。 您可以重試此作業，也可以重新啟動您的偵錯工作階段。
- **建議**：如果重試和重新啟動無法解決問題，請連絡客戶支援。

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>錯誤碼： Debug data preview 在聯結上沒有輸出資料

- **Message**：有大量的 null 值或遺漏值，可能是因為取樣的資料列數目太少所造成。 請嘗試更新調試資料列限制並重新整理資料。
- **原因**：聯結條件不符合任何資料列，或在資料預覽期間導致大量的 null。
- **建議**：移至 [Debug 設定]，並增加來源資料列限制中的資料列數目。 確定您已選取並 Azure IR 具有夠大的資料流程叢集，以處理更多資料。


## <a name="general-troubleshooting-guidance"></a>一般疑難排解指引

1. 檢查資料集連線狀態。 在每個「來源」和「接收」轉換中，瀏覽每個所使用資料集的連結服務，並測試連線。
1. 從資料流程設計工具檢查檔案和資料表連線的狀態。 開啟 [偵錯]，然後按一下來源轉換上的 [資料預覽]，以確保您能夠存取資料。
1. 如果資料預覽看起來沒問題，請進入管線設計工具，並將資料流程放在管線活動中。 針對端對端測試的管線進行偵錯。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解的協助，請嘗試下列資源：
*  [Data Factory 部落格](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory 的 Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 對應資料流程效能指南](concepts-data-flow-performance.md)
