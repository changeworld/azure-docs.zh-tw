---
title: 教學課程：使用認知服務的異常偵測
description: 如何利用 Synapse 中異常偵測認知服務的教學課程
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e7b914d459d2452704f93987ce1bf91bfba988c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222202"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>教學課程：使用認知服務的異常偵測 (預覽)

在本教學課程中，您將了解如何使用[認知服務](../../cognitive-services/index.yml)，輕鬆地擴充 Azure Synapse 中的資料。 我們將使用[異常偵測器](../../cognitive-services/anomaly-detector/index.yml)來執行異常偵測。 Azure Synapse 中的使用者可以直接選取資料表來進行異常偵測。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> - 取得包含時間序列資料之 Spark 資料表資料集的步驟。
> - 使用 Azure Synapse 中的精靈體驗，利用異常偵測器認知服務擴充資料。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 您必須是所要使用 ADLS Gen2 檔案系統的 **儲存體 Blob 資料參與者**。
- 在您 Azure Synapse Analytics 工作區中的 Spark SQL 集區。 如需詳細資訊，請參閱[在 Azure Synapse 中建立 Spark 集區](../quickstart-create-sql-pool-studio.md)。
- 您也必須先完成本教學課程中所述的預先設定步驟，才能使用本教學課程。 [在 Azure Synapse 中設定認知服務](tutorial-configure-cognitive-services-synapse.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>建立 Spark 資料表

在本教學課程中，您將需要 Spark 資料表。

1. 下載下列包含程式碼的筆記本檔案，以產生 Spark 資料表：[prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. 將檔案上傳至 Azure Synapse 工作區。
![上傳筆記本](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. 開啟筆記本檔案，然後選擇 **全部執行** 資料格。
![建立 Spark 資料表](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. 名為 **anomaly_detector_testing_data** 的 Spark 資料表現在應該會出現在預設的 Spark 資料庫中。

## <a name="launch-cognitive-services-wizard"></a>啟動認知服務精靈

1. 以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 選取 [機器學習 -> 使用現有模型擴充] 並開啟精靈。
![啟動評分精靈](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. 組態面板隨即出現，而且系統會要求您選取認知服務模型。 選取異常偵測器。

![啟動評分精靈 - 步驟1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>提供驗證詳細資料

若要向認知服務進行驗證，您必須參考要在 Key Vault 中使用的祕密。 下列輸入取決於您在執行此步驟之前應完成的[必要條件步驟](tutorial-configure-cognitive-services-synapse.md)。

- **Azure 訂用帳戶**：選取金鑰保存庫所屬的 Azure 訂用帳戶。
- **認知服務帳戶**：這是您即將連線的文字分析資源。
- **Azure Key Vault 連結服務**：作為必要條件步驟的一部分，您已建立文字分析資源的連結服務。 請在此選取。
- **祕密名稱**：這是金鑰保存庫中的祕密名稱，其中包含要向您的認知服務資源驗證的金鑰。

![提供 Azure Key Vault 詳細資料](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>設定異常偵測

接下來，您必須設定異常偵測。 請提供下列詳細資料：

- 細微性：資料的取樣率。 例如，如果您的資料有每分鐘的值，則細微性會以分鐘計。 選擇 **每月** 

- Timestamp：表示時間序列的資料行。 選擇資料行 **時間戳記**

- 時間序列值：代表時間戳記資料行所指定之序列值的資料行。 選擇資料行 **值**

- 群組：將序列分組的資料行。 也就是說，在此資料行中具有相同值的所有資料列都應該形成一個時間序列。 選擇資料行 **群組**

完成後，請選取 [開啟筆記本]。 系統會使用 PySpark 程式碼為您產生筆記本，以使用 Azure 認知服務執行異常偵測。

![設定異常偵測器](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>開啟筆記本並執行

您剛開啟的筆記本是使用 [mmlspark 程式庫](https://github.com/Azure/mmlspark) 來連線到認知服務。

您所提供的 Azure Key Vault 詳細資料，可讓您從這項體驗安全地參考祕密，而無洩漏之虞。

您現在可以 **全部執行** 資料格，以執行異常偵測。 深入了解[認知服務 - 異常偵測器](../../cognitive-services/anomaly-detector/index.yml)。

![執行異常偵測](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>後續步驟

- [教學課程：使用認知服務進行情感分析](tutorial-cognitive-services-sentiment.md)
- [教學課程：Azure Synapse 專用 SQL 集區的機器學習模型評分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)