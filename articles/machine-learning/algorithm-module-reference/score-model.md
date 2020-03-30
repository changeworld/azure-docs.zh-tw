---
title: 分數模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習中的分數模型模組使用經過訓練的分類或回歸模型生成預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 56d8cad05a42da8de680ade487dddee9a97aab3a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364185"
---
# <a name="score-model-module"></a>Score Model module

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組使用經過訓練的分類或回歸模型生成預測。

## <a name="how-to-use"></a>使用方式

1. 將**分數模型**模組添加到管道中。

2. 附加已訓練的模型和包含新輸入資料的資料集。 

    資料應採用與您使用的已訓練模型類型相容的格式。 輸入資料集的架構通常還應與用於訓練模型的資料的架構匹配。

3. 提交管道。

## <a name="results"></a>結果

使用[分數模型](./score-model.md)生成一組分數後：

+ 要生成一組用於評估模型準確性（性能）的指標，可以將評分資料集連接到[評估模型](./evaluate-model.md)， 
+ 按右鍵模組並選擇 **"視覺化"** 以查看結果示例。
<!-- + To Save the results to a dataset. -->

分數或預測值可以採用許多不同的格式，具體取決於模型和輸入資料：

- 對於分類模型，[分數模型](./score-model.md)輸出類的預測值以及預測值的概率。
- 對於回歸模型，[分數模型](./score-model.md)僅生成預測的數值。


## <a name="publish-scores-as-a-web-service"></a>將分數發佈為 Web 服務

評分的常見用途是將輸出作為預測 Web 服務的一部分返回。 有關詳細資訊，請參閱有關如何基於 Azure 機器學習設計器中的管道部署即時終結點的[教程](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 