---
title: 教學課程：使用認知服務進行情感分析
description: 瞭解如何在 Azure Synapse Analytics 中使用認知服務進行情感分析
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943711"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>教學課程：使用認知服務進行情感分析 (預覽版) 

在本教學課程中，您將瞭解如何輕鬆地使用 [Azure 認知服務](../../cognitive-services/index.yml)來豐富 Azure Synapse Analytics 中的資料。 您將使用 [文字分析](../../cognitive-services/text-analytics/index.yml) 功能來執行情感分析。 

Azure Synapse 中的使用者可以直接選取包含文字資料行的資料表，以使用情緒擴充。 這些情緒可以是正數、負數、混合或中性。 也會傳回機率。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> - 取得包含情感分析之文字資料行之 Spark 資料表資料集的步驟。
> - 使用 Azure Synapse 中的 wizard 體驗，利用認知服務中的文字分析來擴充資料。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md) ，具有設定為預設儲存體的 Azure Data Lake Storage Gen2 儲存體帳戶。 您必須是您所使用 Data Lake Storage Gen2 檔案系統的 *儲存體 Blob 資料參與者* 。
- 在您 Azure Synapse Analytics 工作區中的 Spark SQL 集區。 如需詳細資訊，請參閱[在 Azure Synapse 中建立 Spark 集區](../quickstart-create-sql-pool-studio.md)。
- 教學課程中所述的預先設定步驟：在 [Azure Synapse 中設定認知服務](tutorial-configure-cognitive-services-synapse.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-spark-table"></a>建立 Spark 資料表

您將需要 Spark 資料表來進行本教學課程。

1. 下載 [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) 檔案，其中包含適用于文字分析的資料集。 

1. 在 Data Lake Storage Gen2 中，將檔案上傳至您的 Azure Synapse 儲存體帳戶。
  
   ![顯示用於上傳資料之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. 以滑鼠右鍵按一下檔案，然後選取 [**新增筆記本**  >  **建立 spark 表格**]，從 .csv 檔案建立 Spark 資料表。

   ![顯示用於建立 Spark 資料表之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. 為程式碼資料格中的資料表命名，然後在 Spark 集區上執行筆記本。 請記得設定 `header=True` 。

   ![顯示執行筆記本的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>開啟認知服務嚮導

1. 以滑鼠右鍵按一下在先前程式中建立的 Spark 資料表。 選取  >  [**使用現有的模型** 擴充] Machine Learning 開啟嚮導。

   ![顯示開啟評分嚮導之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. [設定] 面板隨即出現，系統會要求您選取認知服務模型。 選取 [ **文字分析-情感分析**]。

   ![顯示認知服務模型選取範圍的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>提供驗證詳細資料

若要驗證認知服務，您必須參考金鑰保存庫的密碼。 下列輸入取決於您應該在此點之前完成的必要條件 [步驟](tutorial-configure-cognitive-services-synapse.md) 。

- **Azure 訂** 用帳戶：選取您的金鑰保存庫所屬的 azure 訂用帳戶。
- **認知服務帳戶**：輸入您要連接的文字分析資源。
- **Azure Key Vault 連結服務**：作為必要條件步驟的一部分，您已為您的文字分析資源建立連結的服務。 請在這裡選取它。
- **秘密名稱**：輸入金鑰保存庫中的秘密名稱，其中包含要向認知服務資源驗證的金鑰。

![顯示金鑰保存庫驗證詳細資料的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>設定情感分析

接下來，設定情感分析。 選取下列詳細資料：
- **語言**：選取 [ **英文** ] 作為您想要執行情感分析之文字的語言。
- **文字資料行**：選取 [ **批註] (字串)** 做為您想要分析的資料集中的文字資料行，以判斷情感。

當您完成時，請選取 [ **開啟筆記本**]。 這會為您產生具有 PySpark 程式碼的筆記本，此程式碼會使用 Azure 認知服務執行情感分析。

![顯示用於設定情感分析之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>執行 Notebook

您剛剛開啟的筆記本會使用 [mmlspark 程式庫](https://github.com/Azure/mmlspark) 來連線至認知服務。 您所提供的 Azure Key Vault 詳細資料，可讓您安全地從此體驗參考您的秘密，而不會洩漏這些秘密。

您現在可以使用情緒來執行所有資料格，以豐富資料。 選取 [ **全部執行**]。 

情緒會以 **正面**、 **負面**、 **中性** 或 **混合** 的方式傳回。 您也會取得每個情感的機率。 [深入瞭解認知服務中的情感分析](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md)。

![顯示情感分析的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>後續步驟
- [教學課程：使用 Azure 認知服務的異常偵測](tutorial-cognitive-services-sentiment.md)
- [教學課程： Azure Synapse 專用 SQL 集區中的機器學習模型計分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)