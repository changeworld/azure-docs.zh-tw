---
title: 使用 Azure Machine Learning Studio (傳統) 搭配 IoT 中樞資料的氣象預報
description: 使用 Azure Machine Learning Studio (傳統) 根據 IoT 中樞從感應器收集的溫度和濕度資料，來預測雨的機會。
author: robinsh
manager: philmea
keywords: 氣象預報機器學習
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 8ba68e56d2475b1ff2fb3e63f291f76063ca62e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777151"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>使用 Azure Machine Learning Studio 中 IoT 中樞的感應器資料進行氣象預報 (傳統) 

![端對端圖表](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

機器學習服務是一項資料科學技術，協助電腦從現有的資料學習，以便預測未來的行為、結果和趨勢。 Azure Machine Learning Studio (傳統) 是雲端預測性分析服務，可讓您快速地建立預測模型，並將其部署為分析解決方案。

## <a name="what-you-learn"></a>您學到什麼

您將瞭解如何使用 Azure Machine Learning Studio (傳統) 來執行氣象預測， (使用 Azure IoT 中樞的溫度和濕度資料，有雨的) 。 下雨的機會是備妥的氣象預報模型的輸出。 此模型的建置是根據溫度和溼度的歷史資料來預測下雨的機會。

## <a name="what-you-do"></a>您要做什麼

- 將氣象預報模型部署為 Web 服務。
- 新增取用者群組，讓您的 IoT 中樞準備好進行資料存取。
- 建立串流分析作業，以及設定作業：
  - 從 IoT 中樞讀取溫度和溼度資料。
  - 呼叫 Web 服務來取得降雨機會。
  - 將結果儲存至 Azure Blob 儲存體。
- 使用 Microsoft Azure 儲存體總管來檢視氣象預報。

## <a name="what-you-need"></a>您需要什麼

- 完成 [Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程，或其中一個裝置教學課程；例如，[Raspberry Pi with node.js](iot-hub-raspberry-pi-kit-node-get-started.md)。 其中涵蓋下列需求：
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。
- [Azure Machine Learning Studio (傳統版)](https://studio.azureml.net/) 帳戶。
- 慣用的 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json#types-of-storage-accounts)（ **一般用途 v2** 帳戶），但任何支援 Azure Blob 儲存體的 Azure 儲存體帳戶也可以運作。

> [!Note]
> 本文使用 Azure 串流分析和數個其他付費服務。 必須在 Azure 區域之間傳輸資料時，Azure 串流分析中會產生額外費用。 基於這個理由，確定您的資源群組、IoT 中樞和 Azure 儲存體帳戶，以及稍後在本教學課程中新增的 Machine Learning Studio (傳統) 工作區和 Azure 串流分析作業，都位於相同的 Azure 區域。 您可以在 [ [依區域的 Azure 產品可用性] 頁面](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)上，查看 Azure Machine Learning Studio (傳統) 和其他 azure 服務的區域支援。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>將氣象預報模型部署為 Web 服務

在本節中，您會從 Azure AI 程式庫取得氣象預測模型。 接著，您可以將 R 指令碼模組新增至模型，以清除溫度和濕度資料。 最後，您會部署模型做為預測性 Web 服務。

### <a name="get-the-weather-prediction-model"></a>取得氣象預測模型

在本節中，您會從 Azure AI 資源庫取得氣象預測模型，並在 Azure Machine Learning Studio (傳統版) 中加以開啟。

1. 移至 [氣象預報模型頁面](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)。

   ![在 Azure AI 資源庫中開啟 [氣象預測模型] 頁面](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. 選取 [ **在 studio 中開啟] (傳統) ** 開啟 Microsoft Azure Machine Learning Studio (傳統) 中的模型。 從資源庫快顯視窗中選取 IoT 中樞附近的區域，以及 **複製實驗** 中的正確工作區。

   ![在 Azure Machine Learning Studio (傳統版) 中開啟氣象預報模型](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>新增 R 指令碼模組以清除溫度和濕度資料

若要讓模型正確運作，溫度和濕度資料必須可轉換為數值資料。 在本節中，您會將 R 指令碼模組新增至氣象預測模型，以移除具有無法轉換為數值之溫度或濕度資料值的任何資料列。

1. 在 Azure Machine Learning Studio (傳統) 視窗的左側，選取箭號以展開 [工具] 面板。 在搜尋方塊中輸入 "Execute"。 選取**執行 R 指令碼**模組。

   ![選取執行 R 指令碼模組](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. 將**執行 R 指令碼**模組拖曳到接近**清除遺漏的資料**模組，以及圖表上的現有**執行 R 指令碼**模組。 刪除**清除遺漏的資料**與**執行 R 指令碼**模組之間的連線，然後連線新模組的輸入和輸出，如下所示。

   ![新增執行 R 指令碼模組](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 選取新的**執行 R 指令碼**模組，以開啟其 [屬性] 視窗。 將下列程式碼複製並貼到 [R 指令碼] 方塊中。

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   當您完成時，[屬性] 視窗看起來應該如下所示：

   ![將指令碼新增至執行 R 指令碼模組](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>部署預測性 Web 服務

在本節中，您會驗證模型、根據模型設定預測性 Web 服務，然後部署 Web 服務。

1. 選取 [ **執行** ] 以驗證模型中的步驟。 完成此步驟可能需要數分鐘的時間。

   ![執行實驗來驗證步驟](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. 選取 [**設定 web 服務**  >  **預測性 web 服務**]。 預測性實驗圖表隨即開啟。

   ![在 Azure Machine Learning Studio (傳統版) 中部署氣象預報模型](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 在預測性實驗圖中，刪除 **Web 服務輸入** 模組與頂端 **資料集中的選取資料行** 之間的連接。 然後，將 [Web 服務輸入] 模組拖曳至接近 [評分模型] 模組並加以連線，如下所示：

   ![在 Azure Machine Learning Studio (傳統版) 中連線兩個模組](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. 選取 [ **執行** ] 以驗證模型中的步驟。

1. 選取 [ **部署 WEB 服務** ]，將模型部署為 WEB 服務。

1. 在模型的儀表板上，下載**要求/回應**的 **Excel 2010 或舊版活頁簿**。

   > [!Note]
   > 即使您電腦上執行較新版的 Excel，也請務必下載 **Excel 2010 或舊版活頁簿**。

   ![下載要求回應端點的 Excel](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. 開啟 Excel 活頁簿，請記下 **Web 服務 URL** 和**存取金鑰**。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>設定、設定及執行串流分析作業

### <a name="create-a-stream-analytics-job"></a>建立串流分析作業

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [ **建立資源**]。 在搜尋方塊中輸入「串流分析作業」，然後從 [結果] 下拉式清單中選取 [ **串流分析工作** ]。 當 [ **串流分析工作** ] 窗格開啟時，選取 [ **建立**]。
1. 輸入作業的以下資訊。

   **作業名稱**：作業的名稱。 此名稱必須是全域唯一的。

   **訂**用帳戶：如果您的訂用帳戶與預設值不同，請選取它。

   **資源群組**：使用 IoT 中樞所用的相同資源群組。

   **位置**：使用與資源群組相同的位置。

   將所有其他欄位保留為預設值。

   ![在 Azure 中建立串流分析作業](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. 選取 [建立]****。

### <a name="add-an-input-to-the-stream-analytics-job"></a>將輸入新增至串流分析作業

1. 開啟串流分析作業。
1. 在 [作業拓撲] 下，選取 [輸入]。
1. 在 [ **輸入** ] 窗格中，選取 [ **新增資料流程輸入**]，然後從下拉式清單中選取 [ **IoT 中樞** ]。 在 [ **新增輸入** ] 窗格中，選擇您訂用帳戶中的 [ **選取 IoT 中樞** ]，然後輸入下列資訊：

   **輸入別名**：輸入的唯一別名。

   **訂**用帳戶：如果您的訂用帳戶與預設值不同，請選取它。

   **Iot 中樞**：從您的訂用帳戶選取 IoT 中樞。

   **共用存取原則名稱**：選取  **服務**。  (您也可以使用 **iothubowner**。 ) 

   **取用者群組**：選取您所建立的取用者群組。

   將所有其他欄位保留為預設值。

   ![在 Azure 中將輸入新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. 選取 [儲存]  。

### <a name="add-an-output-to-the-stream-analytics-job"></a>將輸出新增至串流分析作業

1. 在 [作業拓撲] 下，選取 [輸出]。
1. 在 [ **輸出** ] 窗格中，選取 [ **新增**]，然後從下拉式清單中選取 [ **Blob 儲存體]/[Data Lake Storage** ]。 在新的 [ **輸出** ] 窗格中，選擇 [ **從您的訂用帳戶選取儲存體** ]，然後輸入下列資訊：

   **輸出別名**：輸出的唯一別名。

   **訂**用帳戶：如果您的訂用帳戶與預設值不同，請選取它。

   **儲存體帳戶**：您 Blob 儲存體的儲存體帳戶。 您可以建立儲存體帳戶，或使用現有的帳戶。

   **容器**：儲存 Blob 所在位置的容器。 您可以建立容器或是使用現有的容器。

   **事件序列化格式**：選取 [CSV]。

   ![在 Azure 中將輸出新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. 選取 [儲存]****。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>將功能新增至串流分析作業，以呼叫您所部署的 Web 服務

1. 在 [ **作業拓撲**] 下，選取 [ **函數**]。
1. 在 [ **函數** ] 窗格中，選取 [ **新增**]，然後從下拉式清單中選取 [ **Azure ML Studio** ]。  (確定選取 **AZURE Ml Studio**，而不是 **azure ml 服務**。 ) 在 **新** 的函式窗格上，選擇 **手動提供 Azure Machine Learning 函數設定** ，然後輸入下列資訊：

   **函式別名**：輸入 `machinelearning`。

   **URL**：輸入您從 Excel 活頁簿記下的 Web 服務 URL。

   **金鑰**：輸入您從 Excel 活頁簿記下的存取金鑰。

   ![在 Azure 中將功能新增至串流分析作業](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. 選取 [儲存]  。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>設定串流分析作業的查詢

1. 在 [作業拓撲] 下，選取 [查詢]。
1. 將現有程式碼取代為下列程式碼：

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   使用作業的輸入別名取代 `[YourInputAlias]`。

   使用作業的輸出別名取代 `[YourOutputAlias]`。

1. 選取 [儲存佇列]。

> [!Note]
> 如果您選取 [ **測試查詢**]，將會看到下列訊息：不支援使用 Machine Learning 函數進行查詢測試。 請修改查詢，然後再試一次。 您可以放心地忽略此訊息，然後選取 **[確定]** 關閉訊息方塊。 請務必先儲存查詢，然後再繼續進行下一節。

### <a name="run-the-stream-analytics-job"></a>執行串流分析作業

在串流分析作業中，選取左窗格上的 **[總覽** ]。 然後選取 [立即**啟動**]  >  **Now**  >  ** **。 成功啟動作業後，作業狀態會從 [已停止] 變更為 [執行中]。

![執行串流分析作業](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>使用 Microsoft Azure 儲存體總管來檢視氣象預報

執行用戶端應用程式來開始收集和傳送溫度和溼度資料至 IoT 中樞。 對於 IoT 中樞收到的每個訊息，串流分析作業會呼叫氣象預報 Web 服務，以產生下雨的機會。 接著會將結果儲存到您的 Azure Blob 儲存體。 Azure 儲存體總管是一個工具，可供您用來檢視結果。

1. [下載並安裝 Microsoft Azure 儲存體總管](https://storageexplorer.com/)。
1. 開啟 [Azure 儲存體總管]。
1. 登入您的 Azure 帳戶。
1. 選取您的訂用帳戶。
1. 選取您的訂用帳戶 > **儲存體** 帳戶 > 您的儲存體帳戶 > **Blob 容器** > 您的容器。
1. 下載 .csv 檔案來查看結果。 最後一個資料行記錄下雨的機會。

   ![使用 Azure Machine Learning Studio (傳統) 取得氣象預報結果](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>摘要

您已成功使用 Azure Machine Learning Studio (傳統) 根據您的 IoT 中樞所收到的溫度和濕度資料，產生 rain 的機會。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
