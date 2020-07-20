---
title: 使用樣本資料測試 Azure 串流分析作業
description: 本文說明如何使用 Azure 入口網站測試 Azure 串流分析作業、輸入取樣，以及上傳樣本資料。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 04d86b0ef8202c1c544524dd2a7331d521245f12
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043950"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>在入口網站中測試 Azure 串流分析作業

在 Azure 串流分析中，您可以測試查詢，而不需要啟動或停止您的作業。 您可以針對串流來源的傳入資料測試查詢，或從 Azure 入口網站上的本機檔案上傳範例資料。 您也可以從本機範例資料或[Visual Studio](stream-analytics-live-data-local-testing.md)和[Visual Studio Code](visual-studio-code-local-run-live-input.md)中的即時資料，在本機測試查詢。

## <a name="automatically-sample-incoming-data-from-input"></a>自動從輸入取樣傳入資料

Azure 串流分析會自動從您的串流輸入提取事件。 您可以針對預設範例執行查詢，或設定範例的特定時間範圍。

1. 登入 Azure 入口網站。

2. 找出並選取現有的串流分析作業。

3. 在 [串流分析作業] 頁面上，[工作拓撲]**** 標題下方，選取 [查詢]**** 開啟 [查詢] 編輯器視窗。 

4. 若要查看傳入事件的範例清單，請選取 [具有檔案的輸入] 圖示，範例事件就會自動出現在**輸入預覽**中。

   a. 如果您的資料的 JSON 或 CSV，則會自動偵測到其序列化類型。 您也可以藉由變更下拉式功能表中的選項，手動將它變更為 JSON、CSV、AVRO。
    
   b. 使用選取器來以**資料表**或**原始**格式來查看您的資料。
    
   c. 如果顯示的資料不是最新的，**請選取 [** 重新整理] 以查看最新的事件。

   下表是**資料表格式**的資料範例：

   ![Azure 串流分析資料表格式輸入範例](./media/stream-analytics-test-query/asa-sample-table.png)

   下表是**原始格式**的資料範例：

   ![以原始格式的 Azure 串流分析範例輸入](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 若要使用傳入的資料測試查詢，請選取 [**測試查詢**]。 結果會出現在 [**測試結果**] 索引標籤中。您也可以選取 [**下載結果**] 來下載結果。

   ![Azure 串流分析範例測試查詢結果](./media/stream-analytics-test-query/asa-test-query.png)

6. 若要針對傳入事件的特定時間範圍測試查詢，請選取 [**選取時間範圍**]。
   
   ![傳入範例事件的 Azure 串流分析時間範圍](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 設定您想要用來測試查詢的事件時間範圍，然後選取 [**範例**]。 在該時間範圍內，您最多可以抓取1000個事件或 1 MB，視何者先發生。

   ![Azure 串流分析設定傳入範例事件的時間範圍](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 一旦針對選取的時間範圍取樣事件之後，它們就會出現在 [**輸入預覽**] 索引標籤中。

   ![Azure 串流分析 view 測試結果](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 選取 [**重設**] 以查看傳入事件的範例清單。 如果您選取 [**重設**]，將會遺失您的時間範圍選取專案。 選取 [**測試查詢**] 以測試您的查詢，並在 [**測試結果**] 索引標籤中查看結果。

10. 當您對查詢進行變更時，請選取 [**儲存查詢**] 來測試新的查詢邏輯。 這可讓您反復修改查詢並再次測試，以查看輸出如何變更。

11. 確認瀏覽器中顯示的結果之後，您就可以**開始**工作。

## <a name="upload-sample-data-from-a-local-file"></a>從本機檔案上傳範例資料

您可以使用本機檔案中的範例資料來測試您的 Azure 串流分析查詢，而不是使用即時資料。

1. 登入 Azure 入口網站。
   
2. 找到現有的串流分析作業並加以選取。

3. 在 [串流分析作業] 頁面上，[工作拓撲]**** 標題下方，選取 [查詢]**** 開啟 [查詢] 編輯器視窗。

4. 若要使用本機檔案測試查詢，請選取 [**輸入預覽**] 索引標籤上的 **[上傳範例輸入**]。 

   ![Azure 串流分析上傳範例檔案](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 上傳您的本機檔案來測試查詢。 您只能上傳具有 JSON、CSV 或 AVRO 格式的檔案。 選取 [確定]。

   ![Azure 串流分析上傳範例檔案](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 當您上傳檔案時，您也可以將表單中的檔案內容視為資料表或其原始格式來查看。 如果您選取 [**重設**]，範例資料會回到上一節中所述的傳入輸入資料。 您可以隨時上傳任何其他檔案來測試查詢。

7. 選取 [**測試查詢**]，針對上傳的範例檔案測試您的查詢。

8. 測試結果會根據您的查詢來顯示。 您可以變更查詢，然後選取 [**儲存查詢**] 來測試新的查詢邏輯。 這可讓您反復修改查詢並再次測試，以查看輸出如何變更。

9. 當您在查詢中使用多個輸出時，會根據選取的輸出來顯示結果。 

   ![Azure 串流分析選取的輸出](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 確認瀏覽器中顯示的結果之後，您就可以**開始**工作。

## <a name="next-steps"></a>後續步驟
* [使用串流分析建立 IoT 解決方案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)：本教學課程將引導您建立端對端解決方案，其中包含的資料產生器會模擬收費亭中的流量。

* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [一般串流分析使用模式的查詢範例](stream-analytics-stream-analytics-query-patterns.md)

* [了解 Azure 串流分析的輸入](stream-analytics-add-inputs.md)

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)
