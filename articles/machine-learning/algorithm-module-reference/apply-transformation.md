---
title: 應用轉換：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用應用轉換模組基於以前計算的轉換修改輸入資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395260"
---
# <a name="apply-transformation-module"></a>套用轉換模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以基於以前計算的轉換修改輸入資料集。

例如，如果使用 z 分數使用**標準化資料**模組正常化訓練資料，則使用在評分階段為訓練計算的 z 得分值。 在 Azure 機器學習中，可以將正常化方法另存為轉換，然後使用**應用轉換**在評分前將 z 分數應用於輸入資料。

## <a name="how-to-save-transformations"></a>如何保存轉換

設計器允許您將資料轉換另存為**資料集**，以便在其他管道中使用它們。

1. 選擇已成功運行的資料轉換模組。

1. 選擇"**輸出 + 日誌**"選項卡。

1. 選擇 **"保存"圖示**以保存**結果轉換**。

## <a name="how-to-use-apply-transformation"></a>如何使用應用轉換  
  
1. 將 **"應用轉換**"模組添加到管道中。 您可以在模組調色板的 **"模型評分&評估**部分找到此模組。 
  
1. 在模組調色板中的**資料集** > **"我的資料集**"下查找要使用的已保存的轉換。

1. 將保存的轉換的輸出連接到**應用轉換**模組的左輸入埠。

    資料集應具有與最初為其設計轉換的資料集完全相同的架構（列數、列名、資料類型）。  
  
1. 將所需模組的資料集輸出連接到**應用轉換**模組的右側輸入埠。
  
1. 要將轉換應用於新資料集，請運行管道。  

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 