---
title: 針對 Gen2 環境進行診斷和疑難排解-Azure 時間序列深入解析 |Microsoft Docs
description: 瞭解如何對 Azure 時間序列深入解析 Gen2 環境進行診斷和疑難排解。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: b994e8ce34319da4827d389b49e23ed6e5bcde95
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653752"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>針對 Azure 時間序列深入解析 Gen2 環境進行診斷和疑難排解

本文將摘要說明當您使用 Azure 時間序列深入解析 Gen2 環境時，可能會遇到的幾個常見問題。 文章中也會描述每個問題的可能發生原因和解決方案。

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>問題：我在 Gen2 Explorer 中找不到我的環境

如果您沒有存取時間序列深入解析環境的許可權，可能會發生此問題。 使用者需要讀取器層級存取角色，才能檢視他們的時間序列深入解析環境。 若要確認目前的存取層級並授與其他存取權，請移至[Azure 入口網站](https://portal.azure.com/)中時間序列深入解析資源的 [**資料存取原則**] 區段。

  [![驗證資料存取原則。](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>問題： Gen2 Explorer 中未顯示任何資料

有幾個常見的原因，那就是您的資料可能不會出現在 [Azure 時間序列深入解析 Gen2 Explorer](https://insights.timeseries.azure.com/preview)中。

- 事件來源可能沒有接收資料。

    請確認事件來源 (事件中樞或 IoT 中樞)，是否正從您的標籤或執行個體接收資料。 請在 Azure 入口網站中，瀏覽至您資源的概觀頁面以進行確認。

    [![查看儀表板計量總覽。](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- 事件來源資料不是 JSON 格式。

    時間序列深入解析僅支援 JSON 資料。 如需 JSON 範例，請參閱 [支援的 json 圖形](./how-to-shape-query-json.md)。

- 事件來源索引鍵遺漏必要的權限。

  - 針對 IoT 中樞，您必須提供具有「服務連接」**** 權限的索引鍵。

    [![驗證 IoT 中樞許可權。](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - 原則 **iothubowner** 和 **服務** 都可以運作，因為它們具有 **服務連接** 許可權。

  - 針對事件中樞，您必須提供具有「接聽」**** 權限的索引鍵。
  
    [![檢查事件中樞許可權。](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - 「 **讀取** 」和「 **管理** 」原則都可運作，因為它們具有「 **接聽** 」許可權。

- 您提供的取用者群組不是時間序列深入解析專用的。

    在註冊 IoT 中樞或事件中樞期間指定的取用者群組是要用於讀取資料的。 此取用者群組在每個環境中必須是唯一的。 如果共用取用者群組，基礎事件中樞會自動且隨機地與其中一個讀取器中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

- 您在佈建期間指定的時間序列識別碼屬性不正確、遺漏，或為 null。

    如果時間序列識別碼屬性設定不正確，佈建環境時就可能會發生這個問題。 如需詳細資訊，請參閱 [選擇時間序列識別碼的最佳做法](./time-series-insights-update-how-to-id.md)。 目前您無法將現有的時間序列深入解析環境更新為使用不同的時間序列識別碼。

## <a name="problem-some-data-shows-but-some-is-missing"></a>問題：部分資料顯示，但有些資料遺失

您可能傳送了沒有時間序列識別碼的資料。

- 在酬載中傳送沒有時間序列識別碼欄位的事件時，就可能會發生這個問題。 如需詳細資訊，請參閱 [支援的 JSON 圖形](./how-to-shape-query-json.md)。
- 發生這個問題的原因可能是因為您的環境正在進行節流。

    > [!NOTE]
    > 目前時間序列深入解析支援最高 6 Mbps 的擷取速率。

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>問題：顯示資料，但現在內嵌已停止

- 您的事件來源金鑰可能已重新產生，而您的 Gen2 環境需要新的事件來源金鑰。

當您建立事件來源時所提供的金鑰已不再有效時，就會發生此問題。 您會看到中樞內的遙測，但在時間序列深入解析中沒有輸入接收的訊息。 如果您不確定是否已重新產生金鑰，您可以在事件中樞的活動記錄檔中搜尋「建立或更新命名空間授權規則」，或搜尋「建立或更新 IotHub 資源」作為 IoT 中樞。

若要使用新的金鑰來更新您的時間序列深入解析 Gen2 環境，請在 Azure 入口網站中開啟中樞資源，並複製新的金鑰。 流覽至 TSI 資源，然後按一下 [事件來源]。

   [![螢幕擷取畫面顯示已呼叫 [事件來源] 功能表項目的 [T I 資源]。](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

選取已停止內嵌的事件來源 () ，貼上新的機碼，然後按一下 [儲存]。

   [![螢幕擷取畫面顯示我輸入的 T S 資源與 I o T 中樞原則金鑰。](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>問題：我的事件來源的時間戳記屬性名稱無效

請確定名稱和值符合下列規則︰

- 時間戳記屬性名稱區分大小寫。
- 以 JSON 字串形式來自事件來源的時間戳記屬性值具有格式 `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` 。 此類型字串的其中一個範例為 `"2008-04-12T12:53Z"`。

若要確保您的時間戳記屬性名稱已被捕捉並正常運作，最簡單的方式就是使用時間序列深入解析 Gen2 Explorer。 在時間序列深入解析 Gen2 Explorer 內，使用圖表來選取您提供時間戳記屬性名稱之後的一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件]**** 選項。 第一個資料行標頭就是您的時間戳記屬性名稱。 它的字組 `Timestamp` 旁邊應該有 `($ts)`，而非：

- `(abc)`，這表示時間序列深入解析將資料值讀取為字串。
- 行事 **曆** 圖示，表示時間序列深入解析將資料值讀取為 datetime。
- `#`，這表示時間序列深入解析將資料值讀取為整數。

如果未明確指定時間戳記屬性，就會使用事件的 IoT 中樞或事件中樞排入佇列時間作為預設時間戳記。

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>問題：我無法在 Explorer 中從我的暖存放區查看資料

- 您最近可能已布建暖存放區，但資料仍在流入中。
- 您可能已刪除您的暖存放區，在此情況下，您會遺失資料。

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>問題：我無法查看或編輯我的時間序列模型

- 您可能存取了時間序列深入解析 S1 或 S2 環境。

   只有在隨用隨付環境中支援時間序列模型。 如需有關如何從時間序列深入解析 Gen2 Explorer 存取 S1 或 S2 環境的詳細資訊，請參閱 [Explorer 中的視覺化資料](./time-series-insights-update-explorer.md)。

   [![環境中沒有任何事件。](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- 您可能沒有檢視及編輯模型的權限。

   使用者需要參與者層級存取權，才能編輯及檢視他們的時間序列模型。 若要確認目前的存取層級並授與其他存取權，請移至 Azure 入口網站中的時間序列深入解析資源的 [ **資料存取原則** ] 區段。

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>問題： Gen2 Explorer 中的所有實例都缺少父系

如果您的環境未定義時間序列模型階層，則可能會發生此問題。 如需詳細資訊，請參閱如何使用 [時間序列模型](/azure/time-series-insights/time-series-insights-overview)。

  [![無上層實例將會顯示警告。](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="problem-power-bi-connector-shows-unable-to-connect"></a>問題： Power BI 連接器顯示「無法連接」

如果您未在 Power BI Desktop 中使用最新版的 Power BI 連接器，就可能會發生此問題。

[![螢幕擷取畫面顯示 [無法連接] 對話方塊。](media/preview-troubleshoot/power-bi-unable-to-connect.png)](media/preview-troubleshoot/power-bi-unable-to-connect.png#lightbox)

- 檢查您 Power BI Desktop 的版本，並確定您使用的是2020年7月版本。 如果沒有，請更新您的 Power BI Desktop，然後再次執行連接器。

## <a name="next-steps"></a>後續步驟

- 閱讀如何使用 [時間序列模型](/azure/time-series-insights/time-series-insights-overview)的相關資訊。

- 深入瞭解 [支援的 JSON 圖形](./how-to-shape-query-json.md)。

- 請參閱 Azure 時間序列深入解析 Gen2 中的 [規劃與限制](./time-series-insights-update-plan.md) 。
