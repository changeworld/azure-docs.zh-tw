---
title: 針對 Gen2 環境進行診斷和疑難排解-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何診斷和疑難排解 Azure 時間序列深入解析 Gen2 環境。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: fcd73a5bb6de162fc258d93398b7d00044429588
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097575"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>診斷 Azure 時間序列深入解析 Gen2 環境並進行疑難排解

本文摘要說明當您使用 Azure 時間序列深入解析 Gen2 環境時，可能會遇到的幾個常見問題。 文章中也會描述每個問題的可能發生原因和解決方案。

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>問題：在 Gen2 Explorer 中找不到我的環境

如果您沒有存取時間序列深入解析環境的許可權，就可能會發生此問題。 使用者需要讀取器層級存取角色，才能檢視他們的時間序列深入解析環境。 若要確認目前的存取層級並授與其他存取權，請移至[Azure 入口網站](https://portal.azure.com/)中時間序列深入解析資源的 [**資料存取原則**] 區段。

  [![驗證資料存取原則。](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>問題： Gen2 Explorer 中未顯示任何資料

您的資料可能不會出現在[Azure 時間序列深入解析 Gen2 Explorer](https://insights.timeseries.azure.com/preview)中的常見原因有好幾個。

- 事件來源可能沒有接收資料。

    請確認事件來源 (事件中樞或 IoT 中樞)，是否正從您的標籤或執行個體接收資料。 請在 Azure 入口網站中，瀏覽至您資源的概觀頁面以進行確認。

    [![請參閱儀表板計量總覽。](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 事件來源資料不是 JSON 格式。

    時間序列深入解析僅支援 JSON 資料。 如需 JSON 範例，請閱讀[支援的 json 圖形](./how-to-shape-query-json.md)。

- 事件來源索引鍵遺漏必要的權限。

  * 針對 IoT 中樞，您必須提供具有「服務連接」**** 權限的索引鍵。

    [![驗證 IoT 中樞許可權。](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * 原則**iothubowner**和**服務**都有作用，因為它們具有**服務連接**許可權。

  * 針對事件中樞，您必須提供具有「接聽」**** 權限的索引鍵。
  
    [![查看事件中樞許可權。](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * 「**讀取**」和「**管理**」原則都有作用，因為它們具有「**接聽**」許可權。

- 您提供的取用者群組不是時間序列深入解析專用的。

    在註冊 IoT 中樞或事件中樞期間指定的取用者群組是要用於讀取資料的。 此取用者群組在每個環境中必須是唯一的。 如果共用取用者群組，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

- 您在佈建期間指定的時間序列識別碼屬性不正確、遺漏，或為 null。

    如果時間序列識別碼屬性設定不正確，佈建環境時就可能會發生這個問題。 如需詳細資訊，請參閱[選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。 目前您無法將現有的時間序列深入解析環境更新為使用不同的時間序列識別碼。

## <a name="problem-some-data-shows-but-some-is-missing"></a>問題：顯示部分資料，但遺漏部分

您可能傳送了沒有時間序列識別碼的資料。

- 在酬載中傳送沒有時間序列識別碼欄位的事件時，就可能會發生這個問題。 如需詳細資訊，請參閱[支援的 JSON 圖形](./how-to-shape-query-json.md)。
- 發生這個問題的原因可能是因為您的環境正在進行節流。

    > [!NOTE]
    > 目前時間序列深入解析支援最高 6 Mbps 的擷取速率。

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>問題：資料已顯示，但現在已停止內嵌

- 您的事件來源金鑰可能已重新產生，而您的 Gen2 環境需要新的事件來源金鑰。

建立事件來源時所提供的金鑰不再有效時，就會發生此問題。 您會在中樞看到遙測，但在時間序列深入解析中找不到任何輸入訊息。 如果您不確定是否已重新產生金鑰，您可以在事件中樞的活動記錄檔中搜尋「建立或更新命名空間授權規則」，或搜尋「建立或更新 IotHub 資源」（適用于 IoT 中樞）。 

若要使用新的金鑰來更新時間序列深入解析 Gen2 環境，請在 Azure 入口網站中開啟您的中樞資源，然後複製新的金鑰。 流覽至 TSI 資源，然後按一下 [事件來源]。 

   [![更新金鑰。](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

選取已停止內嵌的事件來源，並貼上新的機碼，然後按一下 [儲存]。

   [![更新金鑰。](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>問題：我的事件來源的時間戳記屬性名稱無效

請確定名稱和值符合下列規則︰

* 時間戳記屬性名稱區分大小寫。
* 來自事件來源的時間戳記屬性值為 JSON 字串，其格式為 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` 。 此類型字串的其中一個範例為 `"2008-04-12T12:53Z"`。

若要確保您的時間戳記屬性名稱被捕捉並正常運作，最簡單的方式就是使用時間序列深入解析 Gen2 Explorer。 在時間序列深入解析 Gen2 Explorer 中，使用圖表來選取在您提供時間戳記屬性名稱之後的一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件]**** 選項。 第一個資料行標頭就是您的時間戳記屬性名稱。 它的字組 `Timestamp` 旁邊應該有 `($ts)`，而非：

* `(abc)`，這表示時間序列深入解析將資料值讀取為字串。
* 行事**曆**圖示，表示時間序列深入解析會將資料值讀取為 datetime。
* `#`，這表示時間序列深入解析將資料值讀取為整數。

如果未明確指定 Timestamp 屬性，則會使用事件的 IoT 中樞或事件中樞排入佇列時間做為預設時間戳記。

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>問題：我無法在 Explorer 中查看來自我的暖存放區的資料

- 您最近可能已布建您的暖存放區，且資料仍在流動中。
- 您可能已刪除您的暖存放區，在此情況下，您會遺失資料。

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>問題：我無法查看或編輯我的時間序列模型

- 您可能存取了時間序列深入解析 S1 或 S2 環境。

   只有在隨用隨付環境中才支援時間序列模型。 如需如何從時間序列深入解析 Gen2 Explorer 存取 S1 或 S2 環境的詳細資訊，請參閱在[Explorer 中將資料視覺化](./time-series-insights-update-explorer.md)。

   [![環境中沒有任何事件。](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 您可能沒有檢視及編輯模型的權限。

   使用者需要參與者層級存取權，才能編輯及檢視他們的時間序列模型。 若要確認目前的存取層級並授與其他存取權，請移至 Azure 入口網站中時間序列深入解析資源的 [**資料存取原則**] 區段。

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>問題： Gen2 Explorer 中的所有實例都缺少父系

如果您的環境沒有定義時間序列模型階層，可能會發生此問題。 如需詳細資訊，請參閱如何使用[時間序列模型](./time-series-insights-update-how-to-tsm.md)。

  [![無上層實例將會顯示警告。](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>問題： Power BI 連接器顯示「無法連接」

如果您未在 Power BI Desktop 中使用最新版的 Power BI 連接器，就可能會發生此問題。

[![無上層實例將會顯示警告。](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

* 請檢查您 Power BI Desktop 的版本，並確定您使用的是2020年7月版本。 如果不是，請更新您的 Power BI Desktop，然後再次執行連接器。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[時間序列模型](./time-series-insights-update-how-to-tsm.md)。

- 瞭解[支援的 JSON 圖形](./how-to-shape-query-json.md)。

- 請參閱 Azure 時間序列深入解析 Gen2 中的[規劃和限制](./time-series-insights-update-plan.md)。
