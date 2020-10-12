---
title: Azure 串流分析與 Azure Machine Learning 整合
description: 本文說明如何快速設定簡單的 Azure 串流分析作業，以使用使用者定義函數來整合 Azure Machine Learning。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 26a1208131f1d9d3df7dccd8e27bda37992f043f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236639"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>使用 Azure 串流分析和 Azure Machine Learning Studio (傳統) 進行情感分析

本文說明如何設定簡單的 Azure 串流分析作業，以使用 Azure Machine Learning Studio (傳統) 進行情感分析。 您可以使用 Cortana 智慧資源庫中的 Machine Learning 情感分析模型，來分析串流文字資料並判斷情感分數。

> [!TIP]
> 強烈建議使用 [Azure Machine Learning UDF](machine-learning-udf.md)，而不是Azure Machine Learning Studio (傳統版) UDF，以改善效能和可靠性。

您可以將本文所學套用到以下這類案例：

* 分析串流 Twitter 資料的即時情感。
* 分析客戶與支援人員對談的記錄。
* 評估論壇、部落格和影片的註解。
* 許多其他即時、預測評分的案例。

您建立的串流分析工作會將情感分析模型做為使用者定義的函數 (UDF) 套用到 Blob 存放區中的範例文字資料。 輸出 (情感分析的結果) 會以不同的 CSV 檔案寫入相同的 Blob 存放區。 

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您具有下列項目：

* 有效的 Azure 訂用帳戶。

* 內含一些 Twitter 資料的 CSV 檔案。 您可以從 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)下載範例檔案，也可以建立您自己的檔案。 在真實世界的案例中，您會直接從 Twitter 資料流取得資料。

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>建立儲存體容器並上傳 CSV 輸入檔

在此步驟中，您會將 CSV 檔案上傳至儲存體容器。

1. 在 Azure 入口網站中，選取 [**建立資源**  >  **儲存體**  >  **帳戶**]。

