---
title: Python
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具中使用 Python 來轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455786"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>在 Azure Machine Learning 設計工具中執行 Python 程式碼

在本文中，您將瞭解如何使用 [[執行 Python 腳本](algorithm-module-reference/execute-python-script.md)] 模組，將自訂邏輯新增至 Azure Machine Learning 設計工具。 在下列操作說明中，您可以使用 Pandas 程式庫來執行簡單的功能工程。

您可以使用內建程式碼編輯器來快速新增簡單的 Python 邏輯。 如果您想要新增更複雜的程式碼或上傳其他 Python 程式庫，您應該使用 zip 檔案方法。

預設執行環境會使用 Python 的 Anacondas 分佈。 如需預先安裝套件的完整清單，請參閱[執行 Python 腳本模組參考](algorithm-module-reference/execute-python-script.md)頁面。

![執行 Python 輸入對應](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>執行在設計工具中撰寫的 Python

### <a name="add-the-execute-python-script-module"></a>新增執行 Python 腳本模組

1. 在設計工具選擇區中尋找 [**執行 Python 腳本**] 模組。 它可以在**Python 語言**一節中找到。

1. 將模組拖放到管線畫布上。

### <a name="connect-input-datasets"></a>連接輸入資料集

本文使用範例資料集**汽車價格資料（原始）**。 

1. 將您的資料集拖放到管線畫布上。

1. 將資料集的輸出埠連接至**執行 Python 腳本**模組的左上方輸入埠。 設計工具會將輸入公開為進入點腳本的參數。
    
    右側輸入埠會保留給壓縮的 python 程式庫。

    ![串連資料組](media/how-to-designer-python/connect-dataset.png)
        

1. 請記下您使用的輸入埠。 設計工具會將左側輸入埠指派給變數`dataset1` ，並將中間輸入埠`dataset2`指派給。 

輸入模組是選擇性的，因為您可以直接在 [**執行 Python 腳本**] 模組中產生或匯入資料。

### <a name="write-your-python-code"></a>撰寫您的 Python 程式碼

設計工具會提供初始的進入點腳本，供您編輯及輸入自己的 Python 程式碼。 

在此範例中，您會使用 Pandas 來結合汽車資料集（**價格**和**動力**）中的兩個數據行，以建立新資料行，**每個動力為美元**。 本專欄代表每個動力的費用，這可能是一項很有用的功能，可決定汽車是否適合用來處理金錢。 

1. 選取 [**執行 Python 腳本**] 模組。

1. 在畫布右側顯示的窗格中，選取 [ **Python 腳本**] 文字方塊。

1. 複製下列程式碼並貼到文字方塊中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    您的管線應該看起來如下圖：
    
    ![執行 Python 管線](media/how-to-designer-python/execute-python-pipeline.png)

    進入點腳本必須包含函數`azureml_main`。 有兩個函式參數會對應至**執行 Python 腳本**模組的兩個輸入埠。

    傳回值必須是 Pandas 資料框架。 您最多可以傳回兩個數據框架作為模組輸出。
    
1. 提交管線。

現在，您有一個具有新功能**美元/HP**的資料集，這在訓練汽車推薦時很有用。 這是功能解壓縮和維度縮減的範例。 

## <a name="next-steps"></a>後續步驟

瞭解如何在 Azure Machine Learning 設計工具中匯[入您自己的資料](how-to-designer-import-data.md)。