---
title: 診斷、疑難排解和解決問題-Azure 時間序列深入解析
description: 本文說明如何在 Azure 時間序列深入解析環境中診斷、疑難排解及解決常見的問題。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 231f2e4df1445c60378ac06aab0d0e56f410c1c8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530131"
---
# <a name="diagnose-and-solve-issues-in-your-azure-time-series-insights-gen1-environment"></a>診斷和解決 Azure 時間序列深入解析 Gen1 環境中的問題

> [!CAUTION]
> 這是 Gen1 文章。

本文說明您可能會在 Azure 時間序列深入解析環境中遇到的問題。 文章中提供可能的原因和解決方案以便排除問題。

## <a name="video"></a>影片

### <a name="learn-about-common-azure-time-series-insights-challenges-and-mitigationsbr"></a>瞭解常見的 Azure 時間序列深入解析挑戰和緩和措施</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>問題：未顯示任何資料

如果 [Azure 時間序列深入解析 explorer](https://insights.timeseries.azure.com)中沒有任何資料出現，請考慮這些常見的原因。

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因：事件來源資料不是 JSON 格式

Azure 時間序列深入解析現在只支援 JSON 資料。 如需 JSON 範例，請參閱[支援的 JSON 樣貌](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：事件來源索引鍵遺漏必要的許可權

* 針對 Azure IoT 中樞中的 IoT 中樞，您必須提供具有服務連接許可權的金鑰。 請選取 **iothubowner** 或 **服務** 原則。 兩者都有服務連接許可權。

   [![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* 針對 Azure 事件中樞中的事件中樞，您必須提供具有「接聽」許可權的金鑰。 「 **讀取** 」和「 **管理** 」原則都有作用，因為兩者都具有「接聽」許可權。

   [![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-azure-time-series-insights"></a>原因 C：提供的取用者群組不是獨佔的 Azure 時間序列深入解析

當您註冊 IoT 中樞或事件中樞時，請務必設定您想要用來讀取資料的取用者群組。 此取用者群組「不可是共用的」**。 如果取用者群組是共用的，基礎 IoT 中樞或事件中樞會自動且隨機地將其中一個讀者中斷連線。 提供唯一的取用者群組，讓 Azure 時間序列深入解析從中讀取。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D：環境剛布建

資料會在環境和其資料首次建立後的幾分鐘內，出現在您的 Azure 時間序列深入解析 explorer 中。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>問題：顯示部分資料，但遺漏資料

當資料只出現部分且資料看似延遲時，請考慮這些可能的問題。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A：您的環境正在進行節流

當您建立具有資料的事件來源之後布建環境時，[節流](time-series-insights-environment-mitigate-latency.md)是常見的問題。 Azure IoT 中樞和 Azure 事件中樞會將資料儲存長達七天。 Azure 時間序列深入解析一律會從事件來源中最舊的事件開始， (先進先出或 *FIFO*) 。

例如，當您連接到 S1、單一單位 Azure 時間序列深入解析環境時，如果事件來源中有5000000的事件，Azure 時間序列深入解析每日讀取大約1000000個事件。 它可能看起來像是 Azure 時間序列深入解析發生五天的延遲。 但發生的情況是環境正在進行節流。

如果您的事件來源中有舊的事件，您可以使用以下兩種方式之一進行節流：

* 變更您事件來源的保留期限制，以協助移除您不想在 Azure 時間序列深入解析中顯示的舊事件。
* 佈建較大的環境大小 (單位數)，以增加舊事件的輸送量。 在上述範例中，如果您將相同的 S1 環境增加為一天的五個單位，環境應該會在一天內趕上。 如果您的穩定狀態事件生產是每天1000000或更少的事件，您可以在 Azure 時間序列深入解析趕上之後，將事件容量減少為一個單位。

強制執行的節流限制是以環境的 SKU 類型和容量為基礎。 環境中所有的事件來源皆共用此容量。 如果 IoT 中樞或事件中樞的事件來源推送超過強制限制的資料，您將會遇到節流和延遲。

下圖顯示的 Azure 時間序列深入解析環境具有 S1 的 SKU 和容量3。 該環境可以每日輸入 3 百萬個事件。

[![環境容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

想像一個從事件中樞內嵌訊息的環境。 它的每日輸入速率大約是67000的訊息。 此速率可轉譯為大約每分鐘 46 個訊息。

* 如果將每個事件中樞訊息壓平合併為單一 Azure 時間序列深入解析事件，則不會進行節流。
* 如果每個事件中樞訊息都會壓平合併為 100 Azure 時間序列深入解析事件，則每分鐘應內嵌4600事件。

容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每三單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。

若要深入瞭解簡維邏輯的運作方式，請參閱 [支援的 JSON 圖形](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>過度節流的建議解決方法

若要修正延遲情形，請增加您環境的 SKU 容量。 如需詳細資訊，請參閱 [調整您的 Azure 時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：歷程記錄資料的初始內嵌變慢輸入

如果您連線至現有的事件來源，則您的 IoT 中樞可能已經包含資料。 環境會從事件來源訊息保留期間開始時開始提取資料。 無法覆寫此預設處理。 您可以進行節流。 節流可能需要一些時間才會趕上，因為它要內嵌歷史資料。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大型初始擷取的建議解決方法

修正延遲：

1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量之後，輸入程序會開始更快地趕上。 您需支付增加的容量費用。 若要以視覺化方式來視覺化，您可以在 [Azure 時間序列深入解析 explorer](https://insights.timeseries.azure.com)中查看可用性圖表。

2. 趕上延遲時間之後，即可將 SKU 容量降到正常的輸入速率。

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>問題：先前顯示的資料，但已不再顯示

如果 Azure 時間序列深入解析不再擷取資料，但事件仍串流至 Iot 中樞或事件中樞，請考慮此可能原因。

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>原因 A：已重新產生您的中樞存取金鑰，而且您的環境需要更新

當您建立事件來源時所提供的金鑰已不再有效時，就會發生此問題。 您會看到中樞內的遙測，但 Azure 時間序列深入解析中沒有輸入接收的訊息。 如果您不確定是否已重新產生金鑰，您可以在事件中樞的活動記錄檔中搜尋「建立或更新命名空間授權規則」。 若為 IoT 中樞，請搜尋「建立或更新 IotHub 資源」。

若要使用新的金鑰來更新 Azure 時間序列深入解析環境，請在 Azure 入口網站中開啟中樞資源，並複製新的金鑰。 移至您的 Azure 時間序列深入解析資源，然後選取 [ **事件來源**]：

   [![選取事件來源](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

選取內嵌已停止的事件來源或來源，貼上新的機碼，然後選取 [ **儲存**]：

   [![貼上新的金鑰](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題：事件來源的時間戳記屬性名稱設定無效

確定以 JSON 字串形式來自事件來源的時間戳記屬性值的格式為 _yyyy-mm-dd-ddTHH： mm： ss。FFFFFFFK_。 範例如下： **2008-04-12T12： 53Z**。

請記住，時間戳記屬性名稱會區分大小寫。

若要確保您的時間戳記屬性名稱已被捕捉並正常運作，最簡單的方式就是使用 Azure 時間序列深入解析 explorer。 在 Azure 時間序列深入解析 explorer 中，使用圖表，選取在您輸入時間戳記屬性名稱之後的一段時間。 以滑鼠右鍵按一下選取專案，然後選取 [ **探索事件**]。

第一個資料行標頭應該是您的時間戳記屬性名稱。 在 [ **時間戳記**] 旁邊，會顯示 ** ($ts) ** 。

將不會顯示下列值：

* * (abc) *：表示 Azure 時間序列深入解析正在將資料值讀取為字串。
* 行事*曆圖示*：指出 Azure 時間序列深入解析正在將資料值讀取為日期時間值。
* *#*：指出 Azure 時間序列深入解析正在將資料值讀取為整數。

## <a name="next-steps"></a>後續步驟

* 瞭解 [如何緩和 Azure 時間序列深入解析中的延遲](time-series-insights-environment-mitigate-latency.md)。

* 瞭解 [如何調整您的 Azure 時間序列深入解析環境](time-series-insights-how-to-scale-your-environment.md)。
