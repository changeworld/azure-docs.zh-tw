---
title: 加入資料列：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [加入資料列] 模組來串連兩個資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477726"
---
# <a name="add-rows-module"></a>新增資料列模組

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組來串連兩個資料集。 在串連中，第二個資料集的資料列會加入至第一個資料集的結尾。  
  
資料列串連在如下的案例中很有用：  
  
+ 您已經產生一系列評估統計資料，想要將它們結合成一個資料表，以更輕鬆地產生報告。  
  
+ 您已使用不同的資料集，想要合併資料集來建立最終資料集。  

## <a name="how-to-use-add-rows"></a>如何使用加入資料列  

若要串連兩個資料集中的資料列，資料列必須有完全相同的架構。 這表示資料行中的資料行數目和相同的資料類型。

1.  將 [**加入資料列**] 模組拖曳至管線中，您可以在 [**資料轉換**] 下找到它。

2. 連接至兩個輸入連接埠的資料集。 您想要附加的資料集應該連接到第二個 (右) 的連接埠。 
  
3.  提交管線。 輸出資料集中的資料列數目應該等於這兩個輸入資料集的資料列總和。

    如果您將相同的資料集加入至 [**加入資料列**] 模組的兩個輸入，則資料集會重複。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 