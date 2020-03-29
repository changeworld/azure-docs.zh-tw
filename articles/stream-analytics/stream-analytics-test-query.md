---
title: 使用樣本資料測試 Azure 串流分析作業
description: 本文說明如何使用 Azure 入口網站測試 Azure 串流分析作業、輸入取樣，以及上傳樣本資料。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898394"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>在門戶中測試 Azure 流分析作業

在 Azure 流分析中，無需啟動或停止作業即可測試查詢。 您可以測試來自流源的傳入資料的查詢，或者從 Azure 門戶上的本地檔上載示例資料。 您還可以在[視覺化工作室](stream-analytics-live-data-local-testing.md)和[視覺化工作室代碼](visual-studio-code-local-run-live-input.md)中從本地示例資料或即時資料中本地測試查詢。

## <a name="automatically-sample-incoming-data-from-input"></a>自動從輸入中採樣傳入資料

Azure 流分析會自動從流輸入提取事件。 您可以在預設示例中執行查詢，也可以為示例設置特定的時間範圍。

1. 登入 Azure 入口網站。

2. 查找並選擇現有的流分析作業。

3. 在 [串流分析作業] 頁面上，[工作拓撲]**** 標題下方，選取 [查詢]**** 開啟 [查詢] 編輯器視窗。 

4. 要查看傳入事件的示例清單，請選擇帶有檔圖示的輸入，示例事件將自動顯示在 **"輸入預覽"** 中。

   a. 如果資料的 JSON 或 CSV，將自動檢測到資料的序列化類型。 您可以通過更改下拉式功能表中的選項手動將其更改為 JSON、CSV 和 AVRO。
    
   b. 使用選擇器以**表格**或**原始**格式查看資料。
    
   c. 如果顯示的資料不是最新的，請選擇 **"刷新"** 以查看最新事件。

   下表是**表格式**的資料示例：

   ![Azure 流分析示例輸入（表格式）](./media/stream-analytics-test-query/asa-sample-table.png)

   下表是**原始格式**的資料示例：

   ![Azure 流分析示例輸入（原始格式）](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 要使用傳入資料測試查詢，請選擇 **"測試查詢**"。 結果將顯示在 **"測試結果"** 選項卡中。您還可以選擇 **"下載結果**"來下載結果。

   ![Azure 流分析示例測試查詢結果](./media/stream-analytics-test-query/asa-test-query.png)

6. 要根據傳入事件的特定時間範圍測試查詢，請選擇 **"選擇時間範圍**"。
   
   ![傳入示例事件的 Azure 流分析時間範圍](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 設置要用於測試查詢的事件的時間範圍，並選擇 **"示例**"。 在該時間範圍內，您可以檢索多達 1000 個事件或 1 MB（以先到者為准）。

   ![Azure 流分析為傳入示例事件設置時間範圍](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 為所選時間範圍對事件進行採樣後，它們將顯示在 **"輸入預覽"** 選項卡中。

   ![Azure 流分析查看測試結果](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 選擇 **"重置"** 以查看傳入事件的示例清單。 如果選擇 **"重置"，** 則時間範圍選擇將丟失。 選擇 **"測試"查詢**以測試查詢並查看 **"測試結果"** 選項卡中的結果。

10. 更改查詢時，選擇 **"保存"查詢**以測試新的查詢邏輯。 這允許您反覆運算修改查詢並再次測試，以查看輸出如何更改。

11. 驗證瀏覽器中顯示的結果後，即可**開始**作業。

## <a name="upload-sample-data-from-a-local-file"></a>從本地檔上傳示例資料

您可以使用本地檔的示例資料來測試 Azure 流分析查詢，而不是使用即時資料。

1. 登入 Azure 入口網站。
   
2. 找到現有的串流分析作業並加以選取。

3. 在 [串流分析作業] 頁面上，[工作拓撲]**** 標題下方，選取 [查詢]**** 開啟 [查詢] 編輯器視窗。

4. 要使用本地檔測試查詢，請在 **"輸入預覽"** 選項卡上選擇 **"上載示例輸入**"。 

   ![Azure 流分析上載示例檔](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 上傳本地檔以測試查詢。 您只能上傳具有 JSON、CSV 或 AVRO 格式的檔。 選取 [確定]****。

   ![Azure 流分析上載示例檔](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 上傳檔後，您還可以將表單中的檔內容視為表或原始格式。 如果選擇 **"重置**"，則示例資料將返回到上一節中解釋的傳入輸入資料。 您可以隨時上載任何其他檔以測試查詢。

7. 選擇 **"測試"查詢**以根據上載的示例檔測試查詢。

8. 測試結果將根據您的查詢顯示。 您可以更改查詢並選擇 **"保存"查詢**以測試新的查詢邏輯。 這允許您反覆運算修改查詢並再次測試，以查看輸出如何更改。

9. 在查詢中使用多個輸出時，結果將基於所選輸出顯示。 

   ![Azure 流分析所選輸出](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 驗證瀏覽器中顯示的結果後，可以**啟動**作業。

## <a name="next-steps"></a>後續步驟
* [使用流分析構建 IoT 解決方案](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)：本教程將指導您使用資料產生器構建端到端解決方案，該資料產生器將類比收費亭的流量。

* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [一般串流分析使用模式的查詢範例](stream-analytics-stream-analytics-query-patterns.md)

* [了解 Azure 串流分析的輸入](stream-analytics-add-inputs.md)

* [了解來自 Azure 串流分析的輸出](stream-analytics-define-outputs.md)
