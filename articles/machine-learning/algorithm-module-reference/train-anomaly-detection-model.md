---
title: 列車異常檢測模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用訓練異常檢測模型模組創建經過訓練的異常檢測模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502967"
---
# <a name="train-anomaly-detection-model"></a>定型異常偵測模型

本文介紹如何使用 Azure 機器學習設計器（預覽）中的**訓練異常檢測模型**模組創建經過訓練的異常檢測模型。

該模組將異常檢測模型和未標記資料集的一組模型參數作為輸入。 它返回經過訓練的異常檢測模型以及一組訓練資料的標籤。  

有關設計器中提供的異常檢測演算法的詳細資訊，請參閱以下主題： 

+ [基於 PCA 的異常檢測](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>如何配置列車異常檢測模型 

1.  在設計器中將**訓練異常檢測模型**模組添加到管道中。 您可以在**異常檢測**類別中找到此模組。

2. 連接專為異常檢測而設計的模組之一，例如[基於 PCA 的異常檢測](pca-based-anomaly-detection.md)

    不支援其他類型的模型;運行管道時，您會收到錯誤：所有模型必須具有相同的學員類型。  

3.  通過選擇標籤列並設置特定于演算法的其他參數來配置異常檢測模組。  

4.  將訓練資料集附加到**列車異常檢測模型**的右側輸入。  

5.  提交管道。  

## <a name="results"></a>結果

培訓完成後：

+ 要查看模型的參數，請按右鍵模組並選擇 **"視覺化**"。 

+ 要創建預測，請使用具有新輸入資料[的分數模型](score-model.md)。

+ 要保存已訓練的模型的快照，請選擇模組，然後按一下右側面板中的 **"輸出_logs"** 選項卡下的 **"註冊"資料集**圖示。   

 
## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 

有關特定于設計器模組的錯誤清單[，請參閱設計器的異常和錯誤代碼（預覽](designer-error-codes.md)）。
'