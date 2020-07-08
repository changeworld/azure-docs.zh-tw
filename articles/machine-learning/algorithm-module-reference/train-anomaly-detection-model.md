---
title: 將異常偵測模型定型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 [定型異常偵測模型] 模組來建立定型的異常偵測模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e8f0de9f879498acb71e97fa47c81b3447252d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749617"
---
# <a name="train-anomaly-detection-model-module"></a>定型異常偵測模型模組

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [定型異常偵測模型] 模組，建立定型的異常偵測模型。

此模組會將異常偵測模型和未標記資料集的一組參數當做輸入。 它會傳回已定型的異常偵測模型，以及一組用於定型資料的標籤。  

如需設計工具中提供之異常偵測演算法的詳細資訊，請參閱以[PCA 為基礎的異常偵測](pca-based-anomaly-detection.md)。  

## <a name="how-to-configure-train-anomaly-detection-model"></a>如何設定定型異常偵測模型 

1.  在設計工具中，將 [**訓練異常偵測模型**] 模組新增至您的管線。 您可以在 [**異常偵測**] 分類中找到此模組。

2. 連接為異常偵測設計的其中一個模組，例如以[PCA 為基礎的異常偵測](pca-based-anomaly-detection.md)。

    不支援其他類型的模型。 當您執行管線時，將會收到「所有模型都必須具有相同的學習模組類型」錯誤。  

3.  選擇 [標籤] 資料行，並設定演算法特定的其他參數，以設定異常偵測模組。  

4.  將訓練資料集附加至**定型異常偵測模型**的右側輸入。  

5.  提交管線。  

## <a name="results"></a>結果

定型完成後：

+ 若要查看模型的參數，請以滑鼠右鍵按一下模組，然後選取 [**視覺化**]。 

+ 若要建立預測，請使用「[計分模型](score-model.md)」模組搭配新的輸入資料。

+ 若要儲存定型模型的快照集，請選取模組。 然後選取右面板中 [**輸出 + 記錄**] 索引標籤底下的 [**註冊資料集**] 圖示。   

 
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 

如需設計工具模組特有的錯誤清單，請參閱設計工具的[例外狀況和錯誤碼（預覽）](designer-error-codes.md) 。
'