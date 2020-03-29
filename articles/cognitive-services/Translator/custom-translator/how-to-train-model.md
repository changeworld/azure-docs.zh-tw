---
title: 訓練模型 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 建置翻譯模型時，訓練模型是很重要的步驟。 訓練會根據您為該訓練選取的文件來進行。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595692"
---
# <a name="train-a-model"></a>將模型定型

訓練模型是建置翻譯模型的重要步驟，因為沒有訓練，模型就無法建立。 訓練會根據您為訓練選取的文件來進行。

訓練模型：

1.  選取您要用來建置模型的專案。

2.  專案的 [資料] 索引標籤會顯示專案語言組的所有相關文件。 手動選取您要用來訓練模型的文件。 您可以從此畫面中選取訓練、調整及測試文件。 您也可以只選取訓練集，然後讓自訂翻譯工具為您建立調整和測試集。

    -  文件名稱：文件的名稱。

    -  配對：如果這份文件是平行或單一語言文件。 單一語言文件目前不支援進行訓練。

    -  文件類型：可以是訓練、調整、測試或字典。

    -  語言組：這會顯示專案的原始和目標語言。

    -  源句子：顯示從原始檔案中提取的句子數。

    -  目標句子：顯示從目的檔案中提取的句子數。

    ![定型模型](media/how-to/how-to-train-model.png)

3.  選取 [訓練] 按鈕。

4.  在對話方塊中，指定您模型的名稱。

5.  按一下 [訓練模型]。

    ![訓練模型對話方塊](media/how-to/how-to-train-model-2.png)

6.  自訂翻譯工具會提交訓練，並在 [模型] 索引標籤中顯示訓練的狀態。

    ![訓練模型頁面](media/how-to/how-to-train-model-3.png)

>[!Note]
>自訂轉換器支援工作區內任意時間點的 10 個併發訓練。


## <a name="edit-a-model"></a>編輯模型

您可以使用模型詳細資料頁面上的 [編輯] 連結來編輯模型。

1.  按一下鉛筆圖示。

    ![編輯模型](media/how-to/how-to-edit-model.png)

2.  在對話方塊中變更

    1.  模型名稱 (必要)：為您的模型提供有意義的名稱。

        ![編輯多個對話方塊](media/how-to/how-to-edit-model-dialog.png)

3.  按一下 [儲存]。


## <a name="next-steps"></a>後續步驟

- 了解[如何檢視模型詳細資料](how-to-view-model-details.md)。
