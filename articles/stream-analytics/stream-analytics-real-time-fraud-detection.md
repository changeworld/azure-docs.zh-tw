---
title: 教學課程 - 使用 Azure 串流分析來分析詐騙通話資料，並在 Power BI 儀表板中以視覺方式呈現結果
description: 本教學課程提供如何使用 Azure 串流分析來分析通話串流中詐騙電話的端對端實例。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 8e7a484ff968454f3c5b31422b87123dcee03726
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682889"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>教學課程：使用串流分析來分析詐騙通話資料，並在 Power BI 儀表板中以視覺方式呈現結果

本教學課程說明如何使用 Azure 串流分析來分析通話資料。 用戶端應用程式所產生的通話資料包含詐騙電話，而我們會以串流分析作業來篩選這類電話。 我們可以使用本教學課程中的技術，來進行其他類型的詐騙偵測，例如信用卡詐騙或身分盜用。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 產生範例通話資料並將其傳送至 Azure 事件中樞。
> * 建立串流分析作業。
> * 設定作業輸入和輸出。
> * 定義查詢以篩選詐騙電話。
> * 測試和啟動作業。
> * 在 Power BI 中將結果視覺化。

## <a name="prerequisites"></a>先決條件

開始之前，請先確定您已完成下列步驟：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/)。
* 從 Microsoft 下載中心下載通話事件產生器應用程式 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，或從 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 取得原始程式碼。
* 您將需要 Power BI 帳戶。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-an-azure-event-hub"></a>建立 Azure 事件中樞

資料必須先傳送至 Azure，串流分析才可以分析詐騙電話資料流。 在本教學課程中，您會使用 [Azure 事件中樞](../event-hubs/event-hubs-about.md)將資料傳送至 Azure。

