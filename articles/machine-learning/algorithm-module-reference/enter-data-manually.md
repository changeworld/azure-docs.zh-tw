---
title: 手動輸入資料：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用 [手動輸入資料] 模組，藉由輸入值來建立小型資料集。 資料集可以有多個資料行。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908075"
---
# <a name="enter-data-manually-module"></a>手動輸入資料模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用 [ **手動輸入資料** ] 模組，藉由輸入值來建立小型資料集。 資料集可以有多個資料行。
  
此模組在下列案例中可能很有説明：  
  
- 產生一組較小的值來進行測試。  
- 建立標籤的簡短清單。  
- 輸入要插入資料集中的資料行名稱清單。

## <a name="create-a-dataset"></a>建立資料集 
  
1. 將 [ [手動輸入資料](./enter-data-manually.md) ] 模組新增至您的管線。 您可以在 Azure Machine Learning 的 [ **資料輸入] 和 [輸出** ] 分類中找到此模組。 
  
1. 針對 [ **DataFormat**]，選取下列其中一個選項。 這些選項會決定您提供的資料應如何剖析。 每種格式的需求有很大的差異，因此請務必閱讀相關的主題。  
  
   - **ARFF**： Weka 所使用的屬性關聯檔案格式。   
   - **CSV**：逗點分隔值格式。 如需詳細資訊，請參閱 [轉換成 CSV](./convert-to-csv.md)。    
   - **SVMLight**： Vowpal Wabbit 和其他機器學習架構所使用的格式。    
   - **TSV**：定位字元分隔值格式。

   如果您選擇格式，但未提供符合格式規格的資料，就會發生執行階段錯誤。
  
1. 在 [ **資料** ] 文字方塊內按一下，開始輸入資料。 以下是需要特別注意的格式：  
  
   - **CSV**：若要建立多個資料行，請貼上逗點分隔的文字，或在欄位之間使用逗號來輸入多個資料行。
  
     如果您選取 **HasHeader** 選項，則可以使用值的第一個資料列做為資料行標題。  
  
     如果您取消選取此選項，則會使用資料行名稱 (Col1、Col2 等等) 。 您稍後可以使用 [ [編輯中繼資料](./edit-metadata.md)] 來新增或變更資料行名稱。  
  
   - **TSV**：若要建立多個資料行，請貼上 tab 分隔的文字，或在欄位之間使用定位字元來輸入多個資料行。  
  
     如果您選取 **HasHeader** 選項，則可以使用值的第一個資料列做為資料行標題。  
  
     如果您取消選取此選項，則會使用資料行名稱 (Col1、Col2 等等) 。 您稍後可以使用 [ [編輯中繼資料](./edit-metadata.md)] 來新增或變更資料行名稱。  
  
   - **ARFF**：貼上現有的 ARFF 格式檔案。 如果您要直接輸入值，請務必在資料的開頭加入選擇性的標頭和必要屬性欄位。 

     例如，您可以將下列標頭和屬性資料列新增至簡單列表。 欄位標題為 `SampleText` 。 請注意，不支援字串類型。
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**：使用 SVMLight 格式輸入或貼上值。  
  
     例如，下列範例代表血糖捐贈資料集的前幾行，採用 SVMLight 格式：  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     當您執行 [手動輸入資料](./enter-data-manually.md) 模組時，這些行會轉換成資料行和索引值的資料集，如下所示：  
  
     |Col1|Col2|Col3|Col4|標籤|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 選取每個資料列後面的 Enter 鍵，開始新的一行。      
     
   如果您選取 [多次輸入] 來加入多個空白尾端資料列，將會移除或修剪空白資料列。  
  
   如果您建立具有遺漏值的資料列，您隨時都可以篩選出這些資料列。  
  
1. 將輸出埠連接到其他模組，並執行管線。  
  
   若要查看資料集，請以滑鼠右鍵按一下模組，然後選取 [ **視覺化**]。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 