---
title: 診斷、排除和解決問題 - Azure 時間序列見解 |微軟文檔
description: 本文介紹如何在 Azure 時間序列見解環境中診斷、排除和解決常見問題。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 209df97169c71d910677ffdb2e2b12593882445b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152569"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>在時間序列深入解析環境中診斷與解決問題

本文描述您在 Azure 時間序列深入解析環境中可能遇到的一些問題。 文章中提供可能的原因和解決方案以便排除問題。

## <a name="video"></a>影片

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>瞭解常見的時間序列洞察客戶挑戰和緩解措施。</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>問題：未顯示任何資料

有幾個常見原因可能造成 [Azure 時間序列深入解析總管](https://insights.timeseries.azure.com)中沒有資料：

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>原因 A：事件來源資料不是 JSON 格式

Azure 時間序列深入解析只支援 JSON 資料。 對於 JSON 示例，請閱讀[支援的 JSON 形狀](./how-to-shape-query-json.md)。

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>原因 B：事件源金鑰缺少必需的許可權

* 針對 Azure IoT 中樞的 IoT 中樞，您必須提供有**服務連線**權限的機碼。 選擇**iothub 擁有者****或服務**策略，因為兩者都具有**服務連接**許可權。

   [![IoT 中樞服務連線權限](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* 針對 Azure 事件中樞中的事件中樞，您必須提供有**接聽**權限的機碼。 **read** 或 **manage** 原則都可以，因為它們都有**接聽**權限。

   [![事件中樞接聽權限](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>原因 C：提供的消費者組並非時間序列見解獨佔

當您註冊 IoT 中樞或事件中樞時，請務必設定您想要用來讀取資料的取用者群組。 此取用者群組「不可是共用的」**。 如果取用者群組是共用的，基礎 IoT 中樞或事件中樞會自動且隨機地將其中一個讀者中斷連線。 提供唯一的取用者群組讓時間序列深入解析讀取。

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>原因 D：環境剛剛預配

資料將在首次創建環境及其資料後幾分鐘內顯示在時間序列見解資源管理器中。

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>問題：顯示某些資料，但缺少資料

當資料僅部分出現，且資料似乎有延遲的情況，您應該考慮幾個可能性。

### <a name="cause-a-your-environment-is-being-throttled"></a>原因 A：您的環境正在被限制

在創建具有資料的事件源後預配環境時，[限制](time-series-insights-environment-mitigate-latency.md)是一個常見問題。 Azure IoT 中樞和 Azure 事件中樞會將資料儲存長達七天。 時間序列深入解析一律會從事件來源中最舊的事件 (「先進先出」或 *FIFO*) 開始讀取。

例如，當您連線到某個單一單位 S1 時間序列深入解析環境時，如果事件來源中有 5 百萬個事件，時間序列深入解析每天會讀取約 1 百萬個事件。 這樣看起來像時間序列深入解析有五天的延遲。 不過，實際上是環境正在進行節流。

如果您的事件來源中有舊的事件，您可以使用以下兩種方式之一進行節流：

- 變更您事件來源的保留期限制，以協助移除您不想要顯示在時間序列深入解析的舊事件。
- 佈建較大的環境大小 (單位數)，以增加舊事件的輸送量。 使用上述範例，如果您將相同的 S1 環境增加到一天五個單位，該環境應該會在一天內趕上。 如果您的穩定狀態事件生產是每天 1 百萬個事件或更少，則您可以在趕上之後，將事件的容量減少到一個單位。

節流限制會根據環境 SKU 類型和容量來強制執行。 環境中所有的事件來源皆共用此容量。 如果 IoT 中心或事件中心的事件源將資料推送到強制限制之外，您將遇到限制和延遲。

下圖顯示有 S1 SKU 且容量為 3 的時間序列深入解析環境。 該環境可以每日輸入 3 百萬個事件。

[![環境 SKU 目前容量](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

例如，假設環境從事件中心引入消息。 每日輸入速率為 ~67,000 個訊息。 此速率可轉譯為大約每分鐘 46 個訊息。 

* 如果將每個事件中樞訊息壓平合併為單一時間序列深入解析事件，則不會發生節流。 
* 如果每個事件中樞訊息被壓平合併為 100 個時間序列深入解析事件，則每分鐘應內嵌 4,600 個事件。 

容量為 3 的 S1 SKU 環境每分鐘只能輸入 2,100 個事件 (每天 1 百萬個事件 = 每三單位每分鐘 700 個事件 = 每分鐘 2,100 個事件)。 

為了高度瞭解拼合邏輯的工作原理，請閱讀[支援的 JSON 形狀](./how-to-shape-query-json.md)。

#### <a name="recommended-resolutions-for-excessive-throttling"></a>過度節流的建議解決方法

若要修正延遲情形，請增加您環境的 SKU 容量。 有關詳細資訊，請閱讀[縮放時間序列見解環境](time-series-insights-how-to-scale-your-environment.md)。

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>原因 B：歷史資料的初始引入速度減慢

如果您連線至現有的事件來源，則您的 IoT 中樞可能已經包含資料。 環境會從事件來源訊息保留期間開始時開始提取資料。 無法覆蓋此預設處理。 您可以進行節流。 節流可能需要一些時間才會趕上，因為它要內嵌歷史資料。

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>大型初始擷取的建議解決方法

修正延遲：

1. 將 SKU 容量增加到最大允許值 (在此案例中是 10)。 增加容量之後，輸入程序會開始更快地趕上。 增加容量需支付費用。 您可以從[時間序列深入解析總管](https://insights.timeseries.azure.com)的可用性圖表中看出趕上的速度有多快。

2. 趕上延遲時間之後，即可將 SKU 容量降到正常的輸入速率。

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>問題：以前顯示的資料，但不再顯示

TSI 不再引入資料，但事件仍在資料流到 Iot 中心或事件中心

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-updating"></a>原因 A：您的中心訪問金鑰已重新生成，您的環境需要更新

當創建事件源時提供的金鑰不再有效時，將發生此問題。 您將在中心看到遙測資料，但在時間序列見解中看不到入口接收的消息。 如果您不確定金鑰是否已重新生成，則可以搜索事件中心的活動日誌以查找"創建或更新命名空間授權規則"，或搜索 IoT 中心"創建或更新 IotHub 資源"。

使用新鍵在 Azure 門戶中打開中心資源並複製新金鑰，請更新時間序列見解環境。 導航到 TSI 資源，然後按一下事件源。 

   [![更新金鑰。](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

選擇已停止引入的事件源，粘貼到新鍵中，然後按一下"保存"。

   [![更新金鑰。](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>問題：事件源的時間戳記屬性名稱設置不起作用

請確保時間戳記屬性名稱和值符合下列規則：

* 時間戳記屬性名稱會區分大小寫。
* 來自事件來源的時間戳記屬性值 (JSON 字串) 格式應是 _yyyy-MM-ddTHH:mm:ss.FFFFFFFK_。 例如，**2008-04-12T12:53Z**。

若要確保系統已擷取時間戳記屬性名稱且正常運作，最簡單方式是使用時間序列深入解析總管。 在時間序列深入解析總管中，使用圖表，當您輸入時間戳記屬性名稱之後，選取一段時間。 以滑鼠右鍵按一下選取範圍，然後選取 [探索事件]**** 選項。

第一個資料行標頭應該是您的時間戳記屬性名稱。 "**時間戳記****"（$ts）** 旁邊將顯示。

將不會顯示以下值：

- *（abc）*：指示時序見解正在將資料值讀取為字串。
- *日曆圖示*：指示時間序列見解正在將資料值讀為*日期時間*。
- *#*：指示時序見解正在將資料值讀為整數。

## <a name="next-steps"></a>後續步驟

- 閱讀有關如何[減輕 Azure 時間序列見解中的延遲](time-series-insights-environment-mitigate-latency.md)。

- [瞭解如何擴展時間序列見解環境](time-series-insights-how-to-scale-your-environment.md)。