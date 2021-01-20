---
title: 教學課程：使用認知服務進行情感分析
description: 如何利用 Synapse 中情感分析認知服務的教學課程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 6a4833cf0d73939e01fd3e3e7263c6cba3c0a28a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222185"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>教學課程：使用認知服務進行情感分析 (預覽)

在本教學課程中，您將了解如何使用[認知服務](../../cognitive-services/index.yml)，輕鬆地擴充 Azure Synapse 中的資料。 我們將使用[文字分析](../../cognitive-services/text-analytics/index.yml)功能來執行情感分析。 Azure Synapse 中的使用者可以直接選取包含文字資料行的資料表，以擴充各種情緒。 這些情緒可以是正面、負面、混合或中性，而且也會傳回機率。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> - 取得 Spark 資料表資料集的步驟，其中包含用於情感分析的文字資料行。
> - 使用 Azure Synapse 中的精靈體驗，利用文字分析認知服務擴充資料。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 您必須是所要使用 ADLS Gen2 檔案系統的 **儲存體 Blob 資料參與者**。
- 在您 Azure Synapse Analytics 工作區中的 Spark SQL 集區。 如需詳細資訊，請參閱[在 Azure Synapse 中建立 Spark 集區](../quickstart-create-sql-pool-studio.md)。
- 您也必須先完成本教學課程中所述的預先設定步驟，才能使用本教學課程。 [在 Azure Synapse 中設定認知服務](tutorial-configure-cognitive-services-synapse.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>建立 Spark 資料表

在本教學課程中，您將需要 Spark 資料表。

1. 下載下列 CSV 檔案，其中包含文字分析的資料集：[FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. 將檔案上傳到您的 Azure Synapse ADLSGen2 儲存體帳戶。
![上傳資料](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. 以滑鼠右鍵按一下檔案，然後選取 **新增 Notebook > 建立 Spark 資料表**，從 .csv 檔案建立 Spark 資料表。
![建立 Spark 資料表](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. 為程式碼資料格中的資料表命名，然後在 Spark 集區上執行筆記本。 請記得設定 "header = True"。
![執行 Notebook](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>啟動認知服務精靈

1. 以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 選取 [機器學習 -> 使用現有模型擴充] 並開啟精靈。
![啟動評分精靈](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. 組態面板隨即出現，而且系統會要求您選取認知服務模型。 選取 [文字分析 - 情感分析]。

![啟動評分精靈 - 步驟1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>提供驗證詳細資料

若要向認知服務進行驗證，您必須參考要在 Key Vault 中使用的祕密。 下列輸入取決於您在執行此步驟之前應完成的[必要條件步驟](tutorial-configure-cognitive-services-synapse.md)。

- **Azure 訂用帳戶**：選取金鑰保存庫所屬的 Azure 訂用帳戶。
- **認知服務帳戶**：這是您即將連線的文字分析資源。
- **Azure Key Vault 連結服務**：作為必要條件步驟的一部分，您已建立文字分析資源的連結服務。 請在此選取。
- **祕密名稱**：這是金鑰保存庫中的祕密名稱，其中包含要向您的認知服務資源驗證的金鑰。

![提供 Azure Key Vault 詳細資料](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>設定情感分析

接下來，您必須設定情感分析。 請選取下列詳細資料：
- **語言**：選取您想要執行情感分析之文字的語言。 選取 [EN]。
- **文字資料行**：這是資料集中的文字資料行，您想分析此資料集以判斷情緒。 選取資料表資料行 **註解**。

完成後，請按一下 [開啟 Notebook]。 系統會使用 PySpark 程式碼為您產生筆記本，以使用 Azure 認知服務執行情感分析。

![設定情感分析](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>開啟筆記本並執行

您剛開啟的筆記本是使用 [mmlspark 程式庫](https://github.com/Azure/mmlspark) 來連線到認知服務。

您所提供的 Azure Key Vault 詳細資料，可讓您從這項體驗安全地參考祕密，而無洩漏之虞。

您現在可以 **執行全部** 資料格，使用情緒擴充資料。 情緒將會以正面/負面/中性/混合的方式傳回，而且您也會取得每個情緒的機率。 深入了解[認知服務- 情感分析](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md)。

![執行情感分析](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>後續步驟
- [教學課程：使用 Azure 認知服務的異常偵測](tutorial-cognitive-services-sentiment.md)
- [教學課程：Azure Synapse 專用 SQL 集區的機器學習模型評分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)