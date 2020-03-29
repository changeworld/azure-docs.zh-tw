---
title: 如何執行離線評估 - 個人化
titleSuffix: Azure Cognitive Services
description: 本文將向您展示如何使用離線評估來衡量應用的有效性並分析學習迴圈。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622800"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>通過離線評估分析學習迴圈

了解如何完成離線評估並了解結果。

離線評估允許您測量個人化程式與應用程式的預設行為相比的有效性，瞭解哪些功能對個人化貢獻最大，並自動探索新的機器學習值。

閱讀[離線評估](concepts-offline-evaluation.md)進一步了解。

## <a name="prerequisites"></a>Prerequisites

* 配置的個人化器迴圈
* 個人化器迴圈必須具有具有代表性的資料量 - 作為一個球場，我們建議在其日誌中至少 50，000 個事件，以獲得有意義的評估結果。 (選擇性) 您可能先前也已匯出「學習原則」__ 檔案，以便在相同評估中進行比較和測試。

## <a name="run-an-offline-evaluation"></a>執行離線評估

1. 在[Azure 門戶](https://azure.microsoft.com/free/)中，找到個人化程式資源。
1. 在 Azure 門戶中，轉到 **"評估**"部分並選擇 **"創建評估**"。
    ![在 Azure 門戶中，轉到 [評估] 部分並選擇 [創建評估]。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 設定下列值：

    * 評估名稱。
    * 開始日期和結束日期 - 這些是指定要在評估中使用的資料範圍的日期。 此資料必須存在於日誌中，如[資料保留](how-to-settings.md)值中指定的那樣。
    * 優化發現設置為**是**。

    > [!div class="mx-imgBorder"]
    > ![選擇離線評估設置](./media/offline-evaluation/create-an-evaluation-form.png)

1. 通過選擇 **"確定"** 開始評估。

## <a name="review-the-evaluation-results"></a>查看評估結果

評估可能需要很長的執行時間，這取決於要處理的資料量、要比較的學習原則數目、是否已要求最佳化。

完成後，您可以從評估清單中選擇評估，然後選擇將**應用程式的分數與其他潛在的學習設置進行比較**。 當您要查看當前學習策略相對於新策略的性能時，請選擇此功能。

1. 回顧[學習策略](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)的表現。

    > [!div class="mx-imgBorder"]
    > [![審核評估結果](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. 選擇 **"應用"** 以應用最適合資料的模型的策略。

## <a name="next-steps"></a>後續步驟

* 詳細瞭解[離線評估的工作原理](concepts-offline-evaluation.md)。
