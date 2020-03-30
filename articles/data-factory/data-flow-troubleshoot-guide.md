---
title: 排除資料流程故障
description: 瞭解如何在 Azure 資料工廠中解決資料流程問題。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472123"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>在 Azure 資料工廠中排除資料流程故障

本文探討 Azure 資料工廠中資料流程的常見故障排除方法。

## <a name="common-errors-and-messages"></a>常見錯誤和消息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>錯誤代碼：DF-執行器-源無效負載
- **消息**： 資料預覽、調試和管道資料流程執行失敗，因為容器不存在
- **原因**：當資料集包含存儲中不存在的容器時
- **建議**：確保資料集中引用的容器存在或可訪問。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>錯誤代碼：DF-執行器-系統隱式卡特

- **消息**： 不支援內部聯接的隱式點菜產品，而是使用 CROSS JOIN。 聯接中使用的列應為行創建唯一的鍵。
- **原因**：不支援邏輯計畫之間用於 INNER 聯接的隱式點菜產品。 如果聯接中使用的列創建唯一鍵，則至少需要關係兩側的一列。
- **建議**：對於非相等的聯接，您必須選擇 CUSTOM CROSS JOIN。

### <a name="error-code-df-executor-systeminvalidjson"></a>錯誤代碼：DF-執行器-系統無效

- **消息**： JSON 分析錯誤、不支援的編碼或多行
- **原因**： JSON 檔可能存在問題：不支援的編碼、損壞的位元組或使用 JSON 源作為許多嵌套行上的單個文檔
- **建議**：驗證 JSON 檔的編碼是否受支援。 在使用 JSON 資料集的源轉換上，展開"JSON 設置"並打開"單一文檔"。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>錯誤代碼：DF 執行器-廣播超時

- **消息**： 廣播聯接逾時錯誤，確保廣播流在調試運行中生成資料 60 秒以內，在作業運行中生成 300 秒的資料
- **原因**：在調試運行中廣播的預設超時為 60 秒，在作業運行中預設超時為 300 秒。 選擇廣播的流似乎很大，無法在此限制內生成資料。
- **建議**：避免廣播處理可能需要 60 秒以上的大型資料流程。 選擇較小的流進行廣播。 大型 SQL/DW 表和原始檔案通常是壞候選項。

### <a name="error-code-df-executor-conversion"></a>錯誤代碼：DF-執行器轉換

- **消息**：由於字元無效，轉換為日期或時間失敗
- **原因**： 資料未採用預期格式
- **建議**：使用正確的資料類型

### <a name="error-code-df-executor-invalidcolumn"></a>錯誤代碼：DF-執行器-無效列

- **消息**：列名稱需要在查詢中指定，如果使用 SQL 函數設置別名
- **原因**：未指定列名稱
- **建議**：如果使用 SQL 函數（如最小值/max（）等，則設置別名。

## <a name="general-troubleshooting-guidance"></a>常規故障排除指南

1. 檢查資料集連接的狀態。 在每個源和接收器轉換中，訪問您正在使用的每個資料集的連結服務並測試連接。
1. 從資料流程設計器檢查檔和表連接的狀態。 打開調試並按一下源轉換上的資料預覽，以確保您能夠訪問資料。
1. 如果資料預覽中的所有內容都看起來不錯，請進入管線設計師，並將資料流程放入管道活動中。 調試管道以進行端到端測試。

## <a name="next-steps"></a>後續步驟

有關更多故障排除説明，請嘗試以下資源：
*  [資料工廠博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [資料工廠功能請求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 視頻](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [資料工廠的堆疊溢位論壇](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有關資料工廠的 Twitter 資訊](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射資料流程 性能指南](concepts-data-flow-performance.md)
