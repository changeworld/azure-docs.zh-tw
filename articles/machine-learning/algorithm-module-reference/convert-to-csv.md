---
title: 轉換成 CSV：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [轉換為 CSV] 模組，將資料集轉換為 CSV 格式，以供使用 R 或 Python 腳本模組進行下載、匯出或共用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 3b7c781717952765941acd4ea4aa47593d4b8a0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898800"
---
# <a name="convert-to-csv-module"></a>轉換成 CSV 模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組將資料集轉換成 CSV 格式，以便透過 R 或 Python 腳本模組進行下載、匯出或共用。

### <a name="more-about-the-csv-format"></a>關於 CSV 格式的詳細資訊 

CSV 格式（代表「逗點分隔值」）是許多外部機器學習工具所使用的一種檔案格式。 使用開放原始碼語言（例如 R 或 Python）時，CSV 是常見的交換格式。

即使您在 Azure Machine Learning 中執行大部分的工作，有時候您可能會發現將資料集轉換為 CSV 以在外部工具中使用是很方便的。 例如：

+ 下載 CSV 檔案，以使用 Excel 開啟該檔案，或將其匯入至關係資料庫。  
+ 將 CSV 檔案儲存至雲端儲存體，並從 Power BI 連接到該檔案以建立視覺效果。  
+ 使用 CSV 格式來準備要在 R 和 Python 中使用的資料。 

當您將資料集轉換為 CSV 時，csv 會儲存在您的 Azure ML 工作區中。 您可以使用 Azure 儲存體公用程式來直接開啟並使用該檔案。 您也可以選取 [ **轉換為 CSV** ] 模組來存取設計工具中的 csv，然後在右面板中的 [ **輸出** ] 索引標籤下選取長條圖圖示來查看輸出。 您可以從 [結果] 資料夾將 CSV 下載至本機目錄。  

## <a name="how-to-configure-convert-to-csv"></a>如何設定轉換為 CSV


1.  將 [轉換為 CSV] 模組新增至您的管線。 您可以在設計工具的 [ **資料轉換** ] 群組中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   
  
3.  提交管線。

### <a name="results"></a>結果
  

選取 [**轉換為 CSV**] 右邊面板中的 [**輸出**] 索引標籤，然後選取**埠輸出**下的其中一個圖示。  

+ **註冊資料集**：選取圖示，並將 CSV 檔案以個別資料集的形式儲存回 Azure ML 工作區。 您可以在 [ **我的資料集** ] 區段下的模組樹狀結構中，找到資料集做為模組。

 + **View output**：選取眼睛圖示，然後遵循指示來流覽 **Results_dataset** 資料夾，並下載 data.csv 檔案。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 