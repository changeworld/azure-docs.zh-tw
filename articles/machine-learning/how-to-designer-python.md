---
title: 在設計工具中執行 Python 腳本
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具中使用執行 Python 腳本模型，以執行以 Python 撰寫的自訂作業。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556228"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>在 Azure Machine Learning 設計工具中執行 Python 程式碼

在本文中，您將了解如何使用[執行 Python 指令碼](algorithm-module-reference/execute-python-script.md)模組，將自訂邏輯新增至 Azure Machine Learning 設計工具。 在下列操作說明中，您將使用 Pandas 程式庫執行簡單的特徵工程。

您可以使用內建的程式碼編輯器快速新增簡單的 Python 邏輯。 如果您想要新增更複雜的程式碼或上傳其他 Python 程式庫，則應使用 zip 檔案方法。

預設執行環境會使用 Python 的 Anacondas 發行版本。 如需預先安裝套件的完整清單，請參閱[執行 Python 指令碼模組參考](algorithm-module-reference/execute-python-script.md)頁面。

![執行 Python 輸入對應](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>執行在設計工具中撰寫的 Python

### <a name="add-the-execute-python-script-module"></a>新增執行 Python 指令碼模組

1. 在設計工具選擇區中，尋找 **執行 Python 指令碼** 模組。 您可以在 **Python 語言** 區段中找到該模組。

1. 將模組拖放到管線畫布上。

### <a name="connect-input-datasets"></a>連接輸入資料集

本文使用範例資料集 **汽車價格資料 (原始)** 。 

1. 將您的資料集拖放到管線畫布上。

1. 將資料集的輸出連接埠連線至 **執行 Python 指令碼** 模組左上角的輸入連接埠。 設計工具會將輸入公開為進入點指令碼的參數。
    
    右側的輸入連接埠會保留給壓縮的 Python 程式庫使用。

    ![連接資料集](media/how-to-designer-python/connect-dataset.png)
        

1. 記下您使用的輸入連接埠。 設計工具會將左側的輸入連接埠指派給變數 `dataset1`，並將中間的輸入連接埠指派給 `dataset2`。 

輸入模組是選擇性的，因為您可以直接在 **執行 Python 指令碼** 模組中產生或匯入資料。

### <a name="write-your-python-code"></a>撰寫您的 Python 程式碼

設計工具會提供初始的進入點指令碼，讓您編輯和輸入自己的 Python 程式碼。 

在此範例中，您會使用 Pandas 結合汽車資料集 **價格** 和 **馬力** 中的兩個資料行，以建立新的資料行 **美元/馬力** 。 此資料行代表每一馬力的價格，這在決定車價是否實惠時可能是很有用的特徵。 

1. 選取 **執行 Python 指令碼** 模組。

1. 在顯示於畫布右側的窗格中，選取 [Python 指令碼] 文字方塊。

1. 將下列程式碼複製並貼到文字方塊中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    您的管線應該會如下圖所示：
    
    ![執行 Python 管線](media/how-to-designer-python/execute-python-pipeline.png)

    進入點指令碼必須包含函式 `azureml_main`。 有兩個函式參數會對應至 **執行 Python 指令碼** 模組的兩個輸入連接埠。

    傳回值必須是 Pandas Dataframe。 您最多可以在模組輸出中傳回兩個 Dataframe。
    
1. 提交管線。

現在，您已有一個具有新特徵 **美元/馬力** 的資料集，有助於汽車推薦的定型。 這是特徵擷取和維度縮減的範例。 

## <a name="next-steps"></a>後續步驟

了解如何在 Azure Machine Learning 設計工具中[匯入您自己的資料](how-to-designer-import-data.md)。
