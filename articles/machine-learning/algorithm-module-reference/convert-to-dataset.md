---
title: 轉換成資料集：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [轉換成資料集] 模組，將資料輸入轉換為 Microsoft Azure Machine Learning 所使用的內部資料集格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9b2d003ef4938681229317b625aae4526787ac15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898709"
---
# <a name="convert-to-dataset"></a>轉換為資料集

本文描述如何使用 Azure Machine Learning 設計工具中的 [轉換成資料集] 模組，將管線的任何資料轉換為設計工具的內部格式。
  
在大部分的情況下，不需要轉換。 當對資料執行任何作業時，Azure Machine Learning 會隱含地將資料轉換成其原生資料集格式。 

如果您在一組資料上執行某種正規化或清除，而且想要確保在其他管線中使用這些變更，建議您將資料儲存至資料集格式。  
  
> [!NOTE]
> 轉換成資料集只會變更資料的格式。 它不會將新的資料複本儲存在工作區中。 若要儲存資料集，請按兩下輸出埠，選取 [ **另存為資料集**]，然後輸入新的名稱。  
  
## <a name="how-to-use-convert-to-dataset"></a>如何使用轉換成資料集  

建議您在使用 [轉換成資料集] 之前，先使用 [ [編輯中繼資料](edit-metadata.md) ] 模組準備資料集。 您可以新增或變更資料行名稱、調整資料類型，以及視需要進行其他變更。

1.  將 [轉換成資料集] 模組新增至您的管線。 您可以在設計工具的 [ **資料轉換** ] 類別中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   

    只要資料是 [表格式](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true)的，您就可以將它轉換成資料集。 這包括透過匯 [入資料](import-data.md)載入的資料、 [手動透過輸入資料](enter-data-manually.md)建立的資料，或透過 [套用 [轉換](apply-transformation.md)] 轉換的資料集。

3.  在 [ **動作** ] 下拉式清單中，指出您是否想要在儲存資料集之前對資料進行任何清除：  
  
    - **None**：依原樣使用資料。  
  
    - **SetMissingValue**：將特定值設定為資料集中的遺漏值。 預設預留位置是問號字元 (？ ) ，但您可以使用 [  **自訂遺漏值** ] 選項來輸入不同的值。 例如，如果您為**自訂遺漏值**輸入**計程車**，則資料集中的所有**計程車**實例都會變更為遺漏值。
  
    - **ReplaceValues**：使用此選項來指定要以任何其他精確值取代的單一確切值。 您可以藉由設定 **replace** 方法來取代遺漏值或自訂值：

      - **遺漏**：請選擇此選項來取代輸入資料集中的遺漏值。 在 [ **新值**] 中，輸入要用來取代遺漏值的值。
      - **自訂**：選擇此選項以取代輸入資料集中的自訂值。 針對 [ **自訂值**]，輸入您想要尋找的值。 例如，如果您的資料包含 `obs` 用來作為遺漏值之預留位置的字串，則您會輸入 `obs` 。 在 [ **新值**] 中，輸入要用來取代原始字串的新值。
  
    請注意， **ReplaceValues** 作業只適用于完全相符的專案。 例如，這些字串不會受到影響： `obs.` 、 `obsolete` 。  
 
  
5.  提交管線。  

## <a name="results"></a>結果

+  若要使用新名稱儲存產生的資料集，請在模組右面板的 [**輸出**] 索引標籤下，選取 [圖示**註冊資料集**]。  
  
## <a name="technical-notes"></a>技術說明  

-   任何採用資料集做為輸入的模組，也可以取得 CSV 檔案或 TSV 檔案中的資料。 在執行任何模組程式碼之前，會先將輸入前置處理。 前置處理相當於在輸入上執行 [轉換成資料集] 模組。  
  
-   您無法從 SVMLight 格式轉換為資料集。  
  
-   當您指定自訂取代作業時，會將搜尋和取代作業套用至完整值。 不允許部分符合。 例如，您可以使用-1 或33來取代3，但無法以兩位數的數位取代3，例如35。  
  
-   在自訂取代作業中，如果您使用的任何取代字元不符合資料行的目前資料類型，則取代會失敗而無任何訊息。  

  
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
