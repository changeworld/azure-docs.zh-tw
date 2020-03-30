---
title: 添加行：模組引用
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"添加行"模組串聯兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477726"
---
# <a name="add-rows-module"></a>添加行模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以串聯兩個資料集。 在串聯中，第二個資料集的行將添加到第一個資料集的末尾。  
  
資料列串連在如下的案例中很有用：  
  
+ 您已經產生一系列評估統計資料，想要將它們結合成一個資料表，以更輕鬆地產生報告。  
  
+ 您已使用不同的資料集，想要合併資料集來建立最終資料集。  

## <a name="how-to-use-add-rows"></a>如何使用"添加行"  

要從兩個資料集串聯行，這些行必須具有相同的架構。 這意味著列數相同，列中的資料類型相同。

1.  將 **"添加行"** 模組拖動到管道中，您可以在 **"資料轉換**"下找到它。

2. 連接至兩個輸入連接埠的資料集。 您想要附加的資料集應該連接到第二個 (右) 的連接埠。 
  
3.  提交管道。 輸出資料集中的行數應等於兩個輸入資料集的行總和。

    如果將同一資料集添加到 **"添加行"** 模組的兩個輸入，則資料集將重複。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 