2. 在 [ *基本* ] 索引標籤中填寫下列詳細資料，並保留其餘欄位的預設值：

   |欄位  |值  |
   |---------|---------|
   |訂用帳戶|選擇您的訂用帳戶。|
   |資源群組|選擇您的資源群組。|
   |儲存體帳戶名稱|輸入儲存體帳戶的名稱。 此名稱在整個 Azure 中必須是唯一的。|
   |Location|選擇位置。 所有資源都應該使用相同的位置。|
   |帳戶類型|BlobStorage|

   ![提供儲存體帳戶詳細資料](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. 選取 [檢閱 + 建立]  。 然後，選取 [ **建立** ] 以部署儲存體帳戶。

4. 當部署完成時，請流覽至您的儲存體帳戶。 在 [Blob 服務] 之下，選取 [容器]。 然後選取 [ **+ 容器** ] 以建立新的容器。

   ![建立供輸入使用的 Blob 儲存體容器](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. 提供容器的名稱，並確認 **公用存取層級** 設為 [ **私**用]。 當您完成時，選取 [建立]。

   ![指定 Blob 容器詳細資料](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. 流覽至新建立的容器，然後選取 **[上傳**]。 上傳您稍早下載的 **sampleinput.csv** 檔案。

   ![容器的 [上傳] 按鈕](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>新增 Cortana 智慧資源庫中的情緒分析模型

現在，Blob 中已有範例資料，您可以啟用 Cortana 智慧資源庫中的情感分析模型。

1. 移至 Cortana 智慧資源庫中的[預測情感分析模型](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) \(英文\) 頁面。  

2. 選取 [ **在 Studio 中開啟] (傳統) **。  
   
   ![串流分析機器學習服務, 開啟 Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 登入以移至工作區。 選取位置。

4. 選取頁面底部的 [ **執行** ]。 程序會執行大約一分鐘的時間。

   ![在 Machine Learning Studio 中執行實驗](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 程序成功執行之後，請選取頁面底部的 [部署 Web 服務]****。

   ![在 Machine Learning Studio 中將實驗部署為 Web 服務](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 若要驗證情感分析模型是否已可供使用，請選取 [ **測試** ] 按鈕。 提供文字輸入，例如「我喜歡 Microsoft」。

   ![在 Machine Learning Studio 中測試實驗](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   如果測試成功，您會看到類似以下範例的結果：

   ![Machine Learning Studio 中的測試結果](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. 在 [ **應用程式** ] 欄位中，選取 **excel 2010 或更早** 的活頁簿連結來下載 excel 活頁簿。 活頁簿包含您稍後設定串流分析作業所需的 API 金鑰和 URL。

    ![串流分析機器學習服務, 快速概覽](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>建立使用機器學習服務模型的串流分析工作

您現在可以建立從 Blob 儲存體的 CSV 檔案中讀取範例推文的串流分析工作。

### <a name="create-the-job"></a>建立工作

移至 [Azure 入口網站](https://portal.azure.com) 並建立串流分析作業。 如果您不熟悉此程式，請參閱 [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。

### <a name="configure-the-job-input"></a>設定工作輸入

工作會從您稍早上傳到 Blob 儲存體的 CSV 檔案取得輸入。

1. 瀏覽至您的串流分析作業。 在 [ **作業拓撲**] 下，選取 [ **輸入** ] 選項。 選取 [**新增資料流程輸入**  > **Blob 儲存體**]。

2. 使用下列值填寫 **Blob 儲存體** 詳細資料：

   |欄位  |值  |
   |---------|---------|
   |輸入別名|為您的輸入提供名稱。 當您撰寫查詢時，請記住此別名。|
   |訂用帳戶|選取您的訂用帳戶。|
   |儲存體帳戶|選取您在上一個步驟中所做的儲存體帳戶。|
   |容器|選取您在上一個步驟中建立的容器。|
   |事件序列化格式|CSV|

3. 選取 [儲存]****。

### <a name="configure-the-job-output"></a>設定工作輸出

工作會將結果傳送至取得輸入的相同 Blob 儲存體。

1. 瀏覽至您的串流分析作業。 在 [ **作業拓撲**] 下，選取 [ **輸出** ] 選項。 選取 [**新增**  >  **Blob 儲存體**]。

2. 以下列值填寫 **Blob 儲存體** 表單：

   |欄位  |值  |
   |---------|---------|
   |輸入別名|為您的輸入提供名稱。 當您撰寫查詢時，請記住此別名。|
   |訂用帳戶|選取您的訂用帳戶。|
   |儲存體帳戶|選取您在上一個步驟中所做的儲存體帳戶。|
   |容器|選取您在上一個步驟中建立的容器。|
   |事件序列化格式|CSV|

3. 選取 [儲存]****。

### <a name="add-the-machine-learning-function"></a>新增機器學習服務函數

稍早您將機器學習模型發佈至 Web 服務。 在此案例中，串流分析作業執行時，會將來自輸入的每則範例推文傳送至 Web 服務進行情感分析。 Machine Learning Web 服務會傳回情感 (`positive`、`neutral` 或 `negative`)，以及推文屬於正向的機率。

在本節中，您會在串流分析工作中定義函數。 叫用函數，可以將推文傳送至 Web 服務並得到回應。

1. 請確定您有稍早下載的 Web 服務 URL 和 API 金鑰 (在 Excel 活頁簿中)。

2. 瀏覽至您的串流分析作業。 然後選取**函數**  >  **+ 新增**  >  **Azure ML Studio**

3. 在 **Azure Machine Learning** 函式表單中填入下列值：

   |欄位  |值  |
   |---------|---------|
   | 函式別名 | 使用 [名稱] `sentiment` 並選取 [ **提供 Azure Machine Learning**函式設定]，這可讓您選擇輸入 URL 和金鑰。      |
   | URL| 貼上 Web 服務 URL。|
   |機碼 | 貼上 API 金鑰。 |

4. 選取 [儲存]。

### <a name="create-a-query-to-transform-the-data"></a>建立查詢來轉換資料

串流分析使用宣告式、以 SQL 為基礎的查詢來檢查輸入並處理它。 在本節中，您要建立從輸入讀取每則推文的查詢，然後叫用機器學習服務函數來執行情感分析。 查詢接著會將結果傳送至您定義的輸出 (Blob 儲存體)。

1. 返回串流分析作業總覽。

2. 在 [作業拓撲]  下方，選取 [查詢]  。

3. 輸入下列查詢：

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   此查詢會叫用您稍早建立的函式 (`sentiment`) ，以針對輸入中的每個推文執行情感分析。

4. 選取 [ **儲存** ] 以儲存查詢。

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>啟動串流分析工作並查看輸出

您現在可以啟動串流分析工作。

### <a name="start-the-job"></a>啟動工作

1. 返回串流分析作業總覽。

2. 選取頁面頂端的 [ **開始** ]。

3. 在 [ **啟動工作**] 中選取 [ **自訂**]，然後選取您將 CSV 檔案上傳至 blob 儲存體之前的一天。 完成時，選取 [啟動]****。  

### <a name="check-the-output"></a>查看輸出

1. 讓工作執行幾分鐘，直到您在 [監視]**** 方塊中看到活動。

2. 如果您的工具通常用來檢查 blob 儲存體的內容，請使用該工具來檢查容器。 或者，請在 Azure 入口網站中執行下列步驟：

      1. 在 Azure 入口網站中，尋找您的儲存體帳戶，並在帳戶中尋找容器。 您會在容器中看到兩個檔案：包含範例推文的檔案，和串流分析工作所產生的 CSV 檔案。
      2. 以滑鼠右鍵按一下所產生的檔案，然後選取 [下載]****。

3. 開啟產生的 CSV 檔案。 您會看到類似下列範例的畫面：  

   ![串流分析機器學習服務, CSV 檢視](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>檢視計量

您也能檢視與 Azure Machine Learning 函數相關的度量。 下列與函式相關的度量會顯示在工作總覽的 [ **監視** ] 方塊中：

* **函數要求** 指出傳送至 Machine Learning Web 服務的要求數目。  
* **函數事件** 指出要求中的事件數目。 根據預設，每個對 Machine Learning Web 服務的要求最多可包含 1,000 個事件。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [整合 REST API 與 Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
