---
title: 評分模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「評分模型」模組，利用定型的分類或回歸模型來產生預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0cc367b86507d6d819608795851052e8a0c1c73b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890312"
---
# <a name="score-model"></a>計分模型

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組，利用定型的分類或回歸模型來產生預測。

## <a name="how-to-use"></a>如何使用

1. 將 [ **評分模型** ] 模組新增至您的管線。

2. 附加已定型的模型和包含新輸入資料的資料集。 

    資料的格式應與您所使用之定型模型的類型相容。 輸入資料集的架構通常也應該符合用來定型模型之資料的架構。

3. 提交管線。

## <a name="results"></a>結果

使用 [計分模型](./score-model.md)產生一組分數之後：

+ 若要產生一組用來評估模型精確度 (效能) 的計量，您可以連接計分資料集來 [評估模型](./evaluate-model.md)。 
+ 以滑鼠右鍵按一下模組，然後選取 [ **視覺化** ] 以查看結果的範例。
<!-- + To Save the results to a dataset. -->

分數或預測值可能採用許多不同的格式，視模型和您的輸入資料而定：

- 針對分類模型， [評分模型](./score-model.md) 會輸出類別的預測值，以及預測值的機率。
- 針對回歸模型， [評分模型](./score-model.md) 只會產生預測數值。


## <a name="publish-scores-as-a-web-service"></a>將分數發佈為 web 服務

計分的常見用法是在預測性 web 服務中傳回輸出。 如需詳細資訊，請參閱 [本教學](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) 課程，以瞭解如何在 Azure Machine Learning 表設計工具中根據管線部署即時端點。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 