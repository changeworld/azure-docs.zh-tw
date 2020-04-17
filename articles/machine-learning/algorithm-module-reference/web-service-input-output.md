---
title: Web 服務輸入/輸出
description: 瞭解 Azure 機器學習設計器中的 Web 服務模組(預覽版)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462083"
---
# <a name="web-service-inputoutput"></a>Web 服務輸入/輸出

本文介紹 Azure 機器學習設計器(預覽)中的**Web 服務輸入**模組和**Web 服務輸出**模組。

**Web 服務輸入**模組只能連接到類型**為 DataFrameDirectory**的輸入連接埠。 **並且 Web 服務輸出**模組只能從具有**類型 DataFrameDirectory**的輸出埠進行連接。 這兩個模組可以在模組樹中找到,在**Web 服務**類別下。 

**Web 服務輸入**模組用於指示使用者資料進入管道的位置 **,Web 服務輸出**模組用於指示在即時推理管道中返回用戶數據的位置。

## <a name="how-to-use-web-service-inputoutput"></a>如何使用 Web 服務輸入/輸出

- 當您從訓練管道創建即時推理管道時,將自動添加**Web 服務輸入**和**Web 服務輸出**模組,以顯示使用者數據進入管道的位置以及返回數據的位置。 

    詳細瞭解[如何建立即時推理導管](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)。

    > [!NOTE]
    > 自動生成即時推理管道是一個基於規則的盡力過程,不能保證正確性。 您可以手動添加或刪除 Web**服務輸入/ 輸出**模組,以滿足您的要求。 確保即時推理管道中至少有一個**Web 服務輸入**模組和一個**Web 服務輸出**模組。 如果您有多個**Web 服務輸入**或**Web 服務輸出**模組,請確保它們具有唯一名稱,您可以在模組的右側面板中輸入該名稱。

- 還可以透過將**Web 服務輸入**模組和**Web 服務輸出**模組添加到未提交的管道來手動創建即時推理管道。

    > [!NOTE]
    >  管道類型將在您第一次提交時確定。 因此,如果要創建即時推理管道,請確保在首次提交之前添加**Web 服務輸入**和**Web 服務輸出**模組。

   下面的範例展示如何從**執行 Python 文稿**模組手動建立即時推理管道。 

   ![範例](media/module/web-service-input-output-example.png)
   
   提交管道並成功完成運行後,您將能夠部署即時終結點。
   
   > [!NOTE]
   >  在上面的示例中 **,輸入數據手動**提供 Web 服務輸入的數據架構,是部署即時終結點所必需的。 通常,應始終將模組或數據集連接到**Web 服務輸入**連接的埠以提供數據架構。
   
## <a name="next-steps"></a>後續步驟
詳細瞭解[如何設定即時終結點](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)。
請參考 Azure 機器學習[可用的模組集](module-reference.md)。