---
title: '在設計工具中記錄計量 (預覽) '
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 設計工具實驗。 使用執行 Python 腳本模組來啟用記錄，並在 studio 中查看記錄的結果。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0d8d9f598da41b2bd39369e063200f5445ba740a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554762"
---
# <a name="enable-logging-in-azure-machine-learning-designer-preview-pipelines"></a>在 Azure Machine Learning 設計工具中啟用記錄功能 (預覽) 管線
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何將記錄程式碼加入至設計工具管線。 您也會瞭解如何使用 Azure Machine Learning studio web 入口網站來查看這些記錄。

如需使用 SDK 撰寫體驗來記錄計量的詳細資訊，請參閱[監視 AZURE ML 實驗執行和計量](how-to-track-experiments.md)。

## <a name="enable-logging-with-execute-python-script"></a>使用執行 Python 腳本啟用記錄

使用 [__執行 Python 腳本__] 模組，在設計工具管線中啟用記錄功能。 雖然您可以使用此工作流程來記錄任何值，但從__評估模型__模組記錄計量以追蹤執行之間的模型效能特別有用。

下列範例示範如何使用評估模型和執行 Python 腳本模組，記錄兩個定型模型的平均平方誤差。

1. 將 [__執行 Python 腳本__] 模組連接到 [__評估模型__] 模組的輸出。

    ![將執行 Python 指令碼模組連線至評估模型模組](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 將下列程式碼貼入 [__執行 Python 腳本__編輯器] 中，記錄定型模型的平均絕對錯誤。 您可以使用類似的模式，在設計工具中記錄任何其他值：

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
此程式碼會使用 Azure Machine Learning Python SDK 來記錄值。 它會使用 Run. get_coNtext ( # A1 來取得目前執行的內容。 接著，它會使用 ( # A1 方法，將值記錄到該內容中。 它會使用 `parent` 將值記錄到父管線執行，而不是模組執行。

如需如何使用 Python SDK 來記錄值的詳細資訊，請參閱[在 AZURE ML 訓練回合中啟用記錄](how-to-track-experiments.md)。

## <a name="view-logs"></a>檢視記錄

管線執行完成之後，您可以在 [實驗] 頁面中看到*Mean_Absolute_Error* 。

1. 流覽至 [**實驗**] 區段。
1. 選取您的實驗。
1. 在您要查看的實驗中選取 [執行]。
1. 選取 [計量]。

    ![在 studio 中查看執行計量](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在設計工具中使用記錄。 如需後續步驟，請參閱下列相關文章：

* 瞭解如何疑難排解設計工具管線，請參閱[Debug & 針對 ML 管線進行疑難排解](how-to-debug-pipelines.md#azure-machine-learning-designer-preview)。
* 瞭解如何使用 Python SDK 在 SDK 撰寫體驗中記錄計量，請參閱[在 AZURE ML 訓練回合中啟用記錄](how-to-track-experiments.md)。
