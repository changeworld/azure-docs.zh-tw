---
title: 教學課程：使用批次偵測與 Power BI 將異常狀況視覺化
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Anomaly Detector API 與 Power BI 將整個時間序列資料的異常狀況視覺化。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 887e7432151569eb07e3c9256c7f126e7f884d54
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017619"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>教學課程：使用批次偵測與 Power BI 將異常狀況視覺化

使用本教學課程，以批次方式尋找時間序列資料集內的異常狀況。 使用 Power BI Desktop，您將取得 Excel 檔案、為 Anomaly Detector API 準備資料，以及將所有統計異常視覺化。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Power Bi Desktop 匯入及轉換時間序列資料集
> * 整合 Power BI Desktop 與 Anomaly Detector API 以便進行批次異常偵測
> * 將您資料中的異常狀況視覺化，包括預期和所見的值，以及異常偵測界限。

## <a name="prerequisites"></a>必要條件
* [Azure 訂用帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)(免費提供)。
* 包含時間序列資料點的 Excel 檔案 (.xlsx)。 此快速入門的範例資料可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962) 上找到。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="建立異常偵測器資源"  target="_blank">建立異常偵測器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至異常偵測器 API。 您稍後會在快速入門中執行此動作。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>載入及格式化時間序列資料

若要開始使用，請開啟 Power BI Desktop 並載入您從必要條件下載的時間序列資料。 此 Excel 檔案包含一系列的國際標準時間 (UTC) 時間戳記和值組。  

> [!NOTE]
> Power BI 可以使用各種來源的資料，例如 .csv 檔案、SQL 資料庫、Azure Blob 儲存體等等。  

在 Power BI Desktop 主視窗中，按一下 [首頁]**** 功能區。 在功能區的 [外部資料]**** 群組中，開啟 [取得資料]**** 下拉式功能表，然後按一下 [Excel]****。

![Power BI 中的 [取得資料] 按鈕的影像](../media/tutorials/power-bi-get-data-button.png)

對話方塊出現之後，請瀏覽至您下載範例 .xlsx 檔案的資料夾並加以選取。 在 [導覽]**** 對話方塊出現後，按一下 **Sheet1**，然後按一下 [編輯]****。

![Power BI 中資料來源 [導覽] 畫面的影像](../media/tutorials/navigator-dialog-box.png)

Power BI 會將第一欄中的時間戳記轉換為 `Date/Time` 資料類型。 這些時間戳記必須轉換成文字，才能傳送到 Anomaly Detector API。 如果 Power Query 編輯器不會自動開啟，請按一下首頁索引標籤上的 [編輯查詢]****。

按一下 Power Query 編輯器中的 [轉換]**** 功能區。 在 [任何資料行]**** 群組中，開啟 [資料類型:]**** 下拉式功能表，然後選取 [文字]****。

![資料類型下拉式清單的影像](../media/tutorials/data-type-drop-down.png)

當您取得有關變更資料行類型的通知時，請按一下 [取代目前]****。 接著，按一下 [首頁]**** 功能區中的 [關閉並套用]**** 或 [套用]****。

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>建立函式以傳送資料並將回應格式化

若要將資料檔案格式化並傳送到 Anomaly Detector API，您可以在上面建立的資料表上叫用查詢。 在 Power Query 編輯器中，從 [首頁]**** 功能區開啟 [新增來源]**** 下拉式功能表，然後選取 [空白查詢]****。

確定已選取您的新查詢，然後按一下 [進階編輯器]****。

![[進階編輯器] 畫面的影像](../media/tutorials/advanced-editor-screen.png)

在 [進階編輯器] 內，使用下列 Power Query M 程式碼片段，從資料表擷取資料行並將它傳送至 API。 接著，查詢會從 JSON 回應建立資料表並將它傳回。 以有效的 Anomaly Detector API 金鑰取代 `apiKey` 變數，並以您的端點取代 `endpoint`。 在 [進階編輯器] 中輸入查詢之後，按一下 [完成]****。

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

選取 [輸入參數]**** 下方的 `Sheet1`，然後按一下 [叫用]****，以在您的資料工作表上叫用查詢。

![叫用函式的影像](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>資料來源隱私權和驗證

> [!NOTE]
> 請留意貴組織的資料隱私性和存取原則。 如需詳細資訊，請參閱 [Power BI Desktop 隱私權等級](https://docs.microsoft.com/power-bi/desktop-privacy-levels)。

您在嘗試執行查詢時，可能會收到一則警告訊息，因為它會利用外部資料來源。

![顯示 Power BI 所建立警告的影像](../media/tutorials/blocked-function.png)

若要修正此問題，按一下 [檔案]****，然後按一下 [選項與設定]****。 然後按一下 [選項]****。 在 [目前檔案]**** 下方，選取 [隱私權]****，然後選取 [忽略隱私權等級並可能改善效能]****。

此外，您可能會收到訊息，要求您指定連線到 API 的方式。

![顯示要求指定存取認證的影像](../media/tutorials/edit-credentials-message.png)

若要修正此問題，按一下訊息中的 [編輯認證]****。 在對話方塊出現之後，選取 [匿名]**** 以匿名方式連線到 API。 然後按一下 [ **連接**]。

接著，按一下 [首頁]**** 功能區中的 [關閉並套用]****，或套用變更。

## <a name="visualize-the-anomaly-detector-api-response"></a>將 Anomaly Detector API 回應視覺化

在 Power BI 主畫面中，開始使用上面建立的查詢將資料視覺化。 先選取 [視覺效果]**** 中的 [折線圖]****。 然後從叫用的函式，將時間戳記新增到折線圖的 [軸]****。 以滑鼠右鍵按一下它，然後選取 [時間戳記]****。

![以滑鼠右鍵按一下 [時間戳記] 值。](../media/tutorials/timestamp-right-click.png)

從 [叫用的函式]**** 將下列欄位新增到圖表的 [值]**** 欄位。 使用以下螢幕擷取畫面，協助建立您的圖表。

* 值
* UpperMargins
* LowerMargins
* ExpectedValues

![圖表設定的影像](../media/tutorials/chart-settings.png)

新增欄位之後，按一下圖表並調整其大小，以顯示所有的資料點。 您的圖表看起來類似以下螢幕擷取畫面：

![圖表視覺效果的影像](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>顯示異常資料點

在 Power BI 視窗的右側，[欄位]**** 窗格下面，以滑鼠右鍵按一下 [叫用的函式查詢]**** 底下的 [值]****，然後按一下 [新快速量值]****。

![新快速量值畫面的影像](../media/tutorials/new-quick-measure.png)

在出現的畫面上，選取 [篩選的值]**** 作為計算。 將 [基底值]**** 設定為 `Sum of Value`。 然後將 `IsAnomaly` 從 [叫用的函式]**** 欄位拖曳到 [篩選條件]****。 從 [篩選條件]**** 下拉式功能表中選取 `True`。

![新快速量值畫面的第二個影像](../media/tutorials/new-quick-measure-2.png)

按一下 [確定]**** 之後，您的欄位清單底部會出現 `Value for True` 欄位。 以滑鼠右鍵按一下它並重新命名為 [異常]****。 將它新增至圖表的 [值]****。 然後選取 [格式]**** 工具，並將 X 軸類型設定為 [類別]****。

![X 軸格式的影像](../media/tutorials/format-x-axis.png)

按一下 [格式]**** 工具和 [資料色彩]****，將色彩套用至圖表。 您的圖表應該如下所示：

![最終圖表的影像](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[使用 Azure Databricks 串流異常偵測](anomaly-detection-streaming-databricks.md)
