---
title: 將資料分配給群集：模組引用
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"將資料分配給群集"模組來對群集模型進行評分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477709"
---
# <a name="module-assign-data-to-clusters"></a>模組：將資料分配給群集

本文介紹如何在 Azure 機器學習設計器（預覽）中使用 *"將資料分配給群集*"模組。 模組通過使用*K-means 聚類*演算法訓練的聚類模型生成預測。

將資料分配給群集模組返回包含每個新資料點的可能分配的資料集。 

## <a name="how-to-use-assign-data-to-clusters"></a>如何使用將資料分配給群集
  
1. 在 Azure 機器學習設計器中，找到以前訓練的群集模型。 可以使用以下任一方法創建和訓練聚類模型：  
  
    - 使用[K-意味著聚類](k-means-clustering.md)模組配置 K-表示聚類演算法，並使用資料集和訓練群集模型模組（本文）對模型進行訓練。  
  
    - 您還可以從工作區中的 **"已保存模型"** 組中添加現有的已訓練的群集模型。

2. 將訓練的模型附加到**將資料分配給群集**的左側輸入埠。  

3. 將新資料集附加到輸入。 

   在此資料集中，標籤是可選的。 通常，聚類是一種無監督的學習方法。 您不應提前知道類別。 但是，輸入列必須與訓練聚類模型時使用的列相同，否則會發生錯誤。

    > [!TIP]
    > 要減少從群集預測中寫入設計器的列數，請使用[資料集中的"選擇"列](select-columns-in-dataset.md)，然後選擇列的子集。 
    
4. 如果希望結果包含完整的輸入資料集（包括顯示結果的列（群集分配），則僅選中"**檢查結果"或"取消選中結果**"核取方塊。
  
    如果清除此核取方塊，則僅返回結果。 當您創建作為 Web 服務的一部分的預測時，此選項可能很有用。
  
5.  提交管道。  
  
### <a name="results"></a>結果

+  要查看資料集中的值，請按右鍵模組，然後選擇 **"視覺化**"。 或者選擇模組並切換到右側面板中的 **"輸出"** 選項卡，按一下 **"埠輸出**"中的長條圖圖示以視覺化結果。

