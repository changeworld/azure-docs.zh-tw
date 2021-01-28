---
title: 教學課程：使用認知服務的異常偵測
description: 瞭解如何在 Azure Synapse Analytics 中使用認知服務進行異常偵測。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943507"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>教學課程：使用認知服務 (預覽) 的異常偵測

在本教學課程中，您將瞭解如何輕鬆地使用 [Azure 認知服務](../../cognitive-services/index.yml)來豐富 Azure Synapse Analytics 中的資料。 您將會使用 [異常](../../cognitive-services/anomaly-detector/index.yml) 偵測器來找出異常狀況。 Azure Synapse 中的使用者可以直接選取資料表來進行異常偵測。

本教學課程涵蓋下列項目：

> [!div class="checklist"]
> - 取得包含時間序列資料之 Spark 資料表資料集的步驟。
> - 使用 Azure Synapse 中的 wizard 體驗，在認知服務中使用異常偵測器來擴充資料。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md) ，具有設定為預設儲存體的 Azure Data Lake Storage Gen2 儲存體帳戶。 您必須是您所使用 Data Lake Storage Gen2 檔案系統的 *儲存體 Blob 資料參與者* 。
- 在您 Azure Synapse Analytics 工作區中的 Spark SQL 集區。 如需詳細資訊，請參閱[在 Azure Synapse 中建立 Spark 集區](../quickstart-create-sql-pool-studio.md)。
- 在 [Azure Synapse 中設定認知服務](tutorial-configure-cognitive-services-synapse.md) 教學課程中完成預先設定步驟。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-spark-table"></a>建立 Spark 資料表

在本教學課程中，您需要 Spark 資料表。

1. 下載下列包含程式碼的筆記本檔案，以產生 Spark 資料表： [prepare_anomaly_detector_data .ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)。

1. 將檔案上傳至 Azure Synapse 工作區。

   ![顯示用於上傳筆記本之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. 開啟筆記本檔案，然後選取 [ **全部執行** ] 以執行所有儲存格。

   ![顯示用於建立 Spark 資料表之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. 名為 **anomaly_detector_testing_data** 的 Spark 資料表現在應該會出現在預設的 Spark 資料庫中。

## <a name="open-the-cognitive-services-wizard"></a>開啟認知服務嚮導

1. 以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 選取  >  [**使用現有的模型** 擴充] Machine Learning 開啟嚮導。

   ![顯示開啟評分嚮導之選項的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. [設定] 面板隨即出現，系統會要求您選取認知服務模型。 選取 **異常** 偵測器。

   ![顯示將異常偵測器選取為模型的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>提供驗證詳細資料

若要驗證認知服務，您必須參考金鑰保存庫中的秘密。 下列輸入取決於您在此時間點之前應完成的必要條件 [步驟](tutorial-configure-cognitive-services-synapse.md) 。

- **Azure 訂** 用帳戶：選取您的金鑰保存庫所屬的 azure 訂用帳戶。
- **認知服務帳戶**：輸入您要連接的文字分析資源。
- **Azure Key Vault 連結服務**：作為必要條件步驟的一部分，您已為您的文字分析資源建立連結的服務。 請在這裡選取它。
- **秘密名稱**：輸入金鑰保存庫中的秘密名稱，其中包含要向認知服務資源驗證的金鑰。

![顯示金鑰保存庫驗證詳細資料的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>設定異常偵測器

請提供下列詳細資料來設定異常偵測器：

- **細微性**：取樣資料的速率。 選擇 [ **每月**]。 

- **Timestamp 資料行**：代表數列時間的資料行。 選擇 **timestamp (字串)**。

- **時間序列值資料行**：代表時間戳記資料行所指定之數列值的資料行。 選擇 [ **值 (雙精度)**。

- 群組資料 **行**：群組數列的資料行。 也就是說，在此資料行中具有相同值的所有資料列都應該形成一個時間序列。 選擇 [ **群組 (字串])**。

當您完成時，請選取 [ **開啟筆記本**]。 這會使用 PySpark 程式碼為您產生筆記本，以使用 Azure 認知服務來偵測異常狀況。

![顯示異常偵測器設定詳細資料的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>執行 Notebook

您剛剛開啟的筆記本會使用 [mmlspark 程式庫](https://github.com/Azure/mmlspark) 來連線至認知服務。 您所提供的 Azure Key Vault 詳細資料，可讓您安全地從此體驗參考您的秘密，而不會洩漏這些秘密。

您現在可以執行所有資料格來執行異常偵測。 選取 [ **全部執行**]。 [深入瞭解認知服務中的異常](../../cognitive-services/anomaly-detector/index.yml)偵測器。

![顯示異常偵測的螢幕擷取畫面。](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>後續步驟

- [教學課程：使用認知服務進行情感分析](tutorial-cognitive-services-sentiment.md)
- [教學課程： Azure Synapse 專用 SQL 集區中的機器學習模型計分](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)
