---
title: 手動輸入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"輸入資料手動"模組通過鍵入值來創建小型資料集。 資料集可以有多個列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367511"
---
# <a name="enter-data-manually-module"></a>輸入手動輸入資料模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用 **"手動輸入資料"** 模組通過鍵入值來創建小型資料集。 資料集可以有多個列。
  
此模組在以下情況下非常有用：  
  
- 生成一小組值進行測試。  
- 創建標籤的簡短清單。  
- 鍵入要插入資料集的列名稱清單。

## <a name="create-a-dataset"></a>建立資料集 
  
1. 將["手動輸入資料"](./enter-data-manually.md)模組添加到管道中。 您可以在 Azure 機器學習中**的資料輸入和輸出**類別中找到此模組。 
  
1. 對於**資料格式**，請選擇以下選項之一。 這些選項確定如何分析您提供的資料。 每種格式的要求差別很大，因此請務必閱讀相關主題。  
  
   - **ARFF**：韋卡使用的屬性關係檔案格式。   
   - **CSV**：逗號分隔值格式。 有關詳細資訊，請參閱轉換為[CSV](./convert-to-csv.md)。    
   - **SVMLight**：Vowpal Wabbit 和其他機器學習框架使用的格式。    
   - **TSV**：選項卡分隔值格式。

   如果選擇格式，但不提供符合格式規範的資料，則會發生執行階段錯誤。
  
1. 按一下 **"資料**文本"框以開始輸入資料。 以下格式需要特別注意：  
  
   - **CSV**： 要創建多個列，請粘貼逗號分隔的文本，或使用欄位之間的逗號鍵入多個列。
  
     如果選擇 **"雜湊"** 選項，則可以使用第一行值作為列標題。  
  
     如果取消選擇此選項，將使用列名稱（Col1、Col2 等）。 以後可以使用["編輯中繼資料"](./edit-metadata.md)添加或更改列名稱。  
  
   - **TSV**： 要創建多個列，請粘貼與選項卡分隔的文本，或使用欄位之間的選項卡鍵入多個列。  
  
     如果選擇 **"雜湊"** 選項，則可以使用第一行值作為列標題。  
  
     如果取消選擇此選項，將使用列名稱（Col1、Col2 等）。 以後可以使用["編輯中繼資料"](./edit-metadata.md)添加或更改列名稱。  
  
   - **ARFF**： 粘貼到現有的 ARFF 格式檔中。 如果直接鍵入值，請確保在資料開頭添加可選標頭和所需屬性欄位。 

     例如，可以將以下標頭和屬性行添加到簡單列表中。 列標題將為`SampleText`。 請注意，不支援字串類型。
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**：使用 SVMLight 格式鍵入或粘貼值。  
  
     例如，以下示例以 SVMLight 格式表示獻血資料集的前兩行：  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     運行"[手動輸入資料"](./enter-data-manually.md)模組時，這些行將轉換為列和索引值的資料集，如下所示：  
  
     |Col1|Col2|Col3|Col4|標籤|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 選擇每行之後的 Enter 鍵，以啟動新行。      
     
   如果選擇"輸入多次"以添加多個空尾隨行，則空行將被刪除或修剪。  
  
   如果創建缺少值的行，則始終可以在以後篩選出來。  
  
1. 將輸出埠連接到其他模組，並運行管道。  
  
   要查看資料集，請按右鍵模組並選擇 **"視覺化**"。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 