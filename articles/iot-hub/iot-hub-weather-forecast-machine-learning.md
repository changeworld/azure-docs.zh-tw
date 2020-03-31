---
title: 使用 Azure 機器學習與 IoT 中心資料進行天氣預報
description: 使用 Azure Machine Learning，根據 IoT 中樞透過感應器收集的溫度和溼度資料來預測下雨的機會。
author: robinsh
manager: philmea
keywords: 氣象預報機器學習
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122298"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>在 Azure Machine Learning 中使用 IoT 中樞的感應器資料進行氣象預報

![端對端圖表](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

機器學習服務是一項資料科學技術，協助電腦從現有的資料學習，以便預測未來的行為、結果和趨勢。 Azure Machine Learning 是雲端預測性分析服務，可讓您快速地建立預測模型，並將其部署為分析解決方案。

## <a name="what-you-learn"></a>您學到什麼

了解如何使用 Azure Machine Learning 透過來自您的 Azure IoT 中樞的溫度和溼度資料執行氣象預報 (下雨的機會)。 下雨的機會是備妥的氣象預報模型的輸出。 此模型的建置是根據溫度和溼度的歷史資料來預測下雨的機會。

## <a name="what-you-do"></a>您要做什麼

- 將氣象預報模型部署為 Web 服務。
- 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。
- 建立串流分析作業，以及設定作業：
  - 從 IoT 中樞讀取溫度和溼度資料。
  - 呼叫 Web 服務來取得降雨機會。
  - 將結果儲存至 Azure Blob 儲存體。
- 使用 Microsoft Azure 儲存體總管來檢視氣象預報。

## <a name="what-you-need"></a>您需要什麼

- 完成[樹莓派線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或設備教程之一;例如，[樹莓派與node.js。](iot-hub-raspberry-pi-kit-node-get-started.md) 這些要求包括以下要求：
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。
- [Azure 機器學習工作室（經典）](https://studio.azureml.net/)帳戶。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>將氣象預報模型部署為 Web 服務

在本節中，您將從 Azure AI 庫獲取天氣預報模型。 然後，向模型添加 R 腳本模組以清潔溫度和濕度資料。 最後，將模型部署為預測 Web 服務。

### <a name="get-the-weather-prediction-model"></a>獲取天氣預報模型

在本節中，您將從 Azure AI 庫獲取天氣預報模型，並在 Azure 機器學習工作室（經典）中打開它。

1. 移至 [氣象預報模型頁面](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)。

   ![在 Azure AI 庫中打開天氣預報模型頁面](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. 按一下 **"在工作室中打開"（經典）** 以在 Microsoft Azure 機器學習工作室（經典）中打開模型。

   ![在 Azure 機器學習工作室中打開天氣預報模型（經典）](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>添加 R 腳本模組以清潔溫度和濕度資料

要使模型正確運行，溫度和濕度資料必須轉換為數值資料。 在本節中，您將 R 腳本模組添加到天氣預報模型中，該模型將刪除具有溫度或濕度資料值的任何行，這些值無法轉換為數值。

1. 在 Azure 機器學習工作室視窗的左側，按一下箭頭展開工具面板。 在搜索框中輸入"執行"。 選擇**執行 R 腳本**模組。

   ![選擇執行 R 腳本模組](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. 在關係圖上拖動"**清除缺失資料**"模組和現有執行**R 腳本**模組附近的**執行 R 腳本**模組。 刪除 **"清除缺失資料****"與執行 R 腳本**模組之間的連接，然後按所示連接新模組的輸入和輸出。

   ![添加執行 R 腳本模組](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 選擇新的**執行 R 腳本**模組以打開其屬性視窗。 複製以下代碼並將其粘貼到**R 腳本**框中。

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   完成後，屬性視窗應類似于以下內容：

   ![將代碼添加到執行 R 腳本模組](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>部署預測性 Web 服務

在本節中，您可以驗證模型，根據模型設置預測 Web 服務，然後部署 Web 服務。

1. 按一下 **"運行**"以驗證模型中的步驟。 此步驟可能需要幾分鐘才能完成。

   ![運行實驗以驗證步驟](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. 按一下 **"設置 WEB 服務** > **預測 Web 服務**"。 將打開預測實驗圖。

   ![在 Azure 機器學習工作室中部署天氣預報模型（經典）](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 在預測實驗圖中，刪除**Web 服務輸入**模組和頂部**的天氣資料集**之間的連接。 然後拖動在**分數模型**模組附近的 Web**服務輸入**模組，然後如圖所示連接它：

   ![連接 Azure 機器學習工作室中的兩個模組（經典）](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. 按一下 [執行]**** 來驗證模型中的步驟。

1. 按一下 [部署 Web 服務]**** 來將模型部署為 Web 服務。

1. 在模型的儀表板上，下載**要求/回應**的 **Excel 2010 或舊版活頁簿**。

   > [!Note]
   > 請確保您下載**Excel 2010 或更早的活頁簿**，即使您在電腦上運行了更高版本的 Excel。

   ![下載要求回應端點的 Excel](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. 開啟 Excel 活頁簿，請記下 **Web 服務 URL** 和**存取金鑰**。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>設定、設定及執行串流分析作業

### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在[Azure 門戶](https://portal.azure.com/)中，按一下"**創建資源** > **物聯網** > **流分析作業**"。
1. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。

   **資源組**：使用 IoT 中心使用的相同資源組。

   **位置**︰使用與資源群組相同的位置。

   **釘選至儀表板**︰核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。

   ![在 Azure 中建立串流分析作業](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. 按一下 **[建立]**。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 開啟串流分析作業。
1. 在 [作業拓撲]**** 之下，按一下 [輸入]****。
1. 在 [輸入]**** 窗格中，按一下 [新增]****，然後輸入下列資訊︰

   **輸入別名**︰輸入的唯一別名。

   **來源**︰選取 [IoT 中樞]****。

   **取用者群組**：選取您建立的取用者群組。

   ![在 Azure 中將輸入新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. 按一下 **[建立]**。

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [作業拓撲]**** 之下，按一下 [輸出]****。
1. 在 [輸出]**** 窗格中，按一下 [新增]****，然後輸入下列資訊︰

   **輸出別名**︰輸出的唯一別名。

   **匯**： 選擇**Blob 存儲**。

   **儲存體帳戶**：您 Blob 儲存體的儲存體帳戶。 您可以建立儲存體帳戶，或使用現有的帳戶。

   **容器**：儲存 Blob 所在位置的容器。 您可以建立容器或是使用現有的容器。

   **事件序列化格式**：選取 [CSV]****。

   ![在 Azure 中將輸出新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. 按一下 **[建立]**。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>將功能新增至串流分析作業，以呼叫您所部署的 Web 服務

1. 在 [作業拓撲]**** 下，按一下 [函式]**** > [新增]****。
1. 輸入以下資訊：

   **函式別名**：輸入 `machinelearning`。

   **函數類型**：選擇**Azure ML**。

   **匯入選項**：選取 [從不同的訂用帳戶匯入]****。

   **URL**：輸入您從 Excel 活頁簿記下的 Web 服務 URL。

   **金鑰**：輸入您從 Excel 活頁簿記下的存取金鑰。

   ![在 Azure 中將功能新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. 按一下 **[建立]**。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [作業拓撲]**** 之下，按一下 [查詢]****。
1. 將現有程式碼取代為下列程式碼：

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   使用作業的輸入別名取代 `[YourInputAlias]`。

   使用作業的輸出別名取代 `[YourOutputAlias]`。

1. 按一下 [儲存]****。

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在"流分析"作業中**Start** > ，按一下"**立即** > **開始**"。 成功啟動作業後，作業狀態會從 [已停止]**** 變更為 [執行中]****。

![執行串流分析作業](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>使用 Microsoft Azure 儲存體總管來檢視氣象預報

執行用戶端應用程式來開始收集和傳送溫度和溼度資料至 IoT 中樞。 對於 IoT 中樞收到的每個訊息，串流分析作業會呼叫氣象預報 Web 服務，以產生下雨的機會。 接著會將結果儲存到您的 Azure Blob 儲存體。 Azure 儲存體總管是一個工具，可供您用來檢視結果。

1. [下載並安裝 Microsoft Azure 儲存體總管](https://storageexplorer.com/)。
1. 開啟 [Azure 儲存體總管]。
1. 登入您的 Azure 帳戶。
1. 選取您的訂用帳戶。
1. 按一下您的訂用帳戶 > [儲存體帳戶]**** > 您的儲存體帳戶 > [Blob 容器]**** > 您的容器。
1. 下載 .csv 檔以查看結果。 最後一個資料行記錄下雨的機會。

   ![使用 Azure Machine Learning 取得氣象預報結果](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>總結

您已成功使用 Azure Machine Learning 根據 IoT 中樞收到的溫度和溼度資料來產生下雨的機會。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]