請使用下列步驟來建立事件中樞，並將通話資料傳送至事件中樞：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [建立資源]   > [物聯網]   > [事件中樞]  。

   ![在入口網站中建立 Azure 事件中樞](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. 在 [建立命名空間]  窗格中填入下列值：

   |**設定**  |**建議的值** |**說明**  |
   |---------|---------|---------|
   |名稱     | asaTutorialEventHub        |  用以識別事件中樞命名空間的唯一名稱。       |
   |訂用帳戶     |   \<Your subscription\>      |   選取您要在其中建立事件中樞的 Azure 訂用帳戶。      |
   |資源群組     |   MyASADemoRG      |  選取 [新建]  ，然後為您的帳戶輸入新的資源群組名稱。       |
   |Location     |   美國西部 2      |    可以部署事件中樞命名空間的位置。     |

4. 對其餘的設定使用預設選項，並選取 [檢閱 + 建立]。 然後，選取 [建立] 以開始部署。

   ![在 Azure 入口網站中建立事件中樞命名空間](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. 在命名空間部署完成時，移至 [所有資源]，並在 Azure 資源清單中尋找 *asaTutorialEventHub*。 選取 *asaTutorialEventHub* 加以開啟。

6. 接著，選取 [+事件中樞]，並輸入事件中樞的 [名稱]。 將 [分割區計數] 設定為 2。  對其餘的設定使用預設選項，並選取 [建立]。 然後，等待部署完成。

   ![Azure 入口網站中的事件中樞組態](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>授權存取事件中樞並取得連接字串

事件中樞必須先具有允許存取權的原則，應用程式才能將資料傳送到 Azure 事件中樞。 存取原則會產生包含授權資訊的連接字串。

1. 瀏覽至您在上一個步驟中建立的事件中樞 *MyEventHub*。 在 [設定] 下方選取 [共用存取原則]，然後選取 [+ 新增]。

2. 將原則命名為 **MyPolicy**，並確定已核取 [管理]。 然後選取 [建立]。

   ![建立事件中樞共用存取原則](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. 在建立原則之後，選取原則名稱以開啟原則。 尋找 [連接字串–主要金鑰]。 選取連接字串旁的 [複製] 按鈕。

   ![儲存共用存取原則連接字串](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. 將連接字串貼到文字編輯器。 您在下一節中需要此連接字串。

   連接字串如下所示：

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   請注意，連接字串包含多個機碼值組，以分號分隔：**Endpoint**、**SharedAccessKeyName**、**SharedAccessKey** 和 **EntityPath**。

## <a name="start-the-event-generator-application"></a>啟動事件產生器應用程式

啟動 TelcoGenerator 應用程式之前，您應該先將它設定為將資料傳送到您先前建立的事件中樞。

1. 將 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 檔案的內容解壓縮。
2. 在您選擇的文字編輯器中開啟 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 檔案。有一個以上的 `.config` 檔案，請確實開啟正確的檔案。

3. 使用下列詳細資料更新組態檔中的 `<appSettings>` 元素：

   * 將 *EventHubName* 索引鍵的值設為連接字串中的 EntityPath 值。
   * 將 *Microsoft.ServiceBus.ConnectionString* 索引鍵的值設為連接字串，不含 EntityPath 值。 別忘了移除 EntityPath 值前面的分號。

4. 儲存檔案。

5. 接著，開啟命令視窗，並切換至解壓縮 TelcoGenerator 應用程式的資料夾。 然後，輸入下列命令：

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   此命令採用下列參數︰
   * 每小時的通話資料記錄筆數。
   * 詐騙機率的百分比，也就是應用程式模擬詐騙電話的頻率。 值 0.2 表示大約 20% 的通話記錄可能是詐騙。
   * 以小時為單位的持續時間，也就是應用程式應該執行的時數。 您也可以在命令列結束程序 (**Ctrl+C**)，隨時停止應用程式。

   幾秒之後，隨著應用程式將通話記錄傳送到事件中樞，它會開始在螢幕上顯示通話記錄。 通話資料包含下列欄位：

   |**記錄**  |**[定義]**  |
   |---------|---------|
   |CallrecTime    |  通話開始時間的時間戳記。       |
   |SwitchNum     |  用來接通電話的電話交換機。 在此範例中，交換機是代表發話國家/地區的字串 (美國、中國、英國、德國或澳大利亞)。       |
   |CallingNum     |  來電者的電話號碼。       |
   |CallingIMSI     |  國際行動用戶識別碼 (IMSI)。 這是來電者的唯一識別碼。       |
   |CalledNum     |   受話方的電話號碼。      |
   |CalledIMSI     |  國際行動用戶識別碼 (IMSI)。 這是受話方的唯一識別碼。       |

## <a name="create-a-stream-analytics-job"></a>建立串流分析作業

既然已取得通話事件的串流，您可以建立串流分析作業，以從事件中樞讀取資料。

1. 若要建立串流分析作業，請瀏覽至 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [建立資源]，並搜尋 [串流分析作業]。 選取 [串流分析作業] 圖格，然後選取 *[建立]**。

3. 在 [新增串流分析作業] 表單中填入下列值：

   |**設定**  |**建議的值**  |**說明**  |
   |---------|---------|---------|
   |作業名稱     |  ASATutorial       |   用以識別事件中樞命名空間的唯一名稱。      |
   |訂用帳戶    |  \<Your subscription\>   |   選取您要在其中建立作業的 Azure 訂用帳戶。       |
   |資源群組   |   MyASADemoRG      |   選取 [使用現有的]，然後為您的帳戶輸入新的資源群組名稱。      |
   |Location   |    美國西部 2     |      可以部署作業的位置。 建議將作業和事件中樞放在相同的區域，以達到最佳效能，在區域之間傳送資料也不需要付費。      |
   |裝載環境    | Cloud        |     串流分析作業可以部署到雲端或邊緣裝置。 雲端部分可讓您部署到 Azure 雲端，邊緣裝置部分可讓您部署到 IoT Edge 裝置。    |
   |串流單位     |    1       |      串流單位代表執行作業所需的計算資源。 根據預設，此值設定為 1。 若要深入了解如何調整串流單位，請參閱[了解與調整串流單位](stream-analytics-streaming-unit-consumption.md)一文。      |

4. 對其餘的設定使用預設選項，選取 [建立] 並等待部署成功。

   ![建立 Azure 串流分析作業](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>設定作業輸入

下一個步驟是使用您在上一節中建立的事件中樞定義作業的輸入來源，以讀取資料。

1. 從 Azure 入口網站開啟 [所有資源] 頁面，然後尋找 *ASATutorial* 串流分析作業。

2. 在串流分析作業的 [作業拓撲] 區段中，選取 [輸入]。

3. 選取 [+ 新增資料流輸入]，並選取 [事件中樞]。 在輸入表單中填入下列值：

   |**設定**  |**建議的值**  |**說明**  |
   |---------|---------|---------|
   |輸入別名     |  CallStream       |  提供可識別輸入的易記名稱。 輸入別名只可包含英數字元、連字號與底線，且其長度必須介於 3-63 個字元之間。       |
   |訂用帳戶    |   \<Your subscription\>      |   選取您在其中建立事件中樞的 Azure 訂用帳戶。 事件中樞可位於與串流分析作業相同或不同的訂用帳戶。       |
   |事件中樞命名空間    |  asaTutorialEventHub       |  選取您在上一節中建立的事件中樞命名空間。 您目前訂用帳戶中所有可用的事件中樞命名空間都會列在下拉式清單中。       |
   |事件中樞名稱    |   MyEventHub      |  選取您在上一節中建立的事件中樞。 您目前訂用帳戶中所有可用的事件中樞都會列在下拉式清單中。       |
   |事件中樞原則名稱   |  MyPolicy       |  選取您在上一節中建立的事件中樞共用存取原則。 您目前訂用帳戶中所有可用的事件中樞原則都會列在下拉式清單中。       |

4. 對其餘的設定使用預設選項，並選取 [儲存]。

   ![設定 Azure 串流分析輸入](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>設定作業輸出

最後一個步驟是定義作業的輸出接收，也就是可以寫入轉換後資料的位置。 在本教學課程中，您可以使用 Power BI 輸出及視覺化資料。

1. 從 Azure 入口網站開啟 [所有資源]，然後選取 *ASATutorial* 串流分析作業。

2. 在串流分析作業的 [作業拓撲] 區段中，選取 [輸出] 選項。

3. 選取 [+ 新增] > [Power BI]。 然後，選取 [授權]，並依照提示驗證 Power BI。

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Power BI 的授權按鈕":::

4. 在輸出表單中填入下列詳細資料，然後選取 [儲存]：

   |**設定**  |**建議的值**  |
   |---------|---------|
   |輸出別名  |  MyPBIoutput  |
   |群組工作區| 我的工作區 |
   |資料集名稱  |   ASAdataset  |
   |資料表名稱 |  ASATable  |
   | 驗證模式 | 使用者權杖 |

   ![設定串流分析輸出](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   本教學課程使用 *使用者權杖* 驗證模式。 若要使用受控識別，請參閱[使用受控識別來驗證您的 Power BI Azure 串流分析作業](powerbi-output-managed-identity.md)。

## <a name="create-queries-to-transform-real-time-data"></a>建立查詢來轉換即時資料

目前，您已設定串流分析作業來讀取傳入資料流。 下一步是建立即時分析資料的查詢。 查詢使用類似 SQL 的語言，可針對串流分析來擴充一些功能。

在這一節的教學課程中，您會建立並測試數個查詢來學習幾種方法，以轉換輸入資料流來分析。 

您在此處建立的查詢只是在螢幕上顯示轉換後的資料。 在稍後的章節中，您會將已轉換的資料寫入 Power BI。

若要深入了解語言，請參閱 [Azure Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)。

### <a name="test-using-a-pass-through-query"></a>使用傳遞查詢來測試

如果您想要封存每一個事件，您可以使用傳遞查詢，讀取事件承載中的所有欄位。

1. 在 Azure 入口網站中瀏覽至您的串流分析作業，然後選取 [作業拓撲] 底下的 [查詢]。 

2. 在查詢視窗中，輸入此查詢︰

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >如同 SQL，關鍵字不區分大小寫，空白字元也不重要。

    在此查詢中，`CallStream` 是您建立輸入時所指定的別名。 如果您使用不同的別名，請改為使用該名稱。

3. 選取 [測試查詢]。

    串流分析作業會查詢來自輸入的範例資料，然後在視窗底部顯示輸出。 結果指出事件中樞和串流分析作業都正確設定。

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="測試查詢的範例輸出":::

    您確實會看到的記錄數目取決於範例中所擷取的記錄數目。

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>使用資料行投影來減少欄位數目

在許多情況下，您的分析不需要輸入資料流的所有資料行。 您可以使用查詢來投影更小的一組傳回欄位，比傳遞查詢傳回的欄位更少。

執行下列查詢並注意輸出。

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>依區域計算來電數目：具彙總的輪轉視窗

假設您想要計算每個區域的來電數目。 在串流資料中，當您想要執行彙總函式時 (例如計數)，您需要將資料流分割成時態單位 (因為資料流本身實際上是沒有止境的)。 做法是使用串流分析[視窗函式](stream-analytics-window-functions.md)。 然後，您就可以將該視窗內的資料當作一個單位來使用。

在這個轉換過程中，您需要有一連串不重疊的時態性視窗，每個視窗會有一組特定的資料讓您分組和彙總。 這種視窗稱為「輪轉視窗」。 在輪轉視窗中，您可取得依 `SwitchNum` (代表發話國家/地區) 分組的來電計數。

1. 將下列查詢貼到查詢編輯器：

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    此查詢在 `FROM` 子句中使用 `Timestamp By` 關鍵字，以指定使用輸入資料流中的哪個時間戳記欄位來定義輪轉視窗。 在此案例中，視窗會將每一筆記錄的資料依 `CallRecTime` 欄位分段。 (如果未指定欄位，則時間範圍作業會使用每個事件抵達事件中樞的時間。 請參閱[串流分析查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)中的＜抵達時間與應用時間的比較＞。 

    投影包含 `System.Timestamp`，它會傳回每個視窗結尾的時間戳記。 

    若要指定使用輪轉視窗，請在 `GROUP BY` 子句中使用 [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) 函式。 在此函式中，您可以指定時間單位 (從一微秒到一天即可) 和視窗大小 (單位數量)。 在本範例中，輪轉視窗由 5 秒的間隔組成，所以會依國家/地區產生每 5 秒的來電計數。

2. 選取 [測試查詢]。 在結果中，可看見 **WindowEnd** 底下的時間戳記是以 5 秒為增量單位。

### <a name="detect-sim-fraud-using-a-self-join"></a>使用自我聯結來偵測 SIM 詐騙

在此範例中，請將詐騙手法想像成同一位使用者在 5 秒內從不同位置撥出多通電話。 例如，按照常理，同一位使用者不可能同時從美國和澳大利亞打電話。

若要檢查這些情況，您可以使用自我聯結的串流資料，根據 `CallRecTime` 值將資料流聯結到本身。 然後，您可尋找 `CallingIMSI` 值 (發話號碼) 相同但 `SwitchNum` 值 (發話國家/地區) 不同的通話記錄。

當您在串流資料中使用聯結時，聯結必須稍微限制相符的資料列在時間上可以相隔多久。 如稍早所述，串流資料實際上是沒有止境的。 在聯結的 `ON` 子句內，可使用 `DATEDIFF` 函式來指定關聯性的時間界限。 在此案例中，聯結是以 5 秒間隔的通話資料為基礎。

1. 將下列查詢貼到查詢編輯器：

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    此查詢就像任何 SQL 聯結一樣，差別在於聯結中的 `DATEDIFF` 函式。 這是串流分析專用的 `DATEDIFF` 版本，必須出現在 `ON...BETWEEN` 子句中。 參數是時間單位 (在此範例中為秒) 和兩個聯結來源的別名。 這不同於標準 SQL `DATEDIFF` 函式。

    `WHERE` 子句中的條件會標出詐騙電話：發話端交換機不相同。

2. 選取 [測試查詢]。 檢閱輸出，然後選取 [儲存查詢]。

## <a name="start-the-job-and-visualize-output"></a>啟動作業並將輸出視覺化

1. 若要啟動作業，請瀏覽至作業的 [概觀]，然後選取 [啟動]。

2. 針對作業輸出開始時間選取 [現在]  並選取 [啟動]  。 您可以在通知列中檢視作業狀態。

3. 作業成功之後，請瀏覽至 [Power BI](https://powerbi.com/)，然後使用公司或學校帳戶登入。 如果串流分析作業查詢會輸出結果，您所建立的 *ASAdataset* 會存在於 [資料集] 索引標籤下。

4. 從 Power BI 工作區中選取 [+ 建立]，建立名為 *詐騙電話* 的新儀表板。

5. 在視窗頂端選取 [編輯]，然後選取 [新增圖格]。 接著選取 [自訂串流資料]  ，然後選取 [下一步]  。 在 [您的資料集] 下方選擇 **ASAdataset**。 從 [視覺效果類型] 下拉式清單中選取 [卡]，並將 **詐騙變化** 新增至 [欄位]。 選取 [下一步]  以輸入圖格的名稱，然後選取 [套用]  以建立圖格。

   ![建立 Power BI 儀表板圖格](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. 使用下列選項，再次依照步驟 5 操作：
   * 在 [視覺效果類型] 中選取 [折線圖]。
   * 新增軸並選取 [windowend]。
   * 新增值並選取 [fraudulentcalls]。
   * 在 [要顯示的時間範圍] 中，選取過去 10 分鐘。

7. 兩個圖格都新增之後，儀表板應該會如下列範例所示。 請注意，如果您的事件中樞傳送者應用程式和串流分析應用程式正在執行，則 Power BI 儀表板會在新資料送達時定期更新。

   ![在 Power BI 儀表板中檢視結果](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>在 Web 應用程式中內嵌 Power BI 儀表板

在這部分的教學課程中，您將使用 Power BI 團隊所建立的範例 [ASP.NET](https://asp.net/) Web 應用程式來內嵌您的儀表板。 如需內嵌儀表板的詳細資訊，請參閱[內嵌 Power BI](/power-bi/developer/embedding) 一文。

若要設定應用程式，請移至 [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub 存放庫，並遵循 **使用者擁有資料** 一節之下的指示進行 (使用 **integrate-web-app** 小節之下的重新導向和首頁 URL)。 因為我們使用儀表板範例，所以使用位於 [GitHub 存放庫](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)中的 **integrate-web-app** 範例程式碼。
一旦在瀏覽器中執行應用程式，請遵循下列步驟將先前建立的儀表板內嵌到網頁中：

1. 選取 [登入 Power BI]，以將您的 Power BI 帳戶中儀表板的存取權授與應用程式。

2. 選取 [取得儀表板] 按鈕，即可在資料表中顯示您的帳戶儀表板。 尋找您先前建立的儀表板名稱 (**powerbi-embedded-dashboard**)，並且複製對應的 **EmbedUrl**。

3. 最後，將 **EmbedUrl** 貼到對應的文字欄位中，然後選取 [內嵌儀表板]。 您現在可以檢視 Web 應用程式中內嵌的相同儀表板。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立簡單的串流分析作業、分析傳入的資料，並且在 Power BI 儀表板中呈現結果。 若要深入了解串流分析作業，請繼續下一個教學課程：

> [!div class="nextstepaction"]
> [在 Azure 串流分析作業中執行 Azure Functions](stream-analytics-with-azure-functions.md)