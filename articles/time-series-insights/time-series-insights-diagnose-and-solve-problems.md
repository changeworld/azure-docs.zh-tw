---
title: 診斷、疑難排解和解決問題-Azure 時間序列深入解析
description: 本文說明如何在 Azure 時間序列深入解析環境中診斷、疑難排解和解決常見的問題。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 4d9efa1ebf1a3e3b146c4f45b0e84047562141cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192709"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>在時間序列深入解析環境中診斷與解決問題

本文說明您在 Azure 時間序列深入解析環境中可能會遇到的問題。 文章中提供可能的原因和解決方案以便排除問題。

## <a name="video"></a>影片

### <a name="learn-about-common-time-series-insights-challenges-and-mitigationsbr"></a>瞭解常見的時間序列深入解析挑戰和緩和措施</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>問題：沒有顯示任何資料

如果[Azure 時間序列深入解析 explorer](https://insights.timeseries.azure.com)中未顯示任何資料，請考慮這些常見的原因。

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因：事件來源資料不是 JSON 格式

Azure 時間序列深入解析現在只支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：事件來源索引鍵遺漏必要的許可權

* 針對 Azure IoT 中樞中的 IoT 中樞，您必須提供具有服務連接許可權的金鑰。 選取 [ **iothubowner** ] 或 [**服務**] 原則。 兩者都具有服務連接許可權。

   [![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* 對於 Azure 事件中樞中的事件中樞，您必須提供具有「接聽」許可權的金鑰。 「**讀取**」和「**管理**」原則都可以使用，因為它們都有「接聽」許可權。

   [![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-time-series-insights"></a>原因 C：提供的取用者群組不是專屬的時間序列深入解析

當您註冊 IoT 中樞或事件中樞時，請務必設定您想要用來讀取資料的取用者群組。 此取用者群組「不可是共用的」**。 如果取用者群組是共用的，基礎 IoT 中樞或事件中樞會自動且隨機地將其中一個讀者中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D：環境剛剛已布建

資料會在環境及其資料首次建立後的幾分鐘內，于您的時間序列深入解析 explorer 中出現。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>問題：顯示部分資料，但遺失資料

當資料僅部分出現，而且資料似乎延遲時，請考慮這些可能的問題。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因：您的環境正在進行節流

當您建立具有資料的事件來源之後，就會布建環境時，[節流](time-series-insights-environment-mitigate-latency.md)是常見的問題。 Azure IoT 中樞和 Azure 事件中樞會將資料儲存長達七天。 時間序列深入解析一律從事件來源中最舊的事件（先進先出或*FIFO*）開始。

例如，當您連線到某個單一單位 S1 時間序列深入解析環境時，如果事件來源中有 5 百萬個事件，時間序列深入解析每天會讀取約 1 百萬個事件。 這樣看起來像時間序列深入解析有五天的延遲。 但發生的情況是環境正在進行節流。

如果您的事件來源中有舊的事件，您可以使用以下兩種方式之一進行節流：

- 變更您事件來源的保留期限制，以協助移除您不想要顯示在時間序列深入解析的舊事件。
- 佈建較大的環境大小 (單位數)，以增加舊事件的輸送量。 在上述範例中，如果您將相同的 S1 環境增加至一天五個單位，環境應該會在一天內趕上。 如果您的穩定狀態事件生產時間為1000000或每天較少的事件，您可以在時間序列深入解析趕上之後，將事件容量減少到一個單位。

強制執行的節流限制是以環境的 SKU 類型和容量為基礎。 環境中所有的事件來源皆共用此容量。 如果 IoT 中樞或事件中樞的事件來源推送超過強制限制的資料，您將會遇到節流和延遲。

下圖顯示有 S1 SKU 且容量為 3 的時間序列深入解析環境。 該環境可以每日輸入 3 百萬個事件。

[![環境容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

假設有一個從事件中樞內嵌訊息的環境。 它有大約67000個訊息的每日輸入速率。 此速率可轉譯為大約每分鐘 46 個訊息。

* 如果將每個事件中樞訊息壓平合併為單一時間序列深入解析事件，則不會發生節流。
* 如果每個事件中樞訊息被壓平合併為 100 個時間序列深入解析事件，則每分鐘應內嵌 4,600 個事件。

容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每三單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。

若要取得簡維邏輯運作方式的高階瞭解，請參閱[支援的 JSON 圖形](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>過度節流的建議解決方法

若要修正延遲情形，請增加您環境的 SKU 容量。 如需詳細資訊，請參閱[調整您的時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：歷程記錄資料的初始內嵌變慢輸入

如果您連線至現有的事件來源，則您的 IoT 中樞可能已經包含資料。 環境會從事件來源訊息保留期間開始時開始提取資料。 無法覆寫這個預設處理。 您可以進行節流。 節流可能需要一些時間才會趕上，因為它要內嵌歷史資料。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大型初始擷取的建議解決方法

修正延遲：

1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量之後，輸入程序會開始更快地趕上。 您需支付增加的容量。 您可以從[時間序列深入解析總管](https://insights.timeseries.azure.com)的可用性圖表中看出趕上的速度有多快。

2. 趕上延遲時間之後，即可將 SKU 容量降到正常的輸入速率。

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>問題：先前已顯示資料，但已不再顯示

如果時間序列深入解析不再內嵌資料，但事件仍會串流至 Iot 中樞或事件中樞，請考慮這種可能的原因。

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>原因：您的中樞存取金鑰已重新產生，而您的環境需要更新

當您建立事件來源時所提供的金鑰不再有效時，就會發生這個問題。 您會在中樞看到遙測，但在時間序列深入解析中找不到任何輸入訊息。 如果您不確定是否已重新產生金鑰，您可以在事件中樞的活動記錄檔中搜尋「建立或更新命名空間授權規則」。 針對 IoT 中樞，搜尋「建立或更新 IotHub 資源」。

若要使用新的金鑰更新您的時間序列深入解析環境，請在 Azure 入口網站中開啟您的中樞資源，然後複製新的金鑰。 移至您的時間序列深入解析資源，然後選取 [**事件來源**]：

   [![選取事件來源](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

選取已停止內嵌的事件來源或來源，並貼上新的機碼，然後選取 [**儲存**]：

   [![貼上新的金鑰](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題：事件來源的時間戳記屬性名稱設定沒有作用

確定來自事件來源的時間戳記屬性值為 JSON 字串，格式為_Yyyy-mm-dd ddTHH： MM： ss。FFFFFFFK_。 以下是範例： **2008-04-12T12： 53Z**。

請記住，時間戳記屬性名稱會區分大小寫。

若要確保系統已擷取時間戳記屬性名稱且正常運作，最簡單方式是使用時間序列深入解析總管。 在時間序列深入解析總管中，使用圖表，當您輸入時間戳記屬性名稱之後，選取一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [**探索事件**]。

第一個資料行標頭應該是您的時間戳記屬性名稱。 會在 [**時間戳記**]、 **（$ts）** 旁邊顯示。

將不會顯示下列值：

- *（abc）*：表示時間序列深入解析以字串的形式讀取資料值。
- 行事*曆圖示*：表示時間序列深入解析會將資料值讀取為日期時間值。
- *#*：表示時間序列深入解析會將資料值讀取為整數。

## <a name="next-steps"></a>後續步驟

- 瞭解[如何在 Azure 時間序列深入解析中降低延遲](time-series-insights-environment-mitigate-latency.md)。

- 瞭解[如何調整您的時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。
