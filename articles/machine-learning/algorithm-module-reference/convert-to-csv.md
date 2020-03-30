---
title: 轉換為 CSV：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"轉換為 CSV"模組將資料集轉換為可下載、匯出或與 R 或 Python 腳本模組共用的 CSV 格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477675"
---
# <a name="convert-to-csv-module"></a>轉換為 CSV 模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可將資料集轉換為可下載、匯出或與 R 或 Python 腳本模組共用的 CSV 格式。

### <a name="more-about-the-csv-format"></a>有關 CSV 格式的更多 

CSV 格式代表"逗號分隔值"，是許多外部機器學習工具使用的檔案格式。 CSV 是使用開來源語言（如 R 或 Python）時的常見交換格式。

即使您在 Azure 機器學習中執行大部分工作，有時您可能會發現將資料集轉換為 CSV 以在外部工具中使用是很方便的。 例如：

+ 下載 CSV 檔以使用 Excel 打開該檔，或將其導入關係資料庫。  
+ 將 CSV 檔保存到雲存儲，並從 Power BI 連接到該檔以創建視覺化效果。  
+ 使用 CSV 格式準備資料以在 R 和 Python 中使用。 

將資料集轉換為 CSV 時，csv 將保存在 Azure ML 工作區中。 可以使用 Azure 存儲實用程式直接打開和使用該檔。 您還可以通過選擇 **"轉換為 CSV"** 模組來訪問設計器中的 CSV，然後在右側面板的 **"輸出"** 選項卡下選擇長條圖圖示以查看輸出。 您可以將 CSV 從"結果"資料夾下載到本地目錄。  

## <a name="how-to-configure-convert-to-csv"></a>如何配置轉換為 CSV


1.  將"轉換為 CSV"模組添加到管道中。 您可以在設計器中**的資料轉換**組中找到此模組。 

2. 將其連接到輸出資料集的任何模組。   
  
3.  提交管道。

### <a name="results"></a>結果
  

選擇 **"轉換為 CSV"** 右側面板中的 **"輸出**"選項卡，然後選擇**在埠輸出**下的這些圖示之一上。  

+ **註冊資料集**：選擇圖示並將 CSV 檔保存回 Azure ML 工作區作為單獨的資料集。 您可以在"**我的資料集"** 部分下的模組樹中找到資料集作為模組。

 + **查看輸出**：選擇眼睛圖示，然後按照說明流覽**Results_dataset**資料夾，然後下載 data.csv 檔。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 