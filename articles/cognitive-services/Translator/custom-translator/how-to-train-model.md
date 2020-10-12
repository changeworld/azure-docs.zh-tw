---
title: 訓練模型 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 建置翻譯模型時，訓練模型是很重要的步驟。 訓練會根據您為該訓練選取的文件來進行。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 824516a327d45feb5b6a084a113633bd3a486abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88508332"
---
# <a name="train-a-model"></a>將模型定型

定型模型是建立轉譯模型的第一個且最重要的步驟，否則無法建立模型。 訓練會根據您為訓練選取的文件來進行。 當您選取「訓練」檔案類型的檔時，請注意10000平行句子的最低需求。 當您選取 [檔] 時，我們會顯示要引導您的訓練句子總數。 當您只選取字典檔案類型的檔來定型模型時，這項需求並不適用。

訓練模型：

1. 選取您要用來建置模型的專案。

2. 專案的 [資料] 索引標籤會顯示專案語言組的所有相關文件。 手動選取您要用來訓練模型的文件。 您可以從此畫面中選取訓練、調整及測試文件。 您也可以只選取訓練集，然後讓自訂翻譯工具為您建立調整和測試集。

    - 文件名稱：文件的名稱。

    - 配對：如果這份文件是平行或單一語言文件。 單一語言文件目前不支援進行訓練。

    - 文件類型：可以是訓練、調整、測試或字典。

    - 語言組：這會顯示專案的原始和目標語言。

    - 來源句子：顯示從原始程式檔解壓縮的句子數目。

    - 目標句子：顯示從目標檔案解壓縮的句子數目。

    ![定型模型](media/how-to/how-to-train-model.png)

3. 按一下 [建立模型] 按鈕。

4. 在對話方塊中，指定模型的名稱。 依預設，當您按一下 [建立模型] 按鈕時，會選取 [立即訓練] 以啟動定型管線。 您可以選取 [儲存為草稿] 以建立模型中繼資料，並將模型置於草稿狀態，但模型定型將不會啟動。 稍後，您必須手動選取草稿狀態中的模型進行定型。

5. 按一下 [建立模型] 按鈕。

    ![訓練模型對話方塊](media/how-to/how-to-train-model-2.png)

6. 自訂翻譯會提交定型，並在 [模型] 索引標籤中顯示定型的狀態。

    ![訓練模型頁面](media/how-to/how-to-train-model-3.png)

>[!Note]
>自訂翻譯在任何時間點都支援工作區中10個並行定型。

## <a name="modify-a-model-name"></a>修改模型名稱

您可以從 [模型詳細資料] 頁面修改模型名稱。

1. 在 [專案] 頁面中，按一下模型存在的專案名稱。
2. 按一下 [模型] 索引標籤。
3. 按一下模型名稱以查看模型詳細資料。
4. 按一下鉛筆圖示。

    ![編輯模型](media/how-to/how-to-edit-model.png)

5. 在對話方塊中，變更模型名稱，並為您的模型提供有意義的名稱。

    ![編輯多個對話方塊](media/how-to/how-to-edit-model-dialog.png)

6. 按一下 [儲存]。

## <a name="next-steps"></a>後續步驟

- 了解[如何檢視模型詳細資料](how-to-view-model-details.md)。
