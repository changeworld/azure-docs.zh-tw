---
title: 使用 Azure 串流分析整合 (預覽) 來串流資料
description: 使用 Azure 串流分析整合，將資料串流至 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454017"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>使用 Azure 串流分析整合 (預覽版) 將資料串流至 Azure SQL Database

使用者現在可以直接從 Azure SQL Database 中的資料庫內嵌、處理、查看及分析即時串流資料到資料表中。 他們會在使用 [Azure 串流分析](../../stream-analytics/stream-analytics-introduction.md)的 Azure 入口網站中這麼做。 這項體驗可提供各種不同的案例，例如連線的汽車、遠端監視、詐騙偵測等等。 在 Azure 入口網站中，您可以選取事件中樞/IoT 中樞) 的事件來源 (、查看傳入的即時事件，然後選取要儲存事件的資料表。 您也可以在入口網站中撰寫 Azure 串流分析查詢語言查詢，以轉換傳入的事件，並將其儲存在選取的資料表中。 這個新的進入點是除了串流分析中已存在的建立和設定體驗。 這項體驗從資料庫的內容開始，讓您能夠快速設定串流分析作業，並在 Azure SQL Database 和串流分析體驗的資料庫之間順暢地流覽。

![串流分析流程](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>主要權益

- 最小內容切換：您可以從入口網站 Azure SQL Database 中的資料庫啟動，並開始將即時資料擷取至資料表，而不需要切換至任何其他服務。
- 減少的步驟數：您的資料庫和資料表的內容會用來預先設定串流分析作業。
- 更輕鬆地使用預覽資料：預覽所選資料表內容中事件來源 (事件中樞/IoT 中樞) 的傳入資料

> [!IMPORTANT]
> Azure 串流分析作業可以輸出至 Azure SQL Database、Azure SQL 受控執行個體或 Azure Synapse Analytics。 如需詳細資訊，請參閱 [輸出](../../stream-analytics/stream-analytics-define-outputs.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文中的步驟，您需要下列資源︰

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。
- Azure SQL Database 中的資料庫。 如需詳細資訊，請參閱 [在 Azure SQL Database 中建立單一資料庫](single-database-create-quickstart.md)。
- 防火牆規則，可讓您的電腦連接到伺服器。 如需詳細資訊，請參閱 [建立伺服器層級防火牆規則](firewall-create-server-level-portal-quickstart.md)。

## <a name="configure-stream-analytics-integration"></a>設定串流分析整合

1. 登入 Azure 入口網站。
2. 流覽至您要內嵌串流資料的資料庫。 選取 [ **串流分析] (預覽)**。

    ![串流分析](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. 若要開始將串流資料擷取到這個資料庫，請選取 [ **建立** ]，並將名稱提供給您的串流作業，然後選取 **[下一步：輸入]**。

    ![設定串流分析作業的基本概念](./media/stream-data-stream-analytics-integration/create-job.png)

4. 輸入您的事件來源詳細資料，然後選取 **[下一步：輸出]**。

   - **輸入類型**：事件中樞/IoT 中樞
   - **輸入別名**：輸入用來識別事件來源的名稱
   - **訂** 用帳戶：與 Azure SQL Database 訂用帳戶相同
   - **事件中樞命名空間**：命名空間的名稱
   - **事件中樞名稱**：所選命名空間內的事件中樞名稱
   - **事件中樞原則名稱** (預設為建立新的) ：提供原則名稱
   - **事件中樞取用者群組** (預設為建立新的) ：提供取用者組名  

      建議您為您在這裡建立的每個新 Azure 串流分析作業建立取用者群組和原則。 取用者群組只允許五個並行讀取器，因此為每個工作提供專用的取用者群組，將可避免因超過該限制而產生的任何錯誤。 專用原則可讓您輪替金鑰或撤銷許可權，而不會影響其他資源。

     ![設定串流分析作業輸出](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. 選取您要內嵌串流資料的資料表。 完成之後，請選取 [ **建立**]。

   - 使用者 **名稱**、**密碼**：輸入您的認證以進行 SQL server 驗證。 選取 [驗證]。
   - **資料表**：選取 [ **建立新** 的] 或 [ **使用現有** 的]。 在此流程中，讓我們選取 [ **建立**]。 當您啟動串流分析作業時，這會建立新的資料表。

     ![建立串流分析作業](./media/stream-data-stream-analytics-integration/create.png)

6. [查詢] 頁面隨即開啟，其中包含下列詳細資料：

   - 您的 **輸入** (輸入事件來源) 您將從中內嵌資料  
   - 輸出 **(輸出** 資料表) 會儲存已轉換的資料
   - 使用 SELECT 語句的 [SAQL 查詢](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) 範例。
   - **輸入預覽**：顯示輸入事件來源中最新傳入資料的快照集。
     -  (JSON/CSV) 會自動偵測資料中的序列化類型。 您也可以手動將它變更為 JSON/CSV/AVRO。
     - 您可以預覽資料表格式或原始格式的傳入資料。
     - 如果顯示的資料不是最新的， **請選取 [** 重新整理] 以查看最新的事件。
     - 選取 [ **選取時間範圍** ]，針對傳入事件的特定時間範圍測試您的查詢。
     - 選取 **[上傳範例輸入** ]，藉由上傳範例 JSON/CSV 檔案來測試您的查詢。 如需有關測試 SAQL 查詢的詳細資訊，請參閱 [使用範例資料測試 Azure 串流分析作業](../../stream-analytics/stream-analytics-test-query.md)。

     ![測試查詢](./media/stream-data-stream-analytics-integration/test-query.png)

   - **測試結果**：選取 **測試查詢** ，您可以看到串流查詢的結果

     ![測試結果](./media/stream-data-stream-analytics-integration/test-results.png)

   - **測試結果架構**：顯示測試後串流查詢結果的架構。 請確定測試結果架構符合您的輸出架構。

     ![測試結果架構](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **輸出架構**：這包含您在步驟5中選取的資料表架構 (新的或現有的) 。

      - 建立新的：如果您已在步驟5中選取此選項，在您啟動串流作業之前，您將不會看到架構。 建立新的資料表時，請選取適當的資料表索引。 如需資料表索引編制的詳細資訊，請參閱 [所述的叢集和非叢集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)。
      - 使用現有的：如果您在步驟5中選取此選項，您會看到所選資料表的架構。

7. 完成撰寫 & 測試查詢之後，請選取 [ **儲存查詢**]。 選取 [ **啟動串流分析作業** ]，開始將已轉換的資料擷取到 SQL 資料表中。 完成下欄欄位之後，請 **啟動** 作業。
   - **輸出開始時間**：這會定義作業第一個輸出的時間。  
     - 現在：作業會立即啟動並處理新的傳入資料。
     - 自訂：作業現在將會啟動，但會處理來自特定時間點 (的資料，這些資料可能會在過去或未來的) 。 如需詳細資訊，請參閱 [如何啟動 Azure 串流分析作業](../../stream-analytics/start-job.md)。
   - **串流處理單位**： Azure 串流分析是依處理資料至服務所需的串流處理單位數計價。 如需詳細資訊，請參閱 [Azure 串流分析定價](https://azure.microsoft.com/pricing/details/stream-analytics/)。
   - **輸出資料錯誤處理**：  
     - 重試：發生錯誤時，Azure 串流分析會無限期地重試寫入事件，直到寫入成功為止。 重試沒有任何超時時間。 最終所有後續事件都會遭到正在重試的事件封鎖，而無法處理。 此選項是預設輸出錯誤處理原則。
     - Drop： Azure 串流分析會卸載任何導致資料轉換錯誤的輸出事件。 捨棄的事件無法復原以便稍後重新處理。 無論輸出錯誤處理原則組態為何，所有暫時性錯誤 (例如網路錯誤) 都會重試。
   - **SQL Database 輸出設定**：此選項可讓您繼承先前查詢步驟的資料分割配置，以啟用資料表的多個寫入器的完全平行拓朴。 如需詳細資訊，請參閱 [Azure 串流分析輸出至 Azure SQL Database](../../stream-analytics/stream-analytics-sql-output-perf.md)。
   - **最大批次計數**：建議在每次大量插入交易時傳送的記錄數目上限。  
    如需輸出錯誤處理的詳細資訊，請參閱 [Azure 串流分析中的輸出錯誤原則](../../stream-analytics/stream-analytics-output-error-policy.md)。  

     ![啟動作業](./media/stream-data-stream-analytics-integration/start-job.png)

8. 當您啟動作業時，您會在清單中看到正在執行的作業，而且您可以採取下列動作：
   - **啟動/停止作業**：如果作業正在執行中，您可以停止作業。 如果工作已停止，您可以啟動作業。
   - **編輯工作**：您可以編輯查詢。 如果您想要對作業進行更多變更，例如新增更多輸入/輸出，然後在串流分析中開啟作業。 當作業正在執行時，[編輯] 選項會停用。
   - **預覽輸出資料表**：您可以在 [SQL 查詢編輯器] 中預覽資料表。
   - **在串流分析中開啟**：開啟串流分析中的作業，以查看作業的監視、偵測詳細資料。

     ![串流分析作業](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>後續步驟

- [Azure 串流分析文件](../../stream-analytics/index.yml)
- [Azure 串流分析解決方案模式](../../stream-analytics/stream-analytics-solution-patterns.md)
