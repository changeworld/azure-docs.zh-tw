---
title: 教學課程：使用自動化 ML來定型模型
description: 有關如何使用 Apache Spark 和自動化 ML，在 Azure Synapse Analytics 中不使用程式碼訓練機器學習模型的教學課程。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093395"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>教學課程：使用 Apache Spark 和自動化 ML，在 Azure Synapse Analytics 中不使用程式碼訓練機器學習模型的教學課程

了解如何使用 [Azure Machine Learning 中的自動化 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 訓練的新機器學習模型，輕鬆地擴充 Spark 資料表中的資料。  Synapse 中的使用者可以直接在 Azure Synapse 工作區中選取 Spark 資料表，作為用來在無需程式碼體驗中建立機器學習模型的訓練資料集。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> - 使用在 Azure Machine Learning 中使用自動化 ML 的 Azure Synapse Studio 中的無需程式碼體驗，將機器學習模型定型。 您所定型的模型類型取決於您嘗試解決的問題。

如果您沒有 Azure 訂用帳戶，[請在開始前建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

- [Synapse Analytics 工作區](../get-started-create-workspace.md)，並將 ADLS Gen2 儲存體帳戶設定為預設儲存體。 您必須是所要使用 ADLS Gen2 檔案系統的 **儲存體 Blob 資料參與者**。
- 在您 Azure Synapse Analytics 工作區中的 Spark SQL 集區。 如需詳細資訊，請參閱[在 Azure Synapse 中建立 Spark 集區](../quickstart-create-sql-pool-studio.md)。
- Azure Synapse Analytics 工作區中的 Azure Machine Learning 連結服務。 如需詳細資料，請參閱[在 Azure Synapse 中建立 Azure Machine Learning 已連結的服務](quickstart-integrate-azure-machine-learning.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>建立用於訓練資料集的 Spark 資料表

在本教學課程中，您將需要 Spark 資料表。 下列筆記本會建立 Spark 資料表。

1. 下載筆記本 [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. 將筆記本匯入到 Azure Synapse Studio。
![匯入筆記本](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. 選取要使用的 Spark 集區，然後按一下 `Run all`。 執行此筆記本將會從開啟的資料集取得紐約計程車資料，並儲存至您的預設 Spark 資料庫。
![全部執行](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. 當筆記本執行完成之後，系統會在預設 Spark 資料庫下建立新的 Spark 資料表。 移至資料中樞，並尋找名為 `nyc_taxi` 的資料表。
![Spark 資料表](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>啟動自動化 ML 精靈將模型定型

以滑鼠右鍵按一下在上一個步驟中建立的 Spark 資料表。 選取 [機器學習 -> 使用新模型擴充] 並開啟精靈。
![啟動自動化 ML 精靈](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

組態面板隨即出現，系統會要求您提供組態詳細資料，以在 Azure Machine Learning 中建立自動化 ML 實驗執行。 此執行會將多個模型定型，而成功執行的最佳模型將會在 Azure Machine Learning 模型登錄中註冊：

![設定執行步驟 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure Machine Learning 工作區**：建立自動化 ML 實驗執行時，必須要有 Azure Machine Learning 工作區。 您也需要使用[連結服務](quickstart-integrate-azure-machine-learning.md)，將 Azure Synapse 工作區連結至 Azure Machine Learning 工作區。 一旦擁有所有的必要條件，就可以指定要用於此自動化 ML 執行的 Azure Machine Learning 工作區。

- **實驗名稱**：指定實驗名稱。 當您提交自動化 ML 執行時，必須提供一個實驗名稱。 執行的資訊會儲存在 Azure Machine Learning 工作區的該實驗底下。 此體驗預設會建立新的實驗，並產生建議的名稱，但您也可以提供現有實驗的名稱。

- **最佳模型**：從自動化 ML 執行指定最佳模型的名稱。 最佳模型會使用此名稱，並在執行後將其自動儲存在 Azure Machine Learning 模型登錄中。 自動化 ML 執行會建立許多機器學習模型。 根據您將在稍後步驟中選取的主要度量，您可以比較這些模型並選出最佳模型。

- **目標資料行**：此為將接受訓練進行預測的模型。 選擇要預測的資料行。

- **Spark 集區**：要用於自動化 ML 實驗執行的 Spark 集區。 系統會在您指定的集區上執行計算。

- **Spark 組態詳細資料**：除了 Spark 集區，您也可以選擇提供工作階段組態詳細資料。

在本教學課程中，我們選取數值資料行 `fareAmount` 作為目標資料行。

按一下 [繼續]。

## <a name="choose-task-type"></a>選擇工作類型

根據您想了解的內容，選取實驗的機器學習模型類型。 由於選取了 `fareAmount` 作為目標資料行，而這是數值，所以我們會選取「迴歸」。

按一下 [繼續] 以進行其他設定。

![工作類型選取](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>其他組態

如果您選取「分類」或「迴歸」類型，則其他組態為：

- **主要計量**：用來測量模型執行程度的計量。 這是用來比較自動化 ML 執行中所建立之不同模型的計量，並判斷哪一個模型最適合執行。

- 訓練作業時間 (小時)：實驗執行和訓練模型的時間上限，以小時為單位。 請注意，您也可以提供小於 1 的值。 例如 `0.5` 。

- **並行反覆項目上限**：表示平行執行的最大反覆運算數目。

- **ONNX 模型相容性**：若已啟用，由自動化 ML 定型的模型會轉換成 ONNX 格式。 如果要在 Azure Synapse SQL 集區中使用模型評分，此項目特別相關。

這些設定都有您可以自訂的預設值。
![其他組態](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> 請注意，當您選取 [時間序列預測] 時，需要設定更多組態。 預測也不支援 ONNX 模型相容性。

完成所有必要的組態之後，您就可以開始執行自動化 ML。

有兩種方式可以開始在 Azure Synapse 中執行自動化 ML。 如需無程式碼體驗，可以直接選擇 [建立執行]。 如果偏好使用程式碼，可以選取 [在筆記本中開啟]，這可讓您查看建立執行和執行筆記本的程式碼。

### <a name="create-run-directly"></a>直接建立執行

按一下 [開始執行]，直接開始執行自動化 ML。 系統將會出現通知，指出正在啟動自動化 ML 執行。

成功啟動自動化 ML 執行後，您會看到另一個成功的通知。 您也可以按一下 [通知] 按鈕，檢查執行提交的狀態。
按一下成功通知中的連結查看 Azure Machine Learning。
![成功通知](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>使用筆記本建立執行

選取 [在筆記本中開啟] 以產生筆記本。 按一下 [全部執行] 來執行筆記本。
這也讓您有機會將其他設定新增至自動化 ML 執行。

![開啟筆記本](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

成功提交筆記本的執行之後，在筆記本輸出的 Azure Machine Learning 工作區中將會有實驗執行的連結。 您可以按一下連結，以在 Azure Machine Learning 中監視您的自動化 ML 執行。
![筆記本全部執行](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png))

## <a name="next-steps"></a>後續步驟

- [教學課程：Azure Synapse 專用 SQL 集區的機器學習模型評分](tutorial-sql-pool-model-scoring-wizard.md)。
- [快速入門：在 Azure Synapse 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
- [Azure Synapse Analytics 中的機器學習功能](what-is-machine-learning.md)
