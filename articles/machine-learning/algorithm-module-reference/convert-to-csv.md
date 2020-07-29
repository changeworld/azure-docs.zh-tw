---
title: 轉換成 CSV：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [轉換成 CSV] 模組，將資料集轉換成 CSV 格式，您可以使用 R 或 Python 腳本模組進行下載、匯出或共用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477675"
---
# <a name="convert-to-csv-module"></a>轉換成 CSV 模組

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

您可以使用此模組，將資料集轉換成 CSV 格式，以供使用 R 或 Python 腳本模組下載、匯出或共用。

### <a name="more-about-the-csv-format"></a>關於 CSV 格式的詳細資訊 

CSV 格式（代表「逗號分隔值」）是許多外部機器學習工具所使用的檔案格式。 CSV 是使用開放原始碼語言（例如 R 或 Python）時的通用交換格式。

即使您在 Azure Machine Learning 中執行大部分的工作，有時可能會發現將資料集轉換成 CSV 以用於外部工具是很方便的。 例如：

+ 下載 CSV 檔案，以使用 Excel 開啟檔案，或將它匯入關係資料庫。  
+ 將 CSV 檔案儲存至雲端存放裝置，並從 Power BI 連接到該檔案，以建立視覺效果。  
+ 使用 CSV 格式來準備要在 R 和 Python 中使用的資料。 

當您將資料集轉換成 CSV 時，csv 會儲存在您的 Azure ML 工作區中。 您可以使用 Azure 儲存體公用程式來直接開啟和使用檔案。 您也可以選取 [**轉換成 CSV** ] 模組來存取設計工具中的 csv，然後選取右面板中 [**輸出**] 索引標籤下的長條圖圖示來查看輸出。 您可以從 [結果] 資料夾將 CSV 下載到本機目錄。  

## <a name="how-to-configure-convert-to-csv"></a>如何設定轉換成 CSV


1.  將 [轉換成 CSV] 模組新增至您的管線。 您可以在設計工具的 [**資料轉換**] 群組中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   
  
3.  提交管線。

### <a name="results"></a>結果
  

選取 [**轉換成 CSV**] 右窗格中的 [**輸出**] 索引標籤，然後選取**埠輸出**底下的其中一個圖示。  

+ **註冊資料集**：選取圖示，然後將 CSV 檔案儲存回 Azure ML 工作區作為個別的資料集。 您可以在 [**我的資料集**] 區段底下的模組樹狀結構中，找到資料集作為模組。

 + **視圖輸出**：選取眼睛圖示，然後依照指示流覽**Results_dataset**資料夾，並下載 data.csv 檔案。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 