---
title: 使用流分析處理來自事件中心 Azure 的資料 |微軟文檔
description: 本文介紹如何使用 Azure 流分析工作處理 Azure 事件中心的資料。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991937"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>使用 Azure 流分析處理事件中心的資料 
Azure 流分析服務使從 Azure 事件中心引入、處理和分析流資料變得容易，從而提供了強大的見解，從而推動即時操作。 通過此集成，您可以快速建立熱路徑分析管道。 可以使用 Azure 門戶視覺化傳入資料並編寫流分析查詢。 查詢準備就緒後，只需按一下幾下即可將其移動到生產中。 

## <a name="key-benefits"></a>主要權益
以下是 Azure 事件中心和 Azure 流分析集成的主要優勢： 
- **預覽資料**– 可以從 Azure 門戶中的事件中心預覽傳入資料。
- **測試查詢**– 準備轉換查詢並直接在 Azure 門戶中進行測試。 有關查詢語言語法，請參閱[流分析查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)文檔。
- **將查詢部署到生產**中 – 您可以通過創建和啟動 Azure 流分析作業將查詢部署到生產中。

## <a name="end-to-end-flow"></a>端到端流

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
1. 導航到**事件中心命名空間**，然後導航到具有傳入資料**的事件中心**。 
1. 在事件中心頁上選擇 **"處理資料**"。  

    ![處理資料磁貼](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 在"**從事件磁貼啟用即時見解**"上選擇 **"流覽**"。 

    ![選取串流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 您將看到一個查詢頁，該頁的值已為以下欄位設置：
    1. 事件**中心**作為查詢的輸入。
    1. 使用 SELECT 語句**對 SQL 查詢**進行示例。 
    1. 用於引用查詢測試結果的**輸出**別名。 

        ![查詢編輯器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  首次使用此功能時，此頁面會請求您創建消費者組的許可權以及活動中心預覽傳入資料的策略。
1. 在 **"輸入預覽**窗格中**創建**"，如上圖所示。 
1. 您將立即在此選項卡中看到最新傳入資料的快照。
    - 將自動檢測資料中的序列化類型 （JSON/CSV）。 您也可以手動將其更改為 JSON/CSV/AVRO。
    - 您可以預覽表格式或原始格式的傳入資料。 
    - 如果顯示的資料不是最新的，請選擇 **"刷新"** 以查看最新事件。 

        下面是**表格式**的資料示例：![表格式的結果](./media/process-data-azure-stream-analytics/snapshot-results.png)

        下面是**原始格式**資料的示例： 

        ![以原始格式的結果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 選擇 **"測試"查詢**，在 **"測試結果"** 選項卡中查看查詢的測試結果快照。您還可以下載結果。

    ![測試查詢結果](./media/process-data-azure-stream-analytics/test-results.png)
1. 編寫您自己的查詢以轉換資料。 請參閱[流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 測試查詢並希望將其移動到生產中後，選擇 **"部署查詢**"。 要部署查詢，請創建 Azure 流分析作業，您可以在其中為作業設置輸出，然後啟動作業。 要創建流分析作業，請為作業指定名稱，然後選擇"**創建**"。

      ![建立 Azure 串流分析作業](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  我們建議您為從事件中心頁創建的每個新的 Azure 流分析作業創建消費者組和策略。 消費者組僅允許五個併發讀取器，因此為每個作業提供專用消費者組將避免因超出該限制而可能出現的任何錯誤。 專用策略允許您輪換金鑰或撤銷許可權，而不會影響其他資源。 
1. 現在，將創建流分析作業，其中查詢與測試的查詢相同，並且輸入是事件中心。 

9.  要完成管道，請設置查詢的**輸出**，然後選擇 **"開始"** 以啟動作業。

    > [!NOTE]
    > 在開始作業之前，不要忘記將輸出別名替換為在 Azure 流分析中創建的輸出名稱。

      ![設置輸出並啟動作業](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知限制
測試查詢時，測試結果大約需要 6 秒才能載入。 我們正在努力提高測試性能。 但是，在生產中部署時，Azure 流分析將具有低於秒的延遲。

## <a name="streaming-units"></a>串流單位
Azure 流分析作業預設為三個流式處理單元 （SUs）。 要調整此設置，請在 Azure 門戶中的 **"流分析"作業**頁中選擇左側功能表的 **"縮放**"。 要瞭解有關流式處理單元的更多，請參閱[瞭解和調整流式處理單元](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![縮放流式處理單元](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>後續步驟
要瞭解有關流分析查詢的更多資訊，請參閱[流分析查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)
