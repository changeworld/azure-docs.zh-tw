---
title: 教學課程：專用 SQL 集區的機器學習模型評分精靈
description: 如何使用機器學習模型評分精靈來擴充專用 SQL 集區中資料的教學課程。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935234"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>教學課程：專用 SQL 集區的機器學習模型評分精靈 (預覽)

了解如何使用預測性機器學習模型，輕鬆地擴充專用 SQL 集區中的資料。 您資料科學家所建立的模型現在可輕鬆地供資料專業人員進行預測性分析。 Azure Synapse Analytics 中的資料專業人員可以直接從 Azure Machine Learning 模型登錄選取模型，以便在 Azure Synapse SQL 集區中進行部署，並啟動預測以豐富資料。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> - 定型預測性機器學習模型，並在 Azure Machine Learning 模型登錄中註冊模型。
> - 您可以使用 SQL 評分嚮導來啟動專用 SQL 集區中的預測。

如果您沒有 Azure 訂用帳戶，請[在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md) ，具有設定為預設儲存體的 Azure Data Lake Storage Gen2 儲存體帳戶。 您必須是您所使用 Data Lake Storage Gen2 檔案系統的 *儲存體 Blob 資料參與者* 。
- 在您 Azure Synapse Analytics 工作區中的專用 SQL 集區。 如需詳細資訊，請參閱[建立專用 SQL 集區](../quickstart-create-sql-pool-studio.md)。
- Azure Synapse Analytics 工作區中的 Azure Machine Learning 連結服務。 如需詳細資料，請參閱[在 Azure Synapse 中建立 Azure Machine Learning 已連結的服務](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="train-a-model-in-azure-machine-learning"></a>在 Azure Machine Learning 中將模型定型

開始之前，請確認您的 sklearn 版本是 0.20.3。

在您執行筆記本中的所有資料格之前，請先檢查計算實例是否正在執行。

![顯示 Azure Machine Learning 計算驗證的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. 移至您的 Azure Machine Learning 工作區。

1. 下載 [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301)。

1. 開啟 [Azure Machine Learning Studio](https://ml.azure.com)中的 Azure Machine Learning 工作區。

1. 移至 **筆記本**  >  **上傳** 檔案。 然後選取您已下載並上傳的 **NYC 計程車秘訣 .ipynb** 檔案。
   ![用來上傳檔案之按鈕的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. 上傳並開啟筆記本之後，請選取 [ **執行所有資料格**]。

   其中一個資料格可能會失敗，並要求您向 Azure 進行驗證。 請在資料格輸出中監看這點，然後在瀏覽器中遵循連結並輸入程式碼來進行驗證。 然後重新執行筆記本。

1. 筆記本會將 ONNX 模型定型，並向 MLflow 註冊。 移至 [ **模型** ]，確認已正確註冊新的模型。
   ![顯示登錄中模型的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. 執行筆記本也會將測試資料匯出為 CSV 檔案。 將 CSV 檔案下載到您的本機系統。 稍後，您會將 CSV 檔案匯入到專用的 SQL 集區，並使用資料來測試模型。

   CSV 檔案會建立在與您筆記本檔案相同的資料夾中。 如果您沒有立即看到 **，請在檔案總管中選取** [重新整理]。

   ![顯示 C S V 檔案的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>使用 SQL 計分 wizard 啟動預測

1. 使用 Synapse Studio 開啟 Azure Synapse 工作區。

1. 移至 **資料**  >  **連結**  >  的 **儲存體帳戶**。 將 `test_data.csv` 上傳到預設儲存體帳戶。

   ![顯示用於上傳資料之選項的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. 移至 [開發] > [SQL 指令碼]。 建立新的 SQL 指令碼，以將 `test_data.csv` 載入您的專用 SQL 集區。

   > [!NOTE]
   > 請先更新此指令碼中的檔案 URL，再加以執行。

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![將資料載入到專用 SQL 集區](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. 移至 [資料] > [工作區]。 在專用 SQL 集區資料表上按一下滑鼠右鍵，以開啟 SQL 評分精靈。 選取 [機器學習] > [使用現有的模型進行擴充]。

   > [!NOTE]
   > 除非您已針對 Azure Machine Learning 建立連結的服務，否則不會出現機器學習服務選項。  (請參閱本教學課程開頭的 [必要條件](#prerequisites) 。 ) 

   ![顯示機器學習選項的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. 在下拉式方塊中選取連結的 Azure Machine Learning 工作區。 此步驟會從所選 Azure Machine Learning 工作區的模型登錄載入機器學習模型的清單。 目前只支援 ONNX 模型，因此此步驟只會顯示 ONNX 模型。

1. 選取您剛剛定型的模型，然後選取 [ **繼續**]。

   ![顯示選取 Azure Machine Learning 模型的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. 將資料表資料行對應至模型輸入，並指定模型輸出。 如果將模型儲存為 MLflow 格式，並填入模型簽章，則會根據名稱的相似性使用邏輯，自動為您完成對應。 介面也支援手動對應。

   選取 [繼續]。

   ![顯示資料表對模型對應的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. 產生的 T-sql 程式碼會包裝在預存程式內。 這就是您需要提供預存程式名稱的原因。 模型二進位檔（包括中繼資料 (版本、描述和其他資訊) ）將會從 Azure Machine Learning 實際複製到專用的 SQL 集區資料表。 因此，您必須指定要在哪一個資料表中儲存模型。 

   您可以選擇 **現有的資料表** 或 **建立新** 的資料表。 當您完成時，請選取 [ **部署模型 + 開啟腳本** ] 來部署模型並產生 t-sql 預測腳本。

   ![顯示建立預存程式之選項的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. 產生腳本之後， **請選取 [執行]** 來執行評分並取得預測。

   ![顯示評分和預測的螢幕擷取畫面。](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>後續步驟

- [快速入門：在 Azure Synapse 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)
