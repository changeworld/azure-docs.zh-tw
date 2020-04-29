---
title: 轉換成資料集：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [轉換成資料集] 模組，將資料輸入轉換成 Microsoft Azure Machine Learning 所使用的內部資料集格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456585"
---
# <a name="convert-to-dataset"></a>轉換為資料集

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [轉換成資料集] 模組，將管線的任何資料轉換為設計工具的內部格式。
  
在大部分的情況下，不需要轉換。 Azure Machine Learning 在資料上執行任何作業時，會隱含地將資料轉換成其原生資料集格式。 

如果您已在一組資料上執行某種正規化或清除，而且想要確保變更用於其他管線中，建議您將資料儲存至資料集格式。  
  
> [!NOTE]
> 轉換成資料集只會變更資料的格式。 它不會將新的資料複本儲存在工作區中。 若要儲存資料集，請按兩下輸出埠，選取 [**另存為資料集**]，然後輸入新的名稱。  
  
## <a name="how-to-use-convert-to-dataset"></a>如何使用轉換成資料集  

我們建議您先使用[編輯中繼資料](edit-metadata.md)模組來準備資料集，再使用 [轉換成資料集]。 您可以新增或變更資料行名稱、調整資料類型，並視需要進行其他變更。

1.  將 [轉換成資料集] 模組新增至您的管線。 您可以在設計工具的 [**資料轉換**] 分類中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   

    只要資料是[表格式](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)的，您就可以將它轉換成資料集。 這包括透過匯[入資料](import-data.md)載入的資料、[手動輸入資料](enter-data-manually.md)所建立的資料，或透過套用[轉換](apply-transformation.md)而轉換的資料集。

3.  在 [**動作**] 下拉式清單中，指出您是否想要在儲存資料集之前對資料進行任何清除：  
  
    - **無**：依其使用資料。  
  
    - **SetMissingValue**：將資料集內的特定值設定為遺漏值。 預設預留位置是問號字元（？），但您可以使用 [**自訂遺漏值**] 選項來輸入不同的值。 例如，如果您輸入**計程車**做為**自訂遺漏值**，則資料集中的**計程車**所有實例都會變更為遺漏值。
  
    - **ReplaceValues**：使用此選項來指定要取代為任何其他確切值的單一確切值。 您可以藉由設定**replace**方法來取代遺漏值或自訂值：

      - **遺漏**：選擇此選項以取代輸入資料集中遺漏的值。 針對 [**新值**]，輸入要用來取代遺漏值的值。
      - **自訂**：選擇此選項以取代輸入資料集中的自訂值。 針對 [**自訂值**]，輸入您想要尋找的值。 例如，如果您的資料包含用`obs`來做為遺漏值預留位置的字串，您`obs`就可以輸入。 針對 [**新值**]，輸入要用來取代原始字串的新值。
  
    請注意， **ReplaceValues**作業只適用于完全相符的專案。 例如，這些字串不會受到影響： `obs.`、。 `obsolete`  
 
  
5.  提交管線。  

## <a name="results"></a>結果

+  若要使用新名稱儲存產生的資料集，請在模組右側面板的 [**輸出**] 索引標籤底下，選取 [**註冊資料集**] 圖示。  
  
## <a name="technical-notes"></a>技術說明  

-   任何接受資料集做為輸入的模組，也都可以接受 CSV 檔案或 TSV 檔案中的資料。 在執行任何模組程式碼之前，會預先處理輸入。 前置處理相當於在輸入上執行 [轉換成資料集] 模組。  
  
-   您無法從 SVMLight 格式轉換為資料集。  
  
-   當您指定自訂的取代作業時，搜尋和取代作業會套用至完整的值。 不允許部分符合的專案。 例如，您可以將3取代為-1 或使用33，但您不能以兩位數的數位取代3，例如35。  
  
-   在自訂取代作業中，如果您使用的任何取代字元不符合資料行的目前資料類型，則取代會失敗而無任何訊息。  

  
## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 
