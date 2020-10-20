---
title: 教學課程：適用於 SQL 集區的機器學習模型評分精靈
description: 如何使用機器學習模型評分精靈來擴充 Synapse SQL 集區中資料的教學課程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8e92ff75bb6a9757c06de3561a385cbcbb7f75ba
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019965"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-synapse-sql-pools"></a>教學課程：適用於 Synapse SQL 集區的機器學習模型評分精靈

了解如何使用預測性機器學習模型，輕鬆地擴充 SQL 集區中的資料。  您資料科學家所建立的模型現在可輕鬆地供資料專業人員進行預測性分析。 Synapse 中的資料專業人員可以直接從 Azure Machine Learning 模型登錄中選取模型，以便在 Synapse SQL 集區中進行部署，並啟動預測來擴充資料。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> - 定型預測性機器學習模型，並在 Azure Machine Learning 模型登錄中註冊模型
> - 使用 SQL 評分精靈來啟動 Synapse SQL 集區中的預測

如果您沒有 Azure 訂用帳戶，請[在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Synapse Analytics 工作區](../get-started-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 您必須是所要使用 ADLS Gen2 檔案系統的**儲存體 Blob 資料參與者**。
- 在您 Synapse Analytics 工作區中的 Synapse SQL 集區。 如需詳細資料，請參閱[建立 Synapse SQL 集區](../quickstart-create-sql-pool-studio.md)。
- 在 Synapse Analytics 工作區中 Azure Machine Learning 已連結的服務。 如需詳細資料，請參閱[在 Synapse 中建立 Azure Machine Learning 已連結的服務](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>在 Azure Machine Learning 中將模型定型

開始之前，請確認您的 **sklearn** 版本是 0.20.3。

在執行筆記本中的所有儲存格之前，請檢查計算執行個體是否正在執行。

![驗證 AML 計算](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. 瀏覽至您的 Azure Machine Learning 工作區。

1. 下載 [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301)。

1. 在 [Azure Machine Learning Studio](https://ml.azure.com) 中啟動 Azure Machine Learning 工作區。

1. 移至 [Notebooks] 然後按一下 [上傳檔案]，選取您已下載的 "Predict NYC Taxi Tips.ipynb" 並上傳檔案。
   ![上傳檔案](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. 上傳並開啟筆記本之後，請按一下 [執行所有儲存格]。

   其中一個儲存格可能會失敗，並要求您向 Azure 進行驗證。 請在儲存格輸出中注意這個項目，並遵循連結並輸入程式碼，藉此在瀏覽器中進行驗證。 重新執行 Notebook。

1. 筆記本會定型 ONNX 模型，並向 MLFlow 註冊。 移至 [模型]，以檢查是否已正確註冊新的模型。
   ![登錄中的模型](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. 執行筆記本也會將測試資料匯出為 CSV 檔案。 將 CSV 檔案下載到您的本機系統。 稍後，您會將 CSV 檔案匯入 SQL 集區，並使用該資料來測試模型。

   CSV 檔案會建立在與您筆記本檔案相同的資料夾中。 如果您未立即看到檔案，請在 [檔案總管] 上按一下 [重新整理]。

   ![CSV 檔案](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>使用 SQL 評分精靈啟動預測

1. 使用 Synapse Studio 開啟 Synapse 工作區。

1. 瀏覽至 [資料] -> [已連結] -> [儲存體帳戶]。 將 `test_data.csv` 上傳到預設儲存體帳戶。

   ![上傳資料](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. 移至 [開發] -> [SQL 指令碼]。 建立新的 SQL 指令碼，以將 `test_data.csv` 載入您的 SQL 集區。

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

   ![將資料載入 SQL 集區](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. 移至 [資料] -> [工作區]。 在 SQL 集區資料表上按一下滑鼠右鍵，以開啟 SQL 評分精靈。 選取 [機器學習] -> [使用現有的模型進行擴充]。

   > [!NOTE]
   > 除非您已針對 Azure Machine Learning 建立連結的服務，否則不會顯示機器學習選項 (請參閱本教學課程開頭的**必要條件**)。

   ![機器學習選項](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. 在下拉式方塊中選取連結的 Azure Machine Learning 工作區。 這會從所選 Azure Machine Learning 工作區的模型登錄中，載入機器學習模型的清單。 目前僅支援 ONNX 模型，因此這只會顯示 ONNX 模型。

1. 選取您剛才定型的模型，然後按一下 [繼續]。

   ![選取 Azure Machine Learning 模型](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. 接下來，將資料表資料行對應至模型輸入，並指定模型輸出。 如果模型是以 MLFlow 格式儲存，且已填入模型簽章，則會根據名稱的相似性，使用邏輯自動為您完成對應。 介面也支援手動對應。

   按一下 **[繼續]** 。

   ![資料表與模型的對應](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. 所產生的 T-SQL 程式碼會包裝在預存程序內。 這就是您需要提供預存程序名稱的原因。 包含中繼資料 (版本、描述等) 的模型二進位檔會從 Azure Machine Learning 實際複製到 SQL 集區資料表。 因此，您必須指定要在哪一個資料表中儲存模型。 您可以選擇 [使用現有的資料表]，也可以 [建立新的資料表]。 完成後，按一下 [部署模型 + 開啟編輯器] 以部署模型並產生 T-SQL 預測指令碼。

   ![建立程序](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. 產生指令碼之後，請按一下 [執行] 來執行評分並取得預測。

   ![執行預測](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>後續步驟

- [快速入門：在 Synapse 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的機器學習功能 (工作區預覽)](what-is-machine-learning.md)
