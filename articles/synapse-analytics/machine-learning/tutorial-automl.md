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
ms.openlocfilehash: e219531a88787f19197a2e8c2a80040497c6dc1e
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901414"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>教學課程：不使用程式碼將機器學習模型定型

您可以使用 [自動化機器學習](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)定型的新機器學習模型，擴充 Spark 資料表中的資料。 在 Azure Synapse Analytics 中，您可以直接在工作區中選取 Spark 資料表，作為用來在無需程式碼體驗中建立機器學習模型的訓練資料集。

在本教學課程中，您將了解如何在 Azure Synapse Analytics studio 中不使用程式碼將機器學習模型定型。 您會在 Azure Machine Learning 中使用自動化機器學習，無需手動編碼。 您所定型的模型類型取決於您嘗試解決的問題。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Azure Synapse Analytics 工作區](../get-started-create-workspace.md)。 確定其具有下列儲存體帳戶，並設定為預設儲存體：Azure Data Lake Storage Gen2。 針對您使用的 Data Lake Storage Gen2 檔案系統，請確定您是 **儲存體 Blob 資料參與者**。
- 在您 Azure Synapse Analytics 工作區中的 Apache Spark 集區。 如需詳細資訊，請參閱[快速入門：使用 Azure Synapse Analytics Studio 建立專屬 SQL 集區](../quickstart-create-sql-pool-studio.md)。
- Azure Synapse Analytics 工作區中的 Azure Machine Learning 連結服務。 如需詳細資訊，請參閱[快速入門：在 Azure Synapse Analytics 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-spark-table-for-training-dataset"></a>建立用於訓練資料集的 Spark 資料表

在本教學課程中，您需要使用 Spark 資料表。 下列筆記本會建立一個 Spark 資料表。

1. 下載筆記本 [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)。

1. 將筆記本匯入到 Azure Synapse Analytics Studio。
![Azure Synapse Analytics 的螢幕擷取畫面，其中反白顯示 [匯入] 選項。](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 選取要使用的 Spark 集區，然後選取 [全部執行]。 這將會從開啟的資料集取得紐約計程車資料，並儲存至您的預設 Spark 資料庫。
![Azure Synapse Analytics 的螢幕擷取畫面，其中反白顯示 [全部執行] 和 [Spark 資料庫]。](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. 當筆記本執行完成之後，您會在預設 Spark 資料庫下看到新的 Spark 資料表。 從 **資料** 中，尋找名為 **nyc_taxi** 的資料表。
![Azure Synapse Analytics Data 索引標籤的螢幕擷取畫面，其中反白顯示新資料表。](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>啟動自動化機器學習精靈

其做法如下：

1. 以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 要開啟精靈，請選取 [機器學習] > [使用新模型擴充]。
![Spark 資料表的螢幕擷取畫面，其中包含 Machine Learning，並反白顯示新的模型擴充。](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. 接著，您可以在 Azure Machine Learning 中提供建立自動化機器學習實驗執行的組態詳細資料。 此執行會將多個模型定型，而成功執行的最佳模型將會在 Azure Machine Learning 模型登錄中註冊。

   ![使用新的模型組態規格擴充的螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning 工作區**：建立自動化機器學習實驗執行時，必須要有 Azure Machine Learning 工作區。 您也需要使用[連結服務](quickstart-integrate-azure-machine-learning.md)，將 Azure Synapse Analytics 工作區連結至 Azure Machine Learning 工作區。 一旦擁有所有的必要條件，就可以指定要用於此自動化執行的 Azure Machine Learning 工作區。

    - **實驗名稱**：指定實驗名稱。 當您提交自動化機器學習執行時，必須提供一個實驗名稱。 執行的資訊會儲存在 Azure Machine Learning 工作區的該實驗底下。 此體驗預設會建立新的實驗，並產生建議的名稱，但您也可以提供現有實驗的名稱。

    - **最佳模型**：從自動化執行指定最佳模型的名稱。 最佳模型會使用此名稱，並在執行後將其自動儲存在 Azure Machine Learning 模型登錄中。 自動化機器學習執行會建立許多機器學習模型。 根據您將在稍後步驟中選取的主要度量，您可以比較這些模型並選出最佳模型。

    - **目標資料行**：此為將接受訓練進行預測的模型。 選擇要預測的資料行。 (在本教學課程中，我們選取數值資料行 `fareAmount` 作為目標資料行。)

    - **Spark 集區**：要用於自動化實驗執行的 Spark 集區。 系統會在您指定的集區上執行計算。

    - **Spark 組態詳細資料**：除了 Spark 集區，您也可以選擇提供工作階段組態詳細資料。

1. 選取 [繼續]。

## <a name="choose-task-type"></a>選擇工作類型

根據您想了解的內容，選取實驗的機器學習模型類型。 由於 `fareAmount` 是目標資料行而且是數值，請在這裡選取 [迴歸]。 然後選取 [繼續]  。

![使用新模型擴充的螢幕擷取畫面，反白顯示迴歸。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>其他組態

如果您在上一節中選取 [迴歸] 或 [分類] 作為模型類型，則可使用下列組態：

- **主要計量**：用來測量模型執行程度的計量。 這是用來比較自動化執行中所建立之不同模型的計量，並判斷哪一個模型最適合執行。

- 訓練作業時間 (小時)：實驗執行和訓練模型的時間上限，以小時為單位。 請注意，您也可以提供小於 1 的值 (例如 `0.5`)。

- **並行反覆項目上限**：表示平行執行的最大反覆運算數目。

- **ONNX 模型相容性**：如果啟用此選項，自動化機器學習定型的模型會轉換成 ONNX 格式。 如果要在 Azure Synapse Analytics SQL 集區中使用模型評分，此項目特別相關。

這些設定都有您可以自訂的預設值。
![以新模型額外組態擴充的螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

完成所有必要組態之後，您就可以開始執行自動化。 您可以選擇 [建立執行]，這會直接開始執行，而不需要程式碼。 或者，如果您偏好使用程式碼，可以選取 [在筆記本中開啟]。 此選項可讓您查看建立執行和執行筆記本的程式碼。

>[!NOTE]
>如果您在上一節中選取 [時間序列預測] 作為模型類型，您必須設定其他組態。 預測也不支援 ONNX 模型相容性。

### <a name="create-run-directly"></a>直接建立執行

若要直接啟動自動化機器學習執行，請選取 [開始執行]。 您會到正在啟動執行的通知。 然後您會看到另一個表示執行成功的通知。 您也可以選取通知中的連結，檢查 Azure Machine Learning 中的狀態。
![通知成功的螢幕擷取畫面。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>使用筆記本建立執行

選取 [在筆記本中開啟] 以產生筆記本。 然後，選取 [全部執行]。 這也讓您有機會將其他設定新增至自動化機器學習執行。

![Notebook 的螢幕擷取畫面，反白顯示 [全部執行]。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

成功提交執行之後，您會在筆記本輸出的 Azure Machine Learning 工作區中看到實驗執行的連結。 選取連結，以在 Azure Machine Learning 中監視您的自動化機器學習執行。
![Azure Synapse Analytics 的螢幕擷取畫面，反白顯示連結。](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png))

## <a name="next-steps"></a>後續步驟

- [教學課程：專用 SQL 集區的機器學習模型評分精靈 (預覽)](tutorial-sql-pool-model-scoring-wizard.md)
- [快速入門：在 Azure Synapse Analytics 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)
