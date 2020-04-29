---
title: 手動輸入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用 [手動輸入資料] 模組，藉由輸入值來建立小型資料集。 Dataset 可以有多個資料行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79367511"
---
# <a name="enter-data-manually-module"></a>手動輸入資料模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用 [**手動輸入資料**] 模組，藉由輸入值來建立小型資料集。 Dataset 可以有多個資料行。
  
此模組有助於下列案例：  
  
- 產生一組較小的值來進行測試。  
- 建立標籤的簡短清單。  
- 輸入要插入資料集的資料行名稱清單。

## <a name="create-a-dataset"></a>建立資料集 
  
1. 將 [[手動輸入資料](./enter-data-manually.md)] 模組新增至您的管線。 您可以在 Azure Machine Learning 的 [**資料輸入和輸出**] 分類中找到此模組。 
  
1. 針對 [ **DataFormat**]，選取下列其中一個選項。 這些選項會決定應該如何剖析您所提供的資料。 每種格式的需求有很大的差異，因此請務必閱讀相關主題。  
  
   - **ARFF**： Weka 所使用的屬性關聯檔案格式。   
   - **CSV**：逗號分隔值格式。 如需詳細資訊，請參閱[轉換成 CSV](./convert-to-csv.md)。    
   - **SVMLight**： Vowpal Wabbit 和其他機器學習架構所使用的格式。    
   - **TSV**：定位字元分隔值格式。

   如果您選擇格式，但未提供符合格式規格的資料，就會發生執行階段錯誤。
  
1. 按一下 [**資料**] 文字方塊內部，開始輸入資料。 下列格式需要特別注意：  
  
   - **CSV**：若要建立多個資料行，請貼上逗號分隔的文字，或在欄位之間使用逗號來輸入多個資料行。
  
     如果您選取**HasHeader**選項，則可以使用值的第一列做為資料行標題。  
  
     如果您取消選取此選項，則會使用資料行名稱（Col1、Col2 等等）。 您之後可以使用 [[編輯中繼資料](./edit-metadata.md)] 來新增或變更資料行名稱。  
  
   - **TSV**：若要建立多個資料行，請貼上 tab 鍵分隔的文字，或在欄位之間使用 tab 鍵來輸入多個資料行。  
  
     如果您選取**HasHeader**選項，則可以使用值的第一列做為資料行標題。  
  
     如果您取消選取此選項，則會使用資料行名稱（Col1、Col2 等等）。 您之後可以使用 [[編輯中繼資料](./edit-metadata.md)] 來新增或變更資料行名稱。  
  
   - **ARFF**：貼上現有的 ARFF 格式檔案。 如果您要直接輸入值，請務必在資料的開頭加入選擇性的標頭和必要的屬性欄位。 

     例如，您可以將下列標頭和屬性資料列加入至簡單列表。 資料行標題會是`SampleText`。 請注意，不支援字串類型。
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**：使用 SVMLight 格式輸入或貼上值。  
  
     例如，下列範例會以 SVMLight 格式表示血壓捐贈資料集的前幾行：  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     當您執行 [[手動輸入資料](./enter-data-manually.md)] 模組時，這些行會轉換成資料行和索引值的資料集，如下所示：  
  
     |Col1|Col2|Col3|Col4|標籤|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 選取每個資料列後面的 Enter 鍵，以開始新的一行。      
     
   如果您選取多次以加入多個空的尾端資料列，則會移除或修剪空白資料列。  
  
   如果您建立具有遺漏值的資料列，您隨時都可以將它們篩選出來。  
  
1. 將輸出埠連接到其他模組，然後執行管線。  
  
   若要查看資料集，請以滑鼠右鍵按一下模組，然後選取 [**視覺化**]。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 