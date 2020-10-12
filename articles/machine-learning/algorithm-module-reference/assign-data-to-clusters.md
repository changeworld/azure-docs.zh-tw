---
title: 將資料指派給叢集：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [將資料指派給叢集] 模組，以評分群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898860"
---
# <a name="module-assign-data-to-clusters"></a>模組：將資料指派給群集

本文說明如何使用 Azure Machine Learning 表設計工具中的 [ *將資料指派給群集* ] 模組。 此模組會透過以 *K 表示的群集* 演算法來定型的群集模型來產生預測。

[將資料指派給叢集] 模組會傳回包含每個新資料點之可能指派的資料集。 

## <a name="how-to-use-assign-data-to-clusters"></a>如何使用將資料指派給群集
  
1. 在 Azure Machine Learning 表設計工具中，找出先前定型的群集模型。 您可以使用下列其中一種方法來建立和定型群集模型：  
  
    - 使用 [k 表示](k-means-clustering.md) 叢集模組來設定 k 表示群集演算法，並使用資料集和「定型群集模型」模組來定型模型， (本文) 。  
  
    - 您也可以從工作區中的 [ **已儲存的模型** ] 群組加入現有的已定型群集模型。

2. 將定型的模型附加至 **將資料指派給**叢集的左側輸入埠。  

3. 附加新的資料集做為輸入。 

   在此資料集中，標籤是選擇性的。 一般來說，群集是一種非監督式的學習方法。 您不應該事先知道這些類別。 但是，輸入資料行必須與用來定型群集模型的資料行相同，否則就會發生錯誤。

    > [!TIP]
    > 若要減少從群集預測寫入設計工具的資料行數目，請使用 [資料集中的 [選取資料行](select-columns-in-dataset.md)]，然後選取資料行的子集。 
    
4. 如果您想要讓結果包含完整的輸入資料集，包括顯示 (叢集) 指派之結果的資料行，請將 [ **檢查附加] 或 [取消** 核取] 核取方塊保持選取狀態。
  
    如果您清除此核取方塊，則只會傳回結果。 當您在 web 服務中建立預測時，此選項可能會很有用。
  
5.  提交管線。  
  
### <a name="results"></a>結果

+  若要查看資料集中的值，請以滑鼠右鍵按一下模組，然後選取 [ **視覺化**]。 或者，選取模組並切換至右面板中的 [ **輸出** ] 索引標籤，按一下 **埠輸出** 中的長條圖圖示以視覺化結果。

