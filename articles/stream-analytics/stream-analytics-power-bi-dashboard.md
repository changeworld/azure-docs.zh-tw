---
title: Azure 串流分析的 Power BI 儀表板整合
description: 本文說明如何使用即時 Power BI 儀表板，以視覺化方式檢視 Azure 串流分析作業中的資料。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 4e3f31442c5fa645e27a640d8facf86aed20aa75
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842476"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>串流分析及 Power BI：適用於串流資料的即時分析儀表板

Azure 串流分析可讓您使用其中一個頂尖的商業智慧工具：[Microsoft Power BI](https://powerbi.com/)。 在本文中，您將了解如何使用 Power BI 作為 Azure 串流分析作業的輸出，以建立商業智慧工具。 您也會瞭解如何建立和使用即時儀表板，此儀表板會持續由串流分析作業進行更新。

本文是延續串流分析[即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)教學課程。 本文以該教學課程所建立的工作流程為基礎，並新增 Power BI 輸出，讓您視覺化串流分析作業所偵測到的詐騙電話。 

您可以觀看此情節的[影片](https://www.youtube.com/watch?v=SGUpT-a99MA)。


## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您具有下列項目：

* 一個 Azure 帳戶。
* Power BI Pro 的帳戶。 您可以使用公司帳戶或學校帳戶。
* [即時詐騙偵測](stream-analytics-real-time-fraud-detection.md)教學課程的完整版本。 本教學課程包含可產生虛構電話中繼資料的應用程式。 在本教學課程中，您將會建立事件中樞，並將串流通話資料傳送到事件中樞。 您將會撰寫查詢來偵測詐騙電話 (相同號碼在同一時間從不同位置的來電)。 


## <a name="add-power-bi-output"></a>新增 Power BI 輸出
在即時詐騙偵測教學課程中，輸出會傳送至 Azure Blob 儲存體。 在本節中，您會新增輸出將資訊傳送至 Power BI。

1. 在 Azure 入口網站中，開啟您稍早建立的串流分析作業。 如果您使用建議的名稱，則作業的名稱為 `sa_frauddetection_job_demo`。

2. 在左側功能表上，選取 [作業拓撲] 底下的 [輸出]。 然後，選取 [+ 新增]，接著從下拉式功能表中選擇 [Power BI]。

3. 選取 [+ 新增] > [Power BI]。 然後，在表單中填入下列詳細資料，然後選取 [授權] 以使用您自己的使用者 **身分** 識別來連線至 Power BI (權杖的有效時間為90天) 。 

>[!NOTE]
>針對生產作業，建議您連線以 [使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI](./powerbi-output-managed-identity.md)。

   |**設定**  |**建議的值**  |
   |---------|---------|
   |輸出別名  |  CallStream-PowerBI  |
   |資料集名稱  |   sa-dataset  |
   |資料表名稱 |  fraudulent-calls  |

   ![設定串流分析輸出](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > 如果 Power BI 的資料集和資料表名稱與您在串流分析作業中所指定的名稱相同，則會覆寫現有的資料集和資料表。
   > 我們不建議您在 Power BI 帳戶中明確建立此資料集和資料表。 當您啟動串流分析作業，而該作業開始將輸出提取至 Power BI 時，系統會自動建立資料集和資料表。 如果作業查詢沒有傳回任何結果，則不會建立資料集和資料表。
   >

4. 當您選取 [授權] 時，快顯視窗隨即開啟，而系統會要求您提供認證來驗證您的 Power BI 帳戶。 一旦授權成功，請 [儲存] 設定。

8. 按一下頁面底部的 [新增] 。

系統會使用下列設定來建立資料集：

* **defaultRetentionPolicy:BasicFIFO** - 資料為 FIFO，具有最多 200,000 個資料列。
* **defaultMode：混合** 式-資料集支援串流磚 (也稱為推送) 和傳統報表型視覺效果。 針對推送內容，在此情況下，資料會從串流分析作業持續更新，而不需要從 Power BI 端排程重新整理。

您目前無法建立具有其他旗標的資料集。

如需 Power BI 資料集的詳細資訊，請參閱 [Power BI REST API](/rest/api/power-bi/) 參考。


## <a name="write-the-query"></a>撰寫查詢

1. 關閉 [輸出] 刀鋒視窗，返回作業刀鋒視窗。

2. 按一下 [查詢] 方塊。 

3. 輸入下列查詢。 此查詢類似於您在詐騙偵測教學課程中建立的自我聯結查詢。 差別在於此查詢會將結果傳送至您所建立的新輸出 (`CallStream-PowerBI`)。 

    >[!NOTE]
    >如果您在詐騙偵測教學課程中不是將輸入命名為 `CallStream`，請在查詢的 **FROM** 和 **JOIN** 子句中，用您的名稱取代 `CallStream`。

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. 按一下 [檔案] 。


## <a name="test-the-query"></a>測試查詢

此為選擇性區段，但建議執行。 

1. 如果目前沒有執行 TelcoStreaming 應用程式，請依照下列步驟啟動它：

    * 開啟命令提示字元。
    * 移至 telcogenerator.exe 和修改的 telcodatagen.exe.config 檔案所在的資料夾。
    * 執行以下命令：

       `telcodatagen.exe 1000 .2 2`

2. 在串流分析作業的 [查詢] 窗格上，按一下 `CallStream` 輸入旁邊的點，然後選取 [來自輸入的範例資料]。

3. 指定您想要取得三分鐘的資料，然後按一下 [確定]。 等待資料已取樣的通知。

4. 按一下 [測試] 並檢閱結果。

## <a name="run-the-job"></a>執行作業

1. 請確定 TelcoStreaming 應用程式正在執行。

2. 瀏覽至串流分析作業 的 [概觀] 頁面，並選取 [啟動]。

    ![啟動串流分析工作](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

串流分析作業會開始在傳入資料流中尋找詐騙電話。 此作業也會在 Power BI 中建立資料集和資料表，並開始將詐騙電話的相關資料傳送至該資料集和資料表。


## <a name="create-the-dashboard-in-power-bi"></a>在 Power BI 中建立儀表板

1. 移至 [Powerbi.com](https://powerbi.com)，然後使用公司或學校帳戶登入。 如果串流分析作業查詢有輸出結果，您會看到資料集已建立：

    ![Power BI 中的串流資料集位置](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. 在工作區中，按一下 [+&nbsp;建立]。

    ![Power BI 工作區中的 [建立] 按鈕](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. 建立新的儀表板並命名為 `Fraudulent Calls`。

    ![在 Power BI 工作區中建立並命名儀表板](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. 在視窗的頂端，按一下 [新增磚]，選取 [自訂串流資料]，然後按 [下一步]。

    ![Power BI 中的自訂串流資料集磚](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. 在 [您的資料集] 底下，選取資料集，然後按 [下一步]。

    ![您在 Power BI 中的串流資料集](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. 在 [視覺效果類型] 底下，選取 [卡]，然後在 [欄位] 清單中，選取 [fraudulentcalls]。

    ![新磚的視覺效果詳細資料](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. 按 [下一步] 。

8. 填寫磚詳細資料，例如標題和副標題。

    ![新磚的標題和副標題](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. 按一下 [套用]。

    您現在有一個詐騙計數器了！

    ![Power BI 儀表板中的詐騙計數器](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. 再次依照步驟來新增磚 (從步驟 4 開始)。 這次請執行下列動作：

    * 在 [視覺效果類型] 中，選取 [折線圖]。 
    * 新增軸並選取 [windowend]。 
    * 新增值並選取 [fraudulentcalls]。
    * 在 [要顯示的時間範圍] 中，選取過去 10 分鐘。

      ![在 Power BI 中建立適用於折線圖的磚](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. 按 [下一步]，新增標題和副標題，然後按一下 [套用]。

     Power BI 儀表板現在提供兩個檢視，讓您看到串流資料中偵測到之詐騙電話的相關資料。

     ![完成的 Power BI 儀表板，以兩個磚來顯示詐騙電話](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)

## <a name="learn-about-limitations-and-best-practices"></a>了解限制和最佳作法
目前來說，系統大約每秒可呼叫 Power BI 一次。 串流視覺效果支援 15 KB 的封包。 超過 15 KB，串流視覺效果就會失敗 (但仍會繼續推送)。 由於有這些限制，Power BI 最適用的自然是 Azure 串流分析會大量降低資料載入的案例。 我們建議使用輪轉視窗或跳動視窗，以確保資料推送最多為每秒推送 1 次，而且您的查詢符合輸送量需求。

您可以使用下列方程式，以秒為單位計算要提供給視窗的值：

![以秒為單位計算要提供給視窗之值的方程式](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

例如：

* 您有 1 千個以一秒間隔傳送資料的裝置。
* 您使用的 Power BI Pro SKU 支援每小時 100 萬個資料列。
* 您想要讓每個裝置將平均資料量發佈至 Power BI。

因此，方程式會變成︰

![以範例準則為依據的方程式](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

根據此設定，您可以將原始查詢變更如下︰

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>更新授權
如果自從建立作業或上次驗證之後已變更密碼，則您需要重新驗證 Power BI 帳戶。 如果 Azure AD Multi-Factor Authentication 設定于 Azure Active Directory (Azure AD) 租使用者，您也需要每兩周更新 Power BI 授權。 如果未更新，作業記錄中會出現一些徵兆，例如缺乏作業輸出或 `Authenticate user error`。

同樣地，如果作業在權杖過期後啟動，則會發生錯誤，作業會失敗。 若要解決這個問題，請停止執行作業並移至 Power BI 輸出。 若要避免資料遺失，請選取 [更新授權] 連結，然後從 [上次停止時間] 重新啟動作業。

Power BI 在重新整理過授權後，授權區域就會出現綠色警示，表示問題已獲得解決。

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [串流分析輸出](stream-analytics-define-outputs.md)
* [Azure 串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)
* [使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI](./powerbi-output-managed-identity.md)
