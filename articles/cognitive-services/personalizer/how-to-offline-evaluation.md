---
title: 如何執行離線評估-個人化工具
titleSuffix: Azure Cognitive Services
description: 本文將說明如何使用離線評估來測量應用程式的有效性，並分析您的學習迴圈。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: d1e11ffcb96bc233604464e90edb7dd42578bf08
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132699"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>使用離線評估來分析您的學習迴圈

了解如何完成離線評估並了解結果。

離線評估可讓您測量有效個人化工具與應用程式預設行為的比較方式、瞭解哪些功能對個人化最有影響，以及自動探索新的機器學習服務值。

閱讀[離線評估](concepts-offline-evaluation.md)進一步了解。

## <a name="prerequisites"></a>必要條件

* 已設定的個人化工具迴圈
* 個人化工具迴圈必須有代表性的資料量-作為約略，我們建議記錄中至少有50000個事件，以取得有意義的評估結果。 (選擇性) 您可能先前也已匯出「學習原則」__ 檔案，以便在相同評估中進行比較和測試。

## <a name="run-an-offline-evaluation"></a>執行離線評估

1. 在 [ [Azure 入口網站](https://azure.microsoft.com/free/)中，找出您的個人化工具資源。
1. 在 Azure 入口網站中，移至 [**評估**] 區段，然後選取 [**建立評估**]。
    ![在 Azure 入口網站中，移至 [評估] 區段，然後選取 [建立評估]。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 設定下列值：

    * 評估名稱。
    * 開始和結束日期-這些是用來指定要在評估中使用之資料範圍的日期。 此資料必須出現在記錄檔中，如[資料保留](how-to-settings.md)值中所指定。
    * 優化探索設定為 **[是]**。

    > [!div class="mx-imgBorder"]
    > ![選擇離線評估設定](./media/offline-evaluation/create-an-evaluation-form.png)

1. 選取 **[確定]** 以開始評估。

## <a name="review-the-evaluation-results"></a>審查評估結果

評估可能需要很長的執行時間，這取決於要處理的資料量、要比較的學習原則數目、是否已要求最佳化。

完成後，您可以從評估清單中選取評估，然後選取 [**比較應用程式的分數與其他潛在的學習設定**]。 當您想要查看目前的學習原則執行方式與新原則的比較時，請選取此功能。

1. 審查[學習原則](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)的效能。

    > [!div class="mx-imgBorder"]
    > [![審查評估結果](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 選取 **[** 套用] 以套用原則，以改善最適合您資料的模型。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[離線評估如何工作](concepts-offline-evaluation.md)。
