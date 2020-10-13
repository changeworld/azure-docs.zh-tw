---
title: 針對對應資料流程進行疑難排解
description: 了解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 47d1f3c78a303f7a45457a435fa11f074c41d7aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316149"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>針對 Azure Data Factory 中的對應資料流程進行疑難排解

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文探討在 Azure Data Factory 中對應資料流程的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>錯誤碼：DF-Executor-SourceInvalidPayload
- **訊息**：資料預覽、偵錯和管線資料流程執行失敗，因為容器不存在
- **原因**：當資料集包含不存在於儲存體中的容器時
- **建議**：請確定您資料集內所參考的容器存在或可供存取。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>錯誤碼：DF-Executor-SystemImplicitCartesian

- **訊息**：不支援內部聯結的隱含笛卡兒乘積，請改用交叉聯結。 聯結中使用的資料行應該為資料列建立唯一的索引鍵。
- **原因**：邏輯計畫之間不支援內部聯結的隱含笛卡兒乘積。 如果聯結中使用的資料行建立唯一索引鍵，則至少需要關聯性兩側的一個資料行。
- **建議**：針對非相等型聯結，您必須選擇自訂交叉聯結。

### <a name="error-code-df-executor-systeminvalidjson"></a>錯誤碼：DF-Executor-SystemInvalidJson

- **訊息**：JSON 剖析錯誤、不支援的編碼或多行
- **原因**：JSON 檔案可能有問題：不支援的編碼、損毀的位元組，或在許多巢狀行上使用 JSON 來源作為單一文件
- **建議**：確認可支援 JSON 檔案的編碼。 在使用 JSON 資料集的來源轉換中，展開 [JSON 設定]，然後開啟 [單一文件]。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>錯誤碼：DF-Executor-BroadcastTimeout

- **訊息**：廣播聯結逾時錯誤，請確定廣播串流會在執行偵錯的 60 秒內產生資料，以及在執行作業的 300 秒內產生資料
- **原因**：在偵錯工具執行中，廣播的預設超時時間為60秒，而在工作執行中有300秒。 選擇進行廣播的資料流程似乎太大而無法在此限制內產生資料。
- **建議**：在資料流程轉換上查看 [最佳化] 索引標籤中的 [聯結]、[存在] 和 [查閱]。 [廣播] 的預設選項是 [自動]。 如果設定了 [自動]，或您手動將左側或右側設定為 [固定] 下的廣播，則可以設定較大的 Azure Integration Runtime 設定，或關閉廣播。 要在資料流程中獲得最佳效能的建議方法是允許 Spark 使用「自動」進行廣播，並使用「記憶體最佳化的 Azure IR」。

如果您是從 debug 管線執行的 debug 測試執行中執行資料流程，您可能會更頻繁地遇到此狀況。 這是因為 ADF 會將廣播等待時間節流至60秒，以維持更快的 debug 體驗。 如果您想要將其從觸發的執行延伸至300秒的超時時間，您可以使用 Debug > Use 活動 Runtime 選項來利用執行資料流程管線活動中定義的 Azure IR。

### <a name="error-code-df-executor-conversion"></a>錯誤碼：DF-Executor-Conversion

- **訊息**：轉換成日期或時間失敗，因為字元無效
- **原因**：資料未使用預期的格式
- **建議**：使用正確的資料類型

### <a name="error-code-df-executor-invalidcolumn"></a>錯誤碼：DF-Executor-InvalidColumn

- **訊息**：必須在查詢中指定資料行名稱，並在使用 SQL 函式時設定別名
- **原因**：未指定資料行名稱
- **建議**：如果使用 min()/max() 等 SQL 函式，請設定別名。

 ### <a name="error-code-df-executor-drivererror"></a>錯誤碼： DF-執行程式-DriverError
- **Message**： INT96 是 ADF 資料流程不支援的舊版時間戳記類型。 請考慮將資料行類型升級為最新的類型。
- **原因**：驅動程式錯誤
- **建議**： INT96 是舊版的時間戳記類型，ADF 資料流程不支援此類型。 請考慮將資料行類型升級為最新的類型。

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>錯誤碼： DF-執行程式-BlockCountExceedsLimitError
- **Message**：未認可的區塊計數不能超過100000區塊的上限。 檢查 blob 設定。
- **原因**： blob 中最多可以有100000個未認可的區塊。
- **建議**：如需詳細資料，請洽詢 Microsoft 產品小組以取得此問題

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>錯誤碼： DF-執行程式-PartitionDirectoryError
- **訊息**：指定的來源路徑具有多個分割目錄 (例如 <Source Path> /<磁碟分割根目錄 1>/a = 10/b = 20， <Source Path> /<磁碟分割根目錄 2>/c = 10/d = 30) 或資料分割目錄與其他檔案或非分割目錄 (例如 <Source Path> /<磁碟分割根目錄 1>/a = 10/b = 20、 <Source Path> /目錄 2/file1) 、從來源路徑移除磁碟分割根目錄，然後透過個別的來源轉換來讀取它。
- **原因**：來源路徑具有多個資料分割目錄或資料分割目錄與其他檔案或非資料分割目錄。
- **建議**：從來源路徑移除分割的根目錄，並透過個別的來源轉換來讀取。

 ### <a name="error-code-df-executor-outofmemoryerror"></a>錯誤碼： DF-執行程式-OutOfMemoryError
- **訊息**：叢集在執行期間發生記憶體不足的問題，請使用具有較大核心計數和/或記憶體優化計算類型的整合執行時間重試
- **原因**：叢集記憶體不足
- **建議**： Debug 叢集適用于開發用途。 利用資料取樣、適當的計算類型，以及執行承載的大小。 請參閱對應 [資料流程效能指南](concepts-data-flow-performance.md) ，以進行微調以達到最佳效能。

 ### <a name="error-code-df-executor-illegalargument"></a>錯誤碼： DF-執行程式-illegalArgument
- **訊息**：請確認連結服務中的存取金鑰正確
- **原因**：帳戶名稱或存取金鑰不正確
- **建議**：確定連結服務中指定的帳號名稱或存取金鑰正確無誤。 

 ### <a name="error-code-df-executor-invalidtype"></a>錯誤碼： DF-執行程式-InvalidType
- **Message**：請確認參數的類型符合傳入的數值型別。 目前不支援從管線傳遞 float 參數。
- **原因**：宣告的型別與實際的參數值之間有不相容的資料類型
- **建議**：檢查傳遞至資料流程的參數值是否符合宣告的型別。

 ### <a name="error-code-df-executor-columnunavailable"></a>錯誤碼： DF-執行程式-ColumnUnavailable
- **訊息**：運算式中使用的資料行名稱無法使用或無效
- **原因**：運算式中使用了無效或無法使用的資料行名稱
- **建議**：檢查運算式中使用的資料行名稱)  (s

 ### <a name="error-code-df-executor-parseerror"></a>錯誤碼： DF-執行程式-ParseError
- **Message**：無法剖析運算式
- **原因**：運算式因為格式化而發生剖析錯誤
- **建議**：檢查運算式中的格式

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
- **建議**：移至 [Debug 設定]，並增加來源資料列限制中的資料列數目。 請確定您選取的 Azure IR 具有夠大的資料流程叢集，以處理更多資料。


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
