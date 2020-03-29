---
title: 管理模型和學習設置 - 個人化
description: 機器學習的模型和學習設置可以匯出到您自己的原始程式碼管理系統中進行備份。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624328"
---
# <a name="how-to-manage-model-and-learning-settings"></a>如何管理模型和學習設置

機器學習的模型和學習設置可以匯出到您自己的原始程式碼管理系統中進行備份。

## <a name="export-the-personalizer-model"></a>匯出個人化工具模型

從"**模型"和學習設置**的資源管理部分，查看模型創建和上次更新日期並匯出當前模型。 您可以使用 Azure 入口網站或個人化工具 API 來匯出要加以封存的模型檔案。

![匯出目前的個人化工具模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除學習迴圈的資料

1. 在 Azure 門戶中，對於個人化程式資源，在 **"模型和學習設置"** 頁上，選擇 **"清除資料**"。
1. 為了清除所有資料，並將學習迴圈重置為原始狀態，請選擇所有 3 個核取方塊。

    ![在 Azure 門戶中，清除來自個人化程式資源的資料。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |記錄個人化和獎勵資料。|此日誌記錄資料用於離線評估。 如果要重置資源，請清除資料。|
    |重置個人化程式模型。|這種模式在每次再培訓時都會發生變化。 此訓練頻率在 **"配置**"頁上的**上載模型頻率**中指定。 |
    |將學習策略設置為預設值。|如果更改了學習策略作為離線評估的一部分，則這將重置為原始學習策略。|

1. 選擇 **"清除所選資料**"以開始清除過程。 狀態在 Azure 通知中報告，在右上角的導航中。

## <a name="import-a-new-learning-policy"></a>導入新的學習策略

[學習策略](concept-active-learning.md#understand-learning-policy-settings)設置確定模型訓練的_超參數_。 執行[離線評估](how-to-offline-evaluation.md)以查找新的學習策略。

1. 打開[Azure 門戶](https://portal.azure.com)，然後選擇個人化工具資源。
1. 在 **"資源管理**"部分中選擇 **"模型和學習設置**"。
1. 對於 **"導入學習設置**"，請選擇使用上面指定的 JSON 格式創建的檔，然後選擇 **"上傳**"按鈕。

    等待已成功上載學習策略的通知。

## <a name="export-a-learning-policy"></a>匯出學習策略

1. 打開[Azure 門戶](https://portal.azure.com)，然後選擇個人化工具資源。
1. 在 **"資源管理**"部分中選擇 **"模型和學習設置**"。
1. 對於 **"導入學習設置**"，請選擇 **"匯出學習設置"** 按鈕。 這將檔`json`保存到本地電腦。

## <a name="next-steps"></a>後續步驟

[瞭解如何管理學習策略](how-to-manage-model.md)
