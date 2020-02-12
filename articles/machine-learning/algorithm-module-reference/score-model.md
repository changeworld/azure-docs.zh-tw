---
title: 評分模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [評分模型] 模組，利用定型的分類或回歸模型來產生預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: c06d1268abe8afdeb03668131c3c61cfbafa44cd
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138182"
---
# <a name="score-model-module"></a>Score Model module

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組可使用定型的分類或回歸模型來產生預測。

## <a name="how-to-use"></a>使用方式

1. 將 [**評分模型**] 模組新增至您的管線。

2. 附加定型的模型和包含新輸入資料的資料集。 

    資料的格式應該與您所使用的定型模型類型相容。 輸入資料集的架構通常也應該符合用來定型模型之資料的架構。

3. 執行管道。

## <a name="results"></a>結果

使用[計分模型](./score-model.md)產生一組分數之後：

+ 產生一組用來評估模型精確度的計量（效能）。  您可以連接評分資料集來[評估模型](./evaluate-model.md)， 
+ 以滑鼠右鍵按一下模組，然後選取 [**視覺化**] 以查看結果的範例。
+ 將結果儲存至資料集。

分數（或預測值）可以有許多不同的格式，視模型和您的輸入資料而定：

- 針對分類模型，[計分模型](./score-model.md)會輸出類別的預測值，以及預測值的機率。
- 針對回歸模型，[計分模型](./score-model.md)只會產生預測的數值。


## <a name="publish-scores-as-a-web-service"></a>將分數發佈為 web 服務

計分的常見用法是將輸出當做預測性 web 服務的一部分傳回。 如需詳細資訊，請參閱本教學課程，瞭解如何在 Azure Machine Learning 中根據管線建立 web 服務：

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 