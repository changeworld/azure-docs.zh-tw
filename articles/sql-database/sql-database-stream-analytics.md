---
title: 使用流分析集成流資料（預覽）
description: 使用 Azure 流分析將資料流程式傳輸到 Azure SQL 資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820834"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>使用 Azure SQL 資料庫流分析集成（預覽）資料流資料

使用者現在可以使用[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)直接從 Azure 門戶中的 SQL 資料庫中將即時流資料引入、處理、查看和分析到表中。 此體驗支援各種方案，如互聯汽車、遠端監控、欺詐檢測等。 在 Azure 門戶中，您可以選擇事件源（事件中心/IoT 中心）、查看傳入的即時事件以及選擇要存儲事件的表。 您還可以在門戶中編寫流分析查詢語言查詢，以轉換傳入事件並將其存儲在所選表中。 此新進入點是流分析中已經存在的創建和配置體驗的補充。 此體驗從資料庫的上下文開始，使您能夠快速設置流分析作業，並在 Azure SQL 資料庫和流分析體驗之間無縫導航。

![流分析流](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>主要權益

- 最小上下文切換：可以從門戶中的 SQL 資料庫開始，並開始將即時資料引入表中，而無需切換到任何其他服務。 
- 減少步驟數：資料庫和表的上下文用於預配置流分析作業。
- 預覽資料的其他易用性：在所選表的上下文中預覽來自事件源（事件中心/IoT 中心）的傳入資料 


## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您需要下列資源：

- Azure 訂用帳戶。 如果沒有 Azure 訂閱，[請創建一個免費帳戶](https://azure.microsoft.com/free/)。 
- SQL 資料庫。 有關詳細資訊，請參閱[在 Azure SQL 資料庫中創建單個資料庫](sql-database-single-database-get-started.md)。
- 允許電腦連接到 Azure SQL 伺服器的防火牆規則。 有關詳細資訊，請參閱[創建伺服器級防火牆規則](sql-database-server-level-firewall-rule.md)。


## <a name="configure-stream-analytics-integration"></a>配置流分析集成

1. 登入 Azure 入口網站。 
2. 導航到要引入流資料的 SQL 資料庫。 選擇**流分析（預覽）。** 

    ![串流分析](media/sql-database-stream-analytics/stream-analytics.png)

3. 要開始將流式處理資料引入此 SQL 資料庫，請選擇"**創建"** 並指定流式處理作業的名稱，然後選擇"**下一步：輸入**"。 

    ![創建流分析作業](media/sql-database-stream-analytics/create-job.png)

4. 輸入事件源詳細資訊，然後選擇 **"下一步：輸出**"。

   - **輸入類型**：事件中心/IoT 中心
   - **輸入別名**：輸入名稱以標識事件源 
   - **訂閱**： 與 SQL 資料庫訂閱相同 
   - **事件中心命名空間**：命名空間的名稱 
   - **事件中心名稱**：選定命名空間中事件中心的名稱 
   - **事件中心策略名稱**（預設為創建新）：提供策略名稱 
   - **事件中心消費者組**（預設為創建新）：提供消費者組名稱  
     - 我們建議您為在此處創建的每個新的 Azure 流分析作業創建一個消費者組和策略。 消費者組僅允許五個併發讀取器，因此為每個作業提供專用消費者組將避免因超出該限制而可能出現的任何錯誤。 專用策略允許您輪換金鑰或撤銷許可權，而不會影響其他資源。

    ![創建流分析作業](media/sql-database-stream-analytics/create-job-output.png)

5. 選擇要將流式資料引入到哪個表。 完成後，選擇 **"創建**"。
   - **使用者名**，**密碼**： 輸入 SQL 伺服器身份驗證的憑據。 選取 [驗證]****。
   - **表**： 選擇 **"創建新**"或使用**現有**。 在此流中，讓我們選擇 **"創建**"。 這將在啟動流分析作業時創建新表。

    ![創建流分析作業](media/sql-database-stream-analytics/create.png)

6. 將打開一個查詢頁，其中包含以下詳細資訊：

   - 輸入 **（** 輸入事件源），您將從中引入資料  
   - 將存儲轉換資料的**輸出**（輸出表） 
   - 使用 SELECT 語句對[SAQL 查詢](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md)進行採樣。 
   - **輸入預覽**：顯示來自輸入事件源的最新傳入資料的快照。  
     - 將自動檢測資料中的序列化類型 （JSON/CSV）。 您也可以手動將其更改為 JSON/CSV/AVRO。 
     - 您可以預覽表格式或原始格式的傳入資料。 
     - 如果顯示的資料不是最新的，請選擇 **"刷新"** 以查看最新事件。 
     - **選擇"選擇時間範圍**"以根據傳入事件的特定時間範圍測試查詢。 
     - 選擇 **"上傳示例輸入**"以通過上傳示例 JSON/CSV 檔來測試查詢。 有關測試 SAQL 查詢的詳細資訊，請參閱[使用示例資料測試 Azure 流分析作業](../stream-analytics/stream-analytics-test-query.md)。 

    ![測試查詢](media/sql-database-stream-analytics/test-query.png)


   - **測試結果**：選擇**測試查詢**，您可以看到流式處理查詢的結果 

    ![測試結果](media/sql-database-stream-analytics/test-results.png)

   - **測試結果架構**：在測試後顯示流式處理查詢結果的架構。 確保測試結果架構與您的輸出架構匹配。 

    ![測試結果架構](media/sql-database-stream-analytics/test-results-schema.png)


   - **輸出架構**：它包含您在步驟 5（新或現有）中選擇的表的架構。
     - 創建新：如果在步驟 5 中選擇此選項，則在開始流式處理作業之前，您還看不到架構。 創建新表時，請選擇相應的表索引。 有關表索引的詳細資訊，請參閱[描述群集和非群集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)。
    - 使用現有：如果在步驟 5 中選擇此選項，您將看到所選表的架構。 
 
7. 完成&測試查詢後，選擇 **"保存查詢**"。 選擇 **"開始流分析"作業**，開始將轉換後的資料引入 SQL 表。 完成以下欄位後，**啟動**作業。 
   - **輸出開始時間**：這定義作業的第一次輸出的時間。  
     - 現在：作業將立即開始並處理新的傳入資料。
     - 自訂：作業現在開始，但將從特定時間點（可以是過去或將來）處理資料。 有關詳細資訊，請參閱[如何啟動 Azure 流分析作業](../stream-analytics/start-job.md)。
   - **流式處理單元**：Azure 流分析按將資料處理到服務所需的流單元數定價。 有關詳細資訊，請參閱[Azure 流分析定價](https://azure.microsoft.com/pricing/details/stream-analytics/)。 
   - **輸出資料錯誤處理**：  
     - 重試：發生錯誤時，Azure 流分析會無限期地重試寫入事件，直到寫入成功。 重試不會逾時。 最終所有後續事件都會遭到正在重試的事件封鎖，而無法處理。 此選項是預設輸出錯誤處理原則。 
     - 刪除：Azure 流分析將刪除導致資料轉換錯誤的任何輸出事件。 無法復原卸除的事件以供稍後重新處理。 無論輸出錯誤處理原則組態為何，所有暫時性錯誤 (例如網路錯誤) 都會重試。 
   - **SQL 資料庫輸出設置**：用於繼承上一個查詢步驟的分區方案的選項，用於啟用具有多個編寫器的完全並行拓撲到表。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)。
   - **最大批次處理計數**：建議對隨每個批量插入事務發送的記錄數的上限。  
    有關輸出錯誤處理的詳細資訊，請參閱[Azure 流分析中的輸出錯誤策略](../stream-analytics/stream-analytics-output-error-policy.md)。  

    ![開始作業](media/sql-database-stream-analytics/start-job.png)

8. 啟動作業後，您將在清單中看到"運行"作業，並可以執行以下操作： 
   - **開始/停止作業**：如果作業正在運行，則可以停止作業。 如果作業已停止，則可以啟動該作業。 
   - **編輯作業**：您可以編輯查詢。 如果要對作業執行更多更改，則添加更多輸入/輸出，然後在流分析中打開作業。 作業運行時禁用編輯選項。 
   - **預覽輸出表**：您可以在 SQL 查詢編輯器中預覽表。 
   - **在流分析中打開**：在流分析服務中打開作業以查看監視、調試作業的詳細資訊。 


    ![流分析作業](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>後續步驟

- [Azure 串流分析文件](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure 串流分析解決方案模式](../stream-analytics/stream-analytics-solution-patterns.md)
