---
title: 設定個人化工具
description: 服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221213"
---
# <a name="configure-personalizer-learning-loop"></a>配置個人化學習迴圈

服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。

在"**配置"** 頁上，在 Azure 門戶中為該個人化工具資源配置學習迴圈。

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>為回饋迴圈配置獎勵

為學習迴圈使用獎勵佈建服務。 對以下值的更改將重置當前個人化模型，並重新訓練它與最後 2 天的資料。

> [!div class="mx-imgBorder"]
> ![配置回饋迴圈的獎勵值](media/settings/configure-model-reward-settings.png)

|值|目的|
|--|--|
|報酬等候時間|設定要讓個人化工具為排名呼叫收集報酬值的時間長度，這段時間從該排名呼叫發生的當下起算。 此值是通過詢問："個人服務人員應該等待獎勵呼叫多長時間？ 在此時段過後才抵達的報酬會記錄下來，但不會用於學習。|
|預設報酬|如果個人化工具未在「報酬等候時間」的時段內收到任何報酬呼叫，便會指派預設報酬。 預設情況下，在大多數情況下，預設獎勵為零 （0）。|
|報酬彙總|如果從相同的排名 API 呼叫收到多個報酬，便會使用下列其中一個彙總方法：**總和**或**最早**。 「最早」會挑選最早收到的分數，並捨棄其餘分數。 如果您希望在可能重複的呼叫之間獲得唯一的獎勵，則此功能非常有用。 |

更改這些值後，請確保選擇 **"保存**"。

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>配置探索以允許學習迴圈適應

個人化可以通過探索替代方法而不是使用經過訓練的模型預測來發現新模式並適應使用者行為隨時間的變化。 **"探索**"值確定使用探索應答的排名呼叫的百分比。

對此值的更改將重置當前個人化模型，並重新訓練它與最後 2 天的資料。

![勘探值確定通過探索應答的排名呼叫的百分比](media/settings/configure-exploration-setting.png)

更改此值後，請確保選擇 **"保存**"。

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>配置模型更新頻率以進行模型培訓

**模型更新頻率**設置模型訓練的頻率。

|頻率設置|目的|
|--|--|
|1 分鐘|使用個人儀**調試**應用程式代碼、執行演示或互動式測試機器學習方面時，一分鐘的更新頻率非常有用。|
|15 分鐘|對於要**密切跟蹤**使用者行為變化的情況，高模型更新頻率非常有用。 例如播放即時新聞、熱門內容或即時產品競標的網站。 在這些案例中，您可以使用 15 分鐘的頻率。 |
|1 小時|針對大多數使用案例，較低的更新頻率會比較有效。|

![模型更新頻率會設定要將新的個人化工具模型重新定型的頻率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

更改此值後，請確保選擇 **"保存**"。

## <a name="data-retention"></a>資料保留

**資料保留期間**會設定個人化工具要將資料記錄保留多少天。 必須有過去的資料記錄才能執行[離線評估](concepts-offline-evaluation.md)，此評估可用來測量個人化工具是否有效，並將學習原則最佳化。

更改此值後，請確保選擇 **"保存**"。

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>包括重置模型的設置

以下操作包括使用最近 2 天的資料立即對模型進行重新訓練。

* 報酬
* 探索

要[清除](how-to-manage-model.md)所有資料，請使用 [模型和學習設置 ] 頁面。

## <a name="next-steps"></a>後續步驟

[瞭解如何管理模型](how-to-manage-model.md)
