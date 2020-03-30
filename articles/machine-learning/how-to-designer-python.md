---
title: Python
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習設計器中使用 Python 來轉換資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455786"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>在 Azure 機器學習設計器中執行 Python 代碼

在本文中，您將瞭解如何使用[執行 Python 腳本](algorithm-module-reference/execute-python-script.md)模組向 Azure 機器學習設計器添加自訂邏輯。 在下面的操作操作中，您可以使用 Pandas 庫執行簡單的功能工程。

您可以使用內置代碼編輯器快速添加簡單的 Python 邏輯。 如果要添加更複雜的代碼或上載其他 Python 庫，則應使用 ZIP 檔案方法。

預設執行環境使用 Python 的 Anacondas 分佈。 有關預先安裝包的完整清單，請參閱執行 Python[腳本模組參考](algorithm-module-reference/execute-python-script.md)頁。

![執行 Python 輸入映射](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>執行在設計器中編寫的 Python

### <a name="add-the-execute-python-script-module"></a>添加執行 Python 腳本模組

1. 在設計器調色板中查找**執行 Python 腳本**模組。 可以在**Python 語言**部分找到它。

1. 將模組拖放到管道畫布上。

### <a name="connect-input-datasets"></a>連接輸入資料集

本文使用示例資料集，**汽車價格資料（原始）。** 

1. 將資料集拖放到管道畫布上。

1. 將資料集的輸出埠連接到**執行 Python 腳本**模組的左上角輸入埠。 設計器將輸入作為參數公開到進入點腳本。
    
    右輸入埠保留用於壓縮的 python 庫。

    ![串連資料組](media/how-to-designer-python/connect-dataset.png)
        

1. 記下您使用的輸入埠。 設計器將左側輸入埠分配給變數`dataset1`，將中間輸入埠分配給 。 `dataset2` 

輸入模組是可選的，因為可以直接在**執行 Python 腳本**模組中生成或導入資料。

### <a name="write-your-python-code"></a>編寫 Python 代碼

設計器提供初始進入點腳本，供您編輯和輸入自己的 Python 代碼。 

在此示例中，您可以使用 Pandas 組合汽車資料集"**價格**和**馬力**"中的兩列，以創建一個新列，**即每馬力的美元**。 本專欄表示您為每馬力支付多少費用，這可能是一個有用的功能，可以決定一輛車是否物有所值。 

1. 選擇**執行 Python 腳本**模組。

1. 在畫布右側顯示的窗格中，選擇**Python 腳本**文字方塊。

1. 複製以下代碼並將其粘貼到文字方塊中。

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    管道應顯示以下圖像：
    
    ![執行 Python 管道](media/how-to-designer-python/execute-python-pipeline.png)

    進入點腳本必須包含函數`azureml_main`。 有兩個函數參數映射到**執行 Python 腳本**模組的兩個輸入埠。

    傳回值必須為熊貓資料幀。 您最多可以返回兩個數據幀作為模組輸出。
    
1. 提交管道。

現在，您有一個資料集，具有新功能 **"美元/HP"，** 這對於培訓汽車推薦者非常有用。 這是特徵提取和尺寸減小的示例。 

## <a name="next-steps"></a>後續步驟

瞭解如何在 Azure 機器學習設計器中[導入自己的資料](how-to-designer-import-data.md)。