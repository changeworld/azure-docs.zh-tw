---
title: 設計工具中的記錄計量
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 設計工具實驗。 使用 [執行 Python 腳本] 模組啟用記錄，並在 studio 中查看記錄的結果。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065247"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>啟用 Azure Machine Learning 設計工具管線中的記錄


在本文中，您將瞭解如何將記錄程式碼新增至設計工具管線。 您也會瞭解如何使用 Azure Machine Learning studio 入口網站來查看這些記錄。

如需使用 SDK 撰寫體驗來記錄計量的詳細資訊，請參閱 [監視 AZURE ML 實驗執行和計量](how-to-track-experiments.md)。

## <a name="enable-logging-with-execute-python-script"></a>使用執行 Python 腳本啟用記錄

使用 [ [執行 Python 腳本](./algorithm-module-reference/execute-python-script.md) ] 模組，在設計工具管線中啟用記錄功能。 雖然您可以使用此工作流程來記錄任何值，但是從 __評估模型__ 模組記錄計量來追蹤跨回合的模型效能特別有用。

下列範例示範如何使用 [評估模型] 和 [執行 Python 腳本] 模組，記錄兩個定型模型的 mean 平方誤差。

1. 將「 __執行 Python 腳本__ 」模組連接到「 __評估模型__ 」模組的輸出。

    ![將執行 Python 指令碼模組連線至評估模型模組](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 將下列程式碼貼入 [ __執行 Python 腳本__ 程式碼編輯器] 中，以記錄定型模型的平均絕對誤差。 您可以使用類似的模式來記錄設計工具中的任何其他值：

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
此程式碼會使用 Azure Machine Learning Python SDK 來記錄值。 它使用 Run.get_coNtext ( # A1 來取得目前執行的內容。 接著，它會使用 ( # A1 方法，將值記錄到該內容。 它會使用 `parent` 將值記錄至父管線執行，而不是執行模組。

如需如何使用 Python SDK 記錄值的詳細資訊，請參閱 [在 AZURE ML 定型回合中啟用記錄](how-to-track-experiments.md)。

## <a name="view-logs"></a>檢視記錄

管線執行完成之後，您可以在 [實驗] 頁面中看到 *Mean_Absolute_Error* 。

1. 流覽至 [ **實驗** ] 區段。
1. 選取您的實驗。
1. 在您想要查看的實驗中選取 [執行]。
1. 選取 [計量]。

    ![在 studio 中查看執行計量](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在設計工具中使用記錄。 如需後續步驟，請參閱下列相關文章：


* 瞭解如何針對設計工具管線進行疑難排解，請參閱 [Debug & 針對 ML 管線進行疑難排解](how-to-debug-pipelines.md#azure-machine-learning-designer)。
* 瞭解如何使用 Python SDK 來記錄 SDK 撰寫體驗中的計量，請參閱 [在 AZURE ML 定型回合中啟用記錄](how-to-track-experiments.md)。
* 瞭解如何在設計工具中使用 [ [執行 Python 腳本](./algorithm-module-reference/execute-python-script.md) ]。
