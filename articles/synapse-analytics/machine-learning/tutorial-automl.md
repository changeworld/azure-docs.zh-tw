---
title: 教學課程：使用自動化機器學習定型模型
description: 有關如何在 Azure Synapse Analytics 中不使用程式碼將機器學習模型定型的教學課程。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943519"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>教學課程：不使用程式碼將機器學習模型定型

您可以使用 [自動化機器學習](../../machine-learning/concept-automated-ml.md)定型的新機器學習模型，擴充 Spark 資料表中的資料。 在 Azure Synapse Analytics 中，您可以直接在工作區中選取 Spark 資料表，作為用來在無需程式碼體驗中建立機器學習模型的訓練資料集。

在本教學課程中，您將瞭解如何在 Synapse Studio 中使用無程式碼體驗來定型機器學習模型。 Synapse Studio 是 Azure Synapse Analytics 的一項功能。 

您將在 Azure Machine Learning 中使用自動化機器學習，而不是手動編碼體驗。 您定型的模型類型取決於您嘗試解決的問題。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md)。 確定它具有設定為預設儲存體的 Azure Data Lake Storage Gen2 儲存體帳戶。 針對您使用的 Data Lake Storage Gen2 檔案系統，請確定您是 *儲存體 Blob 資料參與者*。
- 在您 Azure Synapse Analytics 工作區中的 Apache Spark 集區。 如需詳細資訊，請參閱 [快速入門：使用 Synapse Studio 建立專用的 SQL 集](../quickstart-create-sql-pool-studio.md)區。
- Azure Synapse Analytics 工作區中的 Azure Machine Learning 連結服務。 如需詳細資訊，請參閱[快速入門：在 Azure Synapse Analytics 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-spark-table-for-the-training-dataset"></a>建立訓練資料集的 Spark 資料表

在本教學課程中，您需要使用 Spark 資料表。 下列筆記本會建立一個：

1. 下載筆記本 [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)。

1. 將筆記本匯入 Synapse Studio。
![Azure Synapse Analytics 的螢幕擷取畫面，其中已反白顯示 [匯入] 選項。](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 選取您要使用的 Spark 集區，然後選取 [ **全部執行**]。 此步驟會從開啟的資料集取得紐約計程車資料，並將資料儲存至您的預設 Spark 資料庫。
![Azure Synapse Analytics 的螢幕擷取畫面，其中反白顯示 [全部執行] 和 [Spark 資料庫]。](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. 當筆記本執行完成之後，您會在預設 Spark 資料庫下看到新的 Spark 資料表。 從 **資料** 中，尋找名為 **nyc_taxi** 的資料表。
![[Azure Synapse Analytics 資料] 索引標籤的螢幕擷取畫面，其中反白顯示新的資料表。](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>開啟自動化機器學習服務嚮導

若要開啟嚮導：

1. 以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 然後選取[  >  **使用新模型** 擴充 Machine Learning]。
![Spark 資料表的螢幕擷取畫面，其中包含 Machine Learning，並反白顯示新的模型擴充。](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. 提供在 Azure Machine Learning 中建立自動化機器學習實驗執行的設定詳細資料。 這項執行會訓練多個模型。 成功執行的最佳模型會在 Azure Machine Learning 模型登錄中註冊。

   ![定型機器學習模型的設定規格螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning 工作區**：建立自動化機器學習實驗執行時，必須要有 Azure Machine Learning 工作區。 您也需要使用[連結服務](quickstart-integrate-azure-machine-learning.md)，將 Azure Synapse Analytics 工作區連結至 Azure Machine Learning 工作區。 完成所有必要條件之後，您可以指定要用於此自動化執行的 Azure Machine Learning 工作區。

    - **實驗名稱**：指定實驗名稱。 當您提交自動化機器學習執行時，必須提供一個實驗名稱。 執行的資訊會儲存在 Azure Machine Learning 工作區的該實驗底下。 此體驗預設會建立新的實驗，並產生建議的名稱，但您也可以提供現有實驗的名稱。

    - **最佳模型名稱**：指定自動執行的最佳模型名稱。 最佳模型會使用此名稱，並在執行後將其自動儲存在 Azure Machine Learning 模型登錄中。 自動化機器學習執行會建立許多機器學習模型。 根據您將在稍後步驟中選取的主要度量，您可以比較這些模型並選出最佳模型。

    - **目標資料行**：此為將接受訓練進行預測的模型。 選擇要預測的資料行。 (在本教學課程中，我們選取數值資料行 `fareAmount` 作為目標資料行。)

    - **Spark 集** 區：指定要用於自動化實驗執行的 spark 集區。 計算會在您指定的集區上執行。

    - **Spark 設定詳細資料**：除了 spark 集區之外，您還可以選擇提供會話設定詳細資料。

1. 選取 [繼續]。

## <a name="choose-a-task-type"></a>選擇工作類型

根據您想了解的內容，選取實驗的機器學習模型類型。 因為 `fareAmount` 是目標資料行，而且是數值，所以建議您在這裡選取 [ **回歸** ]。 然後選取 [繼續]  。

![使用新模型擴充的螢幕擷取畫面，反白顯示迴歸。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>其他組態

如果您在上一節中選取 [ **回歸** ] 或 [ **分類** ] 作為模型類型，則可以使用下列設定：

- **主要度量**：輸入度量來測量模型的執行效能。 您可以使用此計量來比較自動執行中所建立的不同模型，並判斷哪一個模型執行的效能最佳。

- **定型作業時間 (小時)**：指定實驗執行和定型模型的最大時間量（以小時為單位）。 請注意，您也可以提供小於1的值 (例如 **0.5**) 。

- **並行反覆運算的最大** 值：選擇平行執行的反覆運算次數上限。

- **ONNX 模型相容性**：如果啟用此選項，自動化機器學習定型的模型會轉換成 ONNX 格式。 如果要在 Azure Synapse Analytics SQL 集區中使用模型評分，此項目特別相關。

這些設定都有您可以自訂的預設值。
![設定回歸模型之其他設定的螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

完成所有必要組態之後，您就可以開始執行自動化。 您可以選擇 [建立執行]，這會直接開始執行，而不需要程式碼。 或者，如果您偏好使用程式碼，可以選取 [在筆記本中開啟]。 此選項可讓您查看建立執行的程式碼，然後執行筆記本。

>[!NOTE]
>如果您在上一節中選取 **時間序列預測** 做為模型類型，您必須進行其他設定。 預測也不支援 ONNX 模型相容性。

### <a name="create-a-run-directly"></a>直接建立執行

若要直接啟動自動化機器學習執行，請選取 [開始執行]。 您會到正在啟動執行的通知。 然後您會看到另一個指出成功的通知。 您也可以選取通知中的連結，檢查 Azure Machine Learning 中的狀態。
![通知成功的螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>使用筆記本建立執行

選取 [在筆記本中開啟] 以產生筆記本。 然後，選取 [全部執行]。 這也讓您有機會將設定新增至您的自動化機器學習執行。

![筆記本的螢幕擷取畫面，其中全部反白顯示 [執行]。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

成功提交執行之後，您會在筆記本輸出的 Azure Machine Learning 工作區中看到實驗執行的連結。 選取連結，以在 Azure Machine Learning 中監視您的自動化機器學習執行。
![已反白顯示連結之 Azure Synapse Analytics 的螢幕擷取畫面。 ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)) 

## <a name="next-steps"></a>後續步驟

- [教學課程：專用 SQL 集區的機器學習模型評分精靈 (預覽)](tutorial-sql-pool-model-scoring-wizard.md)
- [快速入門：在 Azure Synapse Analytics 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)