---
title: 針對資料流程進行疑難排解
description: 瞭解如何針對 Azure Data Factory 中的資料流程問題進行疑難排解。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472123"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>針對 Azure Data Factory 中的資料流程進行疑難排解

本文探討 Azure Data Factory 中資料流程的常見疑難排解方法。

## <a name="common-errors-and-messages"></a>常見的錯誤和訊息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>錯誤碼： DF-執行程式-SourceInvalidPayload
- **訊息**：資料預覽、debug 和管線資料流程執行失敗，因為容器不存在
- **原因**：當資料集包含不存在於儲存體中的容器時
- **建議**：請確定您的資料集內所參考的容器存在或可存取。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>錯誤碼： DF-執行程式-SystemImplicitCartesian

- **訊息**：不支援內部聯結的隱含笛卡兒乘積，請改用 CROSS join。 聯結中使用的資料行應該建立資料列的唯一索引鍵。
- **原因**：不支援邏輯計畫之間內部聯結的隱含笛卡兒乘積。 如果聯結中使用的資料行建立唯一索引鍵，則需要關聯性兩邊的至少一個資料行。
- **建議**：對於不相等的聯結，您必須選擇自訂交叉聯結。

### <a name="error-code-df-executor-systeminvalidjson"></a>錯誤碼： DF-執行程式-SystemInvalidJson

- **訊息**： JSON 剖析錯誤、不支援的編碼或多行
- **原因**： JSON 檔案可能的問題：不支援的編碼、損毀的位元組，或在許多嵌套行上使用 json 來源做為單一檔
- **建議**：確認支援 JSON 檔案的編碼。 在使用 JSON 資料集的來源轉換中，展開 [JSON 設定]，然後開啟 [單一檔]。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>錯誤碼： DF-執行程式-BroadcastTimeout

- **訊息**：廣播聯結逾時錯誤，請確定廣播串流會在執行調試的60秒內產生資料，並在作業執行中產生300秒
- **原因**：廣播在執行偵錯工具時的預設超時時間為60秒，而在作業回合中為300秒。 選擇要廣播的資料流程似乎很大，所以無法在此限制內產生資料。
- **建議**：避免廣播大型資料流程，其中處理可能需要超過60秒的時間。 請改為選擇較小的串流來進行廣播。 大型 SQL/DW 資料表和來源檔案通常是不良的候選項目。

### <a name="error-code-df-executor-conversion"></a>錯誤碼： DF-執行程式-轉換

- **訊息**：因為字元無效，所以轉換成日期或時間失敗
- **原因**：資料不是預期的格式
- **建議**：使用正確的資料類型

### <a name="error-code-df-executor-invalidcolumn"></a>錯誤碼： DF-執行程式-InvalidColumn

- **訊息**：必須在查詢中指定資料行名稱，並在使用 SQL 函數時設定別名
- **原因**：未指定資料行名稱
- **建議**：如果使用 SQL 函式，例如 min （）/max （）等，請設定別名。

## <a name="general-troubleshooting-guidance"></a>一般疑難排解指引

1. 檢查資料集連接的狀態。 在每個「來源」和「接收」轉換中，流覽您所使用之每個資料集的連結服務，並測試連接。
1. 從 [資料流程設計師] 檢查檔案和資料表連接的狀態。 開啟 [偵錯工具]，然後按一下來源轉換上的 [資料預覽]，以確保您能夠存取您的資料。
1. 如果資料預覽中的一切都不錯，請進入「管線設計師」，並將您的資料流程放在管線活動中。 針對端對端測試進行管線的偵錯工具。

## <a name="next-steps"></a>後續步驟

如需更多疑難排解協助，請嘗試下列資源：
*  [Data Factory 的 blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 影片](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [關於 Data Factory 的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 對應資料流程效能指南](concepts-data-flow-performance.md)
