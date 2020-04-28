---
title: 使用串流分析從事件中樞 Azure 處理資料 |Microsoft Docs
description: 本文說明如何使用 Azure 串流分析工作處理來自 Azure 事件中樞的資料。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "69991937"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>使用 Azure 串流分析處理來自事件中樞的資料 
Azure 串流分析服務可讓您輕鬆地從 Azure 事件中樞內嵌、處理和分析串流資料，並提供強大的深入解析來驅動即時動作。 此整合可讓您快速建立熱路徑分析管線。 您可以使用 Azure 入口網站將傳入的資料視覺化，並撰寫串流分析的查詢。 準備好您的查詢之後，只要按幾下滑鼠就可以將它移到生產環境中。 

## <a name="key-benefits"></a>主要權益
以下是 Azure 事件中樞和 Azure 串流分析整合的主要優點： 
- **預覽資料**–您可以在 Azure 入口網站中預覽來自事件中樞的傳入資料。
- **測試查詢**–準備轉換查詢，並直接在 Azure 入口網站中進行測試。 如需查詢語言語法，請參閱[串流分析查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)檔。
- 將**您的查詢部署到生產環境**–您可以藉由建立和啟動 Azure 串流分析作業，將查詢部署到生產環境中。

## <a name="end-to-end-flow"></a>端對端流程

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的**事件中樞命名空間**，然後流覽至具有傳入資料的**事件中樞**。 
1. 選取 [事件中樞] 頁面上的 [**處理資料**]。  

    ![處理資料磚](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. 選取 [**啟用事件的即時深入**解析] 磚上的 [**流覽**]。 

    ![選取串流分析](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 您會看到查詢頁面，其中已針對下欄欄位設定值：
    1. **事件中樞**做為查詢的輸入。
    1. 使用 SELECT 語句的範例**SQL 查詢**。 
    1. 用來參考查詢測試結果的**輸出**別名。 

        ![查詢編輯器](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  當您第一次使用此功能時，此頁面會要求您建立取用者群組的許可權，以及您的事件中樞用來預覽傳入資料的原則。
1. 在 [**輸入預覽**] 窗格中選取 [**建立**]，如上圖所示。 
1. 您會立即在此索引標籤中看到最新傳入資料的快照集。
    - 系統會自動偵測您資料中的序列化類型（JSON/CSV）。 您也可以手動將它變更為 JSON/CSV/AVRO。
    - 您可以預覽資料表格式或原始格式的傳入資料。 
    - 如果顯示的資料不是最新的，**請選取 [** 重新整理] 以查看最新的事件。 

        以下是**資料表格式**的資料範例： ![產生資料表格式](./media/process-data-azure-stream-analytics/snapshot-results.png)

        以下是**原始格式**的資料範例： 

        ![以原始格式產生的結果](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 選取 [**測試查詢**]，即可在 [**測試結果**] 索引標籤中查看查詢的測試結果快照集。您也可以下載結果。

    ![測試查詢結果](./media/process-data-azure-stream-analytics/test-results.png)
1. 撰寫您自己的查詢來轉換資料。 請參閱[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)。
1. 當您測試過查詢，而且想要將它移至生產環境時，請選取 [**部署查詢**]。 若要部署查詢，請建立 Azure 串流分析作業，您可以在其中設定作業的輸出，並啟動作業。 若要建立串流分析作業，請指定作業的名稱，然後選取 [**建立**]。

      ![建立 Azure 串流分析作業](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  我們建議您針對每個從 [事件中樞] 頁面建立的新 Azure 串流分析作業，建立取用者群組和原則。 取用者群組只允許五個並行讀取器，因此為每項作業提供專用的取用者群組，將可避免因超出該限制而引發的任何錯誤。 專用原則可讓您旋轉金鑰或撤銷許可權，而不會影響其他資源。 
1. 現在會建立您的串流分析作業，其中您的查詢與您所測試的相同，而輸入則是您的事件中樞。 

9.  若要完成管線，請設定查詢的**輸出**，然後選取 [**啟動**] 以啟動作業。

    > [!NOTE]
    > 開始工作之前，別忘了將 outputalias 取代為您在 Azure 串流分析中建立的輸出名稱。

      ![設定輸出並啟動作業](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>已知限制
測試查詢時，測試結果大約需要6秒的時間來載入。 我們正努力改善測試的效能。 不過，在生產環境中部署時，Azure 串流分析會有次秒延遲。

## <a name="streaming-units"></a>串流單位
您的 Azure 串流分析作業預設為三個串流單位（su）。 若要調整這項設定，請在 Azure 入口網站的 [**串流分析作業**] 頁面中，選取左側功能表上的 [**調整**]。 若要深入瞭解串流處理單位，請參閱[瞭解和調整串流單位](../stream-analytics/stream-analytics-streaming-unit-consumption.md)。

![調整串流單位](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>後續步驟
若要深入瞭解串流分析查詢，請參閱[串流分析查詢語言](/stream-analytics-query/built-in-functions-azure-stream-analytics)
