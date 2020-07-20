---
title: 設定個人化工具
description: 服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 4c0cbf35a37f6b3eb134992b34b23fd9d7be47ed
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344334"
---
# <a name="configure-personalizer-learning-loop"></a>設定個人化工具學習迴圈

服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。

在 [設定 **] 頁面上的 [個人化工具**] 資源的 [Azure 入口網站中設定學習迴圈。

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>規劃設定變更

因為某些設定變更會[重設您的模型](#settings-that-include-resetting-the-model)，所以您應該規劃您的設定變更。

如果您打算使用[新手模式](concept-apprentice-mode.md)，請務必先檢查您的個人化工具設定，再切換到新手模式。

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>包含重設模型的設定

下列動作會使用過去2天內最多可用的資料，觸發模型的重新定型。

* 報酬
* 探索

若要[清除](how-to-manage-model.md)您的所有資料，請使用 [**模型與學習設定**] 頁面。

## <a name="configure-rewards-for-the-feedback-loop"></a>設定意見反應迴圈的報酬

設定學習迴圈使用報酬的服務。 對下列值所做的變更將會重設目前的個人化工具模型，並以過去2天的資料重新定型。

> [!div class="mx-imgBorder"]
> ![設定意見反應迴圈的獎勵值](media/settings/configure-model-reward-settings.png)

|值|目的|
|--|--|
|報酬等候時間|設定要讓個人化工具為排名呼叫收集報酬值的時間長度，這段時間從該排名呼叫發生的當下起算。 此值的設定方式是詢問：「應該個人化工具等待報酬電話多久？」 在此時段過後才抵達的報酬會記錄下來，但不會用於學習。|
|預設報酬|如果個人化工具未在「報酬等候時間」的時段內收到任何報酬呼叫，便會指派預設報酬。 根據預設，在大部分的情況下，預設報酬為零（0）。|
|報酬彙總|如果從相同的排名 API 呼叫收到多個報酬，便會使用下列其中一個彙總方法：**總和**或**最早**。 「最早」會挑選最早收到的分數，並捨棄其餘分數。 如果您想要在可能重複的呼叫之間進行唯一報酬，這會很有用。 |

變更這些值之後，請務必選取 [**儲存**]。

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>設定探索以允許學習迴圈調整

個人化能夠探索新的模式，並藉由流覽替代專案，而不是使用定型模型的預測，來調整一段時間的使用者行為變更。 [**探索**] 值會決定要使用探索來回答的排名呼叫百分比。

對此值所做的變更將會重設目前的個人化工具模型，並以過去2天的資料重新定型。

![探索值會決定要使用探索來回答的排名呼叫百分比](media/settings/configure-exploration-setting.png)

變更此值之後，請務必選取 [**儲存**]。

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>設定模型定型的模型更新頻率

**模型更新頻率**會設定定型模型的頻率。

|頻率設定|目的|
|--|--|
|1 分鐘|當您使用個人化工具、進行示範，或以互動方式測試機器學習**方面的程式**代碼時，一分鐘的更新頻率會很有用。|
|15 分鐘|如果您想要**仔細追蹤**使用者行為的變更，高模型更新頻率很有用。 例如播放即時新聞、熱門內容或即時產品競標的網站。 在這些案例中，您可以使用 15 分鐘的頻率。 |
|1 小時|針對大多數使用案例，較低的更新頻率會比較有效。|

![模型更新頻率會設定要將新的個人化工具模型重新定型的頻率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

變更此值之後，請務必選取 [**儲存**]。

## <a name="data-retention"></a>資料保留

**資料保留期間**會設定個人化工具要將資料記錄保留多少天。 必須有過去的資料記錄才能執行[離線評估](concepts-offline-evaluation.md)，此評估可用來測量個人化工具是否有效，並將學習原則最佳化。

變更此值之後，請務必選取 [**儲存**]。



## <a name="next-steps"></a>後續步驟

[瞭解如何管理您的模型](how-to-manage-model.md)
