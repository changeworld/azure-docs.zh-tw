---
title: 將資料指派給叢集：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [將資料指派給叢集] 模組來評分群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: eff480d6763ae4bd277e6781663c559cc7c9169e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152443"
---
# <a name="module-assign-data-to-clusters"></a>模組：將資料指派給群集

本文說明如何在 Azure Machine Learning 設計工具（預覽）中使用 [*將資料指派給*叢集] 模組。 此模組會透過以*K 表示*叢集演算法定型的群集模型來產生預測。

[將資料指派給叢集] 模組會傳回資料集，其中包含每個新資料點的可能指派。 

## <a name="how-to-use-assign-data-to-clusters"></a>如何使用將資料指派給群集
  
1. 在 Azure Machine Learning 設計工具中，找出先前定型的群集模型。 您可以使用下列其中一種方法來建立和定型群集模型：  
  
    - 使用[k 意指](k-means-clustering.md)叢集模組來設定 K 表示叢集演算法，並使用資料集和訓練叢集模型模組（本文）來定型模型。  
  
    - 您也可以從工作區中的 [**已儲存的模型**] 群組加入現有的定型群集模型。

2. 將定型的模型附加至**將資料指派給群集**的左側輸入埠。  

3. 附加新的資料集做為輸入。 

   在此資料集中，標籤是選擇性的。 一般而言，叢集是不受監督學習方法。 您不應該事先知道這些類別。 不過，輸入資料行必須與用來定型群集模型的資料行相同，否則會發生錯誤。

    > [!TIP]
    > 若要減少從叢集預測寫入設計工具的資料行數目，請使用[[選取資料集中的資料行](select-columns-in-dataset.md)]，然後選取資料行的子集。 
    
4. 如果您想要讓結果包含完整輸入資料集，包括顯示結果（叢集指派）的資料行，請保留 [**檢查附加] 或 [取消核取僅結果**] 核取方塊。
  
    如果您清除此核取方塊，則只會傳回結果。 當您建立預測做為 web 服務的一部分時，這個選項可能會很有用。
  
5.  執行管道。  
  
### <a name="results"></a>結果

+  若要查看資料集中的值，請以滑鼠右鍵按一下模組，然後選取 [**視覺化**]。 或者，選取模組並切換至右窗格中的 [**輸出**] 索引標籤，按一下**埠輸出**中的長條圖圖示，將結果視覺化。

