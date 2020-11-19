---
title: Azure 監視器中的 Azure 服務匯流排計量 | Microsoft Docs
description: 本文說明如何使用 Azure 監視器來監視服務匯流排實體 (佇列、主題和訂閱)。
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: 1f8bd9484bf2a2106818da1d6e4ef21e937d2ac3
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916877"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure 監視器中的 Azure 服務匯流排計量

服務匯流排計量會提供您 Azure 訂用帳戶中的資源狀態。 您可以使用豐富的計量資料集，評估服務匯流排資源的整體健康狀態，不只在命名空間層級，還有實體層級。 這些統計資料相當重要，因為它們可協助您監視服務匯流排的狀態。 計量也可協助針對問題的根本原因進行疑難排解，而不需要連絡 Azure 支援。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱 [Microsoft Azure 中的監視](../azure-monitor/overview.md)和 GitHub 上的 [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (使用 .NET 擷取 Azure 監視計量) 範例。

> [!IMPORTANT]
> 在未與實體互動的時間達 2 小時之後，計量將會開始顯示 "0" 作為值，直到該實體不再閒置為止。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以透過 [Azure 入口網站](https://portal.azure.com)來存取計量，或使用 Azure 監視器 API (REST 和 .NET) 和分析解決方案 (例如 Azure 監視器記錄和事件中樞) 來存取計量。 如需詳細資訊，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。

計量是預設啟用的功能，您可以存取最近 30 天的資料。 如果您需要保留較長一段時間的資料，您可以將計量資料封存至 Azure 儲存體帳戶。 在「Azure 監視器」的[診斷設定](../azure-monitor/platform/diagnostic-settings.md)中即可設定此值。

## <a name="access-metrics-in-the-portal"></a>在入口網站中存取計量

您可以在 [Azure 入口網站](https://portal.azure.com)中監視一段時間的計量。 下列範例示範如何檢視帳戶層級的成功要求及連入要求：

![Azure 入口網站中 [監視-計量 (預覽]) 頁面的螢幕擷取畫面。][1]

您也可以直接透過命名空間來存取計量。 若要這樣做，請選取您的命名空間，然後選取 [ **計量**]。 若要顯示篩選至實體範圍的計量，請選取實體，然後選取 [ **計量**]。

![監視度量 (預覽) 頁面的螢幕擷取畫面，已篩選至實體的範圍。][2]

如需支援維度的計量，您必須使用所需的維度值來進行篩選。

## <a name="billing"></a>計費

Azure 監視器上的計量和警示會按每項警示計費。 當警示已設定且儲存之前，您應該會在入口網站上提供這些費用。 

其他內嵌計量資料的解決方案會直接透過那些解決方案計費。 例如，如果您將計量資料封存至 Azure 儲存體帳戶，您的計費方式 Azure 儲存體。 如果您將計量資料串流至 Log Analytics 以進行高階分析，也會向您收取 Log Analytics 的費用。

下列計量會提供您服務健康狀態的概觀。 

> [!NOTE]
> 我們正在取代許多計量，因為它們移至不同的名稱下。 這可能需要您更新參考。 此後不再支援標記「已取代」關鍵字的計量。

所有計量值都會每分鐘傳送至「Azure 監視器」。 時間細微性會定義呈現計量值的時間間隔。 所有的服務匯流排計量都支援 1 分鐘的時間間隔。

## <a name="request-metrics"></a>要求計量

計算資料和管理作業要求的數目。

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
| 傳入的要求| 在指定時段內，向服務匯流排服務提出的要求數目。 <br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|成功的要求|在指定時段內，向服務匯流排服務提出的成功要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|伺服器錯誤|因為服務匯流排服務在指定期間內發生錯誤而未處理的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|使用者錯誤 (請參閱下一小節)|在指定的期間內，因使用者錯誤而未處理的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|節流的要求|因為超過使用量而節流的要求數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|

### <a name="user-errors"></a>使用者錯誤

下列兩種錯誤類型會分類為使用者錯誤：

1. 用戶端錯誤 (在 HTTP 中是 400 錯誤)。
2. 處理訊息時發生的錯誤，例如 [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)。


## <a name="message-metrics"></a>訊息計量

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|傳入訊息|在指定時段內，傳送至服務匯流排的事件或訊息數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|外送訊息|在指定時段內，從服務匯流排接收的事件或訊息數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
| 訊息| 佇列/主題中的訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 維度：機構名稱 |
| 活動訊息| 佇列/主題中的作用中訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 維度：機構名稱 |
| 無效訊息| 佇列/主題中的無效訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/>維度：機構名稱 |
| 排定的訊息| 佇列/主題中排程的訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average  <br/> 維度：機構名稱 |
| 已完成的訊息| 佇列/主題中已完成的訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 維度：機構名稱 |
| 已放棄的訊息| 佇列/主題中已放棄的訊息計數。 <br/><br/> 單位：Count <br/> 彙總類型：Average <br/> 維度：機構名稱 |
| 大小 | 實體 (佇列或主題) 的大小（以位元組為單位）。 <br/><br/>單位：Count <br/>彙總類型：Average <br/>維度：機構名稱 | 

> [!NOTE]
> 訊息、使用中、無效信件、已排程、已完成和已放棄訊息的值都是時間點值。 在該時間點之後立即取用的傳入訊息可能不會反映在這些計量中。 

## <a name="connection-metrics"></a>連接計量

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|作用中的連線|命名空間中的作用中連接數目，以及命名空間中的實體。 此度量的值是時間點值。 在該時間點之後處於作用中狀態的連線，可能不會反映在度量中。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|已開啟的連線 |已開啟的連線數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|
|已關閉的連線 |已關閉的連線數目。<br/><br/> 單位：Count <br/> 彙總類型：總計 <br/> 維度：機構名稱|

## <a name="resource-usage-metrics"></a>資源使用量計量

> [!NOTE] 
> 下列計量僅適用於 **進階** 層。 
> 
> 針對進階層命名空間的任何中斷情況，所要監視的重要計量如下：**每個命名空間的 CPU 使用量** 及 **每個命名空間的記憶體大小**。 使用 Azure 監視器對這些計量[設定警示](../azure-monitor/platform/alerts-metric.md)。
> 
> 另一個您可以監視的計量是：**節流的要求**。 不過，只要命名空間維持在其記憶體、CPU 和代理連線的限制內，就不會造成問題。 如需詳細資訊，請參閱 [Azure 服務匯流排進階層中的節流](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|每個命名空間的 CPU 使用量|命名空間的 CPU 使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大值 <br/> 維度：機構名稱|
|每個命名空間的記憶體大小使用量|命名空間的記憶體使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大值 <br/> 維度：機構名稱|

## <a name="metrics-dimensions"></a>計量維度

Azure 服務匯流排支援下列的 Azure 監視器計量維度。 將維度新增至計量為選擇性。 如果您未加入維度，則會在命名空間層級指定計量。 

|維度名稱|描述|
| ------------------- | ----------------- |
|實體名稱| 服務匯流排支援命名空間下的訊息實體。|

## <a name="set-up-alerts-on-metrics"></a>設定計量警示

1. 在 [服務匯流排命名空間] 頁面的 [計量] 索引標籤上，選取 [設定警示]。 

    ![[計量] 頁面 - [設定警示] 功能表](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. 選取 [選取目標] 選項，然後在 [選取資源] 頁面上執行下列動作： 
    1. 針對 [依資源類型篩選] 欄位，選取 [服務匯流排命名空間]。 
    2. 針對 [依訂用帳戶篩選]  欄位，選取您的訂用帳戶。
    3. 從清單中選取 [服務匯流排命名空間]。 
    4. 選取 [完成] 。 
    
        ![選取命名空間](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. 選取 [新增準則]，然後在 [設定訊號邏輯] 頁面上執行下列動作：
    1. 針對 [訊號類型]，選取 [計量]。 
    2. 選取一個訊號。 例如：**服務錯誤**。 

        ![選取伺服器錯誤](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. 針對 [條件]，選取 [大於]。
    2. 針對 [時間彙總]，選取 [總計]。 
    3. 針對 [閾值]，輸入 **5**。 
    4. 選取 [完成] 。    

        ![指定條件](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. 在 [建立規則] 頁面上，展開 [定義警示詳細資料]，然後執行下列動作：
    1. 輸入警示的 **名稱**。 
    2. 輸入警示的 **描述**。
    3. 選取警示規則的 **嚴重性**。 

        ![[建立規則] 頁面的螢幕擷取畫面。 [定義警示詳細資料] 會展開，並醒目提示 [警示規則名稱]、[描述] 和 [嚴重性] 的欄位。](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. 在 [建立規則] 頁面上，展開 [定義動作群組]，選取 [新增動作群組]，然後在 [新增動作群組] 頁面上執行下列動作。 
    1. 輸入動作群組的名稱。
    2. 輸入動作群組的簡短名稱。 
    3. 選取您的訂用帳戶。 
    4. 選取資源群組。 
    5. 針對此逐步解說，輸入 **Send email** 作為 [動作名稱]。
    6. 針對 [動作類型]，選取 [電子郵件/簡訊/推播/語音]。 
    7. 選取 [編輯詳細資料]。 
    8. 在 [電子郵件/簡訊/推播/語音] 頁面上，執行下列動作：
        1. 選取 [電子郵件]。 
        2. 輸入 **電子郵件地址**。 
        3. 選取 [確定]。

            ![[新增動作群組] 頁面的螢幕擷取畫面。 系統會將名為「傳送電子郵件」的動作新增至群組，其中包含 [電子郵件]/[SMS/推送/語音] 動作類型。](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. 在 [新增動作群組] 頁面上，選取 [確定]。 
1. 在 [建立規則] 頁面上，選取 [建立警示規則]。 

    ![[建立警示規則] 按鈕](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>後續步驟

請參閱 [Azure 監視器概觀](../azure-monitor/overview.md)。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png