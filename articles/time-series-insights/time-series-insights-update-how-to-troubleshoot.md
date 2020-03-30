---
title: 診斷和排除預覽環境故障 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何診斷和排除 Azure 時間序列見解預覽環境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152637"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>診斷和排除預覽環境

本文摘要說明使用 Azure 時間序列深入解析預覽環境時，可能會遇到的幾個常見問題。 文章中也會描述每個問題的可能發生原因和解決方案。

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>問題：我在預覽資源管理器中找不到我的環境

如果您沒有存取時間序列深入解析環境的權限，就可能會發生這個問題。 使用者需要讀取器層級存取角色，才能檢視他們的時間序列深入解析環境。 要驗證當前存取層級並授予其他存取權限，請轉到[Azure 門戶](https://portal.azure.com/)中的時序見解資源上的 **"資料訪問策略"** 部分。

  [![驗證資料訪問策略。](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>問題：預覽資源管理器中看不到任何資料

資料可能不會顯示在[Azure 時間序列預覽資源管理器](https://insights.timeseries.azure.com/preview)中的原因有幾個。

- 事件來源可能沒有接收資料。

    請確認事件來源 (事件中樞或 IoT 中樞)，是否正從您的標籤或執行個體接收資料。 請在 Azure 入口網站中，瀏覽至您資源的概觀頁面以進行確認。

    [![查看儀表板指標概述。](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 事件來源資料不是 JSON 格式。

    時間序列深入解析僅支援 JSON 資料。 對於 JSON 示例，請閱讀[支援的 JSON 形狀](./how-to-shape-query-json.md)。

- 事件來源索引鍵遺漏必要的權限。

  * 針對 IoT 中樞，您必須提供具有「服務連接」**** 權限的索引鍵。

    [![驗證 IoT 中心許可權。](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * 策略**iothub 擁有者****和服務**都正常工作，因為它們具有**服務連接**許可權。

  * 針對事件中樞，您必須提供具有「接聽」**** 權限的索引鍵。
  
    [![查看事件中心許可權。](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * **讀取****和管理原則**都工作，因為它們具有**偵聽**許可權。

- 您提供的取用者群組不是時間序列深入解析專用的。

    在註冊 IoT 中樞或事件中樞期間指定的取用者群組是要用於讀取資料的。 此消費者組必須每個環境是唯一的。 如果共用取用者群組，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

- 您在佈建期間指定的時間序列識別碼屬性不正確、遺漏，或為 null。

    如果時間序列識別碼屬性設定不正確，佈建環境時就可能會發生這個問題。 有關詳細資訊，請閱讀[選擇時間序列 ID 的最佳做法](./time-series-insights-update-how-to-id.md)。 目前您無法將現有的時間序列深入解析環境更新為使用不同的時間序列識別碼。

## <a name="problem-some-data-shows-but-some-is-missing"></a>問題：顯示某些資料，但缺少一些資料

您可能傳送了沒有時間序列識別碼的資料。

- 在酬載中傳送沒有時間序列識別碼欄位的事件時，就可能會發生這個問題。 有關詳細資訊，請閱讀支援的[JSON 形狀](./how-to-shape-query-json.md)。
- 發生這個問題的原因可能是因為您的環境正在進行節流。

    > [!NOTE]
    > 目前時間序列深入解析支援最高 6 Mbps 的擷取速率。

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>問題：資料顯示，但現在攝入已停止

- 事件源金鑰可能已重新生成，並且預覽環境需要新的事件源金鑰。

當創建事件源時提供的金鑰不再有效時，將發生此問題。 您將在中心看到遙測資料，但在時間序列見解中看不到入口接收的消息。 如果您不確定金鑰是否已重新生成，則可以搜索事件中心的活動日誌以查找"創建或更新命名空間授權規則"，或搜索 IoT 中心"創建或更新 IotHub 資源"。 

使用新鍵在 Azure 門戶中打開中心資源並複製新金鑰，更新時間序列見解預覽環境。 導航到 TSI 資源，然後按一下事件源。 

   [![更新金鑰。](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

選擇已停止引入的事件源，粘貼到新鍵中，然後按一下"保存"。

   [![更新金鑰。](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>問題：事件源的時間戳記屬性名稱不起作用

請確定名稱和值符合下列規則︰

* 時間戳記屬性名稱區分大小寫。
* 作為 JSON 字串來自事件源的時間戳記屬性值具有格式`yyyy-MM-ddTHH:mm:ss.FFFFFFFK`。 此類型字串的其中一個範例為 `“2008-04-12T12:53Z”`。

若要確保您已擷取時間戳記屬性名稱且正常運作，最簡單方式是使用時間序列深入解析預覽總管。 在時間序列深入解析預覽總管中，當您輸入時間戳記屬性名稱之後，使用圖表選取一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件]**** 選項。 第一個資料行標頭就是您的時間戳記屬性名稱。 它的字組 `Timestamp` 旁邊應該有 `($ts)`，而非：

* `(abc)`，這表示時間序列深入解析將資料值讀取為字串。
* **日曆**圖示，指示時序見解將資料值顯示為日期時間。
* `#`，這表示時間序列深入解析將資料值讀取為整數。

如果未明確指定時間戳記屬性，就會使用事件的 IoT 中樞或事件中樞加入佇列時間作為預設時間戳記。

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>問題：我無法查看資源管理器中暖存儲的資料

- 您可能最近預配了暖存儲，並且資料仍在流入。
- 您可能已刪除暖存儲，在這種情況下，您將遺失資料。

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>問題：無法查看或編輯時間序列模型

- 您可能存取了時間序列深入解析 S1 或 S2 環境。

   時間序列模型僅在即用即付環境中支援。 有關如何從時序見解預覽資源管理器訪問 S1 或 S2 環境的詳細資訊，請閱讀[資源管理器中的視覺化資料](./time-series-insights-update-explorer.md)。

   [![環境中沒有事件。](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 您可能沒有檢視及編輯模型的權限。

   使用者需要參與者層級存取權，才能編輯及檢視他們的時間序列模型。 要驗證當前存取層級並授予其他存取權限，請轉到 Azure 門戶中時間序列見解資源上的 **"資料訪問策略"** 部分。

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>問題：預覽資源管理器中的所有實例都缺少父實例

如果您的環境未定義時間序列模型階層，就可能會發生這個問題。 有關詳細資訊，請閱讀[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

  [![未父級實例將顯示警告。](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 閱讀[使用時間序列模型](./time-series-insights-update-how-to-tsm.md)。

- 瞭解[支援的 JSON 形狀](./how-to-shape-query-json.md)。

- 在 Azure 時間序列見解預覽中查看[計畫和限制](./time-series-insights-update-plan.md)。
