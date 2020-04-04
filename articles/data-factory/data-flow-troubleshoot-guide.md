---
title: 排除資料流程故障
description: 瞭解如何在 Azure 數據工廠中解決數據流問題。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: a5244086c185c111762496086f8044f12f52be14
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632583"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>在 Azure 資料工廠中排除資料流程故障

本文探討 Azure 數據工廠中數據流的常見故障排除方法。

## <a name="common-errors-and-messages"></a>常見錯誤和訊息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>錯誤代碼:DF-執行器-來源不合法
- **訊息**: 資料預覽、除錯和導管資料串流執行失敗,因為容器不存在
- **原因**:當資料集包含儲存中不存在的容器時
- **建議**:確保資料集中引用的容器存在或可訪問。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>錯誤代碼:DF-執行器-系統隱式卡特

- **消息**: 不支援內部聯接的隱式點菜產品,而是使用 CROSS JOIN。 聯接中使用的列應為行創建唯一的鍵。
- **原因**:不支援邏輯計劃之間用於 INNER 聯接的隱式點菜產品。 如果聯接中使用的列創建唯一鍵,則至少需要關係兩側的一列。
- **建議**:對於非相等的聯接,您必須選擇 CUSTOM CROSS JOIN。

### <a name="error-code-df-executor-systeminvalidjson"></a>錯誤代碼:DF-執行器-系統無效

- **訊息**: JSON 分析錯誤、不支援的編碼或多行
- **原因**: JSON 檔案可能存在問題:不支援的編碼、損壞的位元組或使用 JSON 源作為許多嵌套行上的單個文件
- **建議**:驗證 JSON 檔的編碼是否受支援。 在使用 JSON 數據集的源轉換上,展開「JSON 設置」並打開「單一文檔」。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>錯誤代碼:DF 執行器-廣播逾時

- **訊息**: 廣播連線逾時錯誤,確保廣播串流在除錯執行中產生資料 60 秒以內,在工作執行中產生 300 秒的資料
- **原因**:在調試運行中廣播的預設超時為 60 秒,在作業運行中預設超時為 300 秒。 選擇廣播的流似乎很大,無法在此限制內生成數據。
- **建議**:避免廣播處理可能需要60秒以上的大型數據流。 選擇較小的流進行廣播。 大型 SQL/DW 表和源檔通常是壞候選項。

### <a name="error-code-df-executor-conversion"></a>錯誤代碼:DF-執行器轉換

- **訊息**:由於字元無效,轉換為日期或時間失敗
- **原因**: 資料未採用預期格式
- **建議**:使用正確的資料型態

### <a name="error-code-df-executor-invalidcolumn"></a>錯誤代碼:DF-執行器-無效欄位

- **訊息**:列名稱需要在查詢中指定,如果使用 SQL 函式設定別名
- **原因**:未指定欄位
- **建議**:如果使用 SQL 函數(如最小值/max()等,則設置別名。

### <a name="error-code-getcommand-outputasync-failed"></a>錯誤代碼:取得指令輸出 Async 失敗

- **訊息**: 在資料流程除錯和資料預覽期間: 取得指令輸出 Async 失敗與 ...
- **原因**:這是後端服務錯誤。 您可以重試該操作,也可以重新啟動除錯工作階段。
- **建議**:如果重試並重新啟動無法解決問題,請聯繫客戶支援。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>錯誤代碼:遇到意外例外失敗

- **消息**:在數據流活動執行期間:命中意外異常並執行失敗。
- **原因**:這是後端服務錯誤。 您可以重試該操作,也可以重新啟動除錯工作階段。
- **建議**:如果重試並重新啟動無法解決問題,請聯繫客戶支援。

## <a name="general-troubleshooting-guidance"></a>一般故障排除指南

1. 檢查數據集連接的狀態。 在每個源和接收器轉換中,訪問您正在使用的每個資料集的連結服務並測試連接。
1. 從數據流設計器檢查檔和錶連接的狀態。 開啟除錯並按一下源轉換上的數據預覽,以確保您能夠存取資料。
1. 如果數據預覽中的所有內容都看起來不錯,請進入管道設計器,並將數據流放入管道活動中。 調試管道以進行端到端測試。

## <a name="next-steps"></a>後續步驟

有關更多故障排除説明,請嘗試以下資源:
*  [資料工廠博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [資料工廠功能要求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 視訊](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [數據工廠的堆疊溢出論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有關資料工廠的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射資料串流效能指導](concepts-data-flow-performance.md)
