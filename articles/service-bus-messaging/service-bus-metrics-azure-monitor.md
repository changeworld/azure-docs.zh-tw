---
title: Azure 監視器中的 Azure 服務匯流排指標*微軟文檔
description: 本文介紹如何使用 Azure 監視器監視服務匯流排實體（佇列、主題和訂閱）。
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240795"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure 監視器中的 Azure 服務匯流排指標

服務匯流排計量會提供您 Azure 訂用帳戶中的資源狀態。 您可以使用豐富的計量資料集，評估服務匯流排資源的整體健康狀態，不只在命名空間層級，還有實體層級。 這些統計資料相當重要，因為它們可協助您監視服務匯流排的狀態。 計量也可協助針對問題的根本原因進行疑難排解，而不需要連絡 Azure 支援。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱 [Microsoft Azure 中的監視](../monitoring-and-diagnostics/monitoring-overview.md)和 GitHub 上的 [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (使用 .NET 擷取 Azure 監視計量) 範例。

> [!IMPORTANT]
> 在未與實體互動的時間達 2 小時之後，計量將會開始顯示 "0" 作為值，直到該實體不再閒置為止。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以通過[Azure 門戶](https://portal.azure.com)訪問指標，也可以使用 Azure 監視器 API（REST 和 .NET）和分析解決方案（如 Azure 監視器日誌和事件中心）。 有關詳細資訊，請參閱[Azure 監視器 中的指標](../azure-monitor/platform/data-platform-metrics.md)。

計量是預設啟用的功能，您可以存取最近 30 天的資料。 如果您需要延長這些資料的保留時間，您可以將計量資料封存到 Azure 儲存體帳戶。 在「Azure 監視器」的[診斷設定](../azure-monitor/platform/diagnostic-settings.md)中即可設定此值。

## <a name="access-metrics-in-the-portal"></a>在入口網站中存取計量

您可以在 [Azure 入口網站](https://portal.azure.com)中監視一段時間的計量。 下列範例示範如何檢視帳戶層級的成功要求及連入要求：

![][1]

您也可以直接透過命名空間來存取計量。 為此，請選擇命名空間，然後按一下 **"指標**"。 要顯示篩選到實體範圍的指標，請選擇實體，然後按一下 **"指標**"。

![][2]

如需支援維度的計量，您必須使用所需的維度值來進行篩選。

## <a name="billing"></a>計費

Azure 監視器上的指標和警報按警報收費。 設置警報時和保存警報之前，應在門戶上提供這些費用。 

引入指標資料的其他解決方案由這些解決方案直接計費。 例如，如果您將計量資料封存到 Azure 儲存體帳戶，就要支付 Azure 儲存體的使用費。 如果您將計量資料串流到 Log Analytics 進行進階分析，則也需支付 Log Analytics 的費用。

下列計量會提供您服務健康狀態的概觀。 

> [!NOTE]
> 我們正在取代許多計量，因為它們移至不同的名稱下。 這可能需要您更新參考。 此後不再支援標記「已取代」關鍵字的計量。

所有計量值都會每分鐘傳送至「Azure 監視器」。 時間細微性會定義呈現計量值的時間間隔。 所有的服務匯流排計量都支援 1 分鐘的時間間隔。

## <a name="request-metrics"></a>要求計量

計算資料和管理作業要求的數目。

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
| 傳入的要求| 在指定時段內，向服務匯流排服務提出的要求數目。 <br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|成功的要求|在指定時段內，向服務匯流排服務提出的成功要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|伺服器錯誤|在指定時段內，服務匯流排服務中因錯誤而未處理的要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|使用者錯誤（請參閱以下子節）|在指定的期間內，因使用者錯誤而未處理的要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|限制請求|因為超過使用量而節流的要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|

### <a name="user-errors"></a>使用者錯誤

下列兩種錯誤類型會分類為使用者錯誤：

1. 用戶端錯誤 (在 HTTP 中是 400 錯誤)。
2. 處理訊息時發生的錯誤，例如 [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception)。


## <a name="message-metrics"></a>訊息計量

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|傳入訊息|在指定時段內，傳送至服務匯流排的事件或訊息數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|外送訊息|在指定時段內，從服務匯流排接收的事件或訊息數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
| 訊息| 佇列/主題中的訊息計數。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 維度：EntityName |
| ActiveMessages| 佇列/主題中的作用中訊息計數。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/> 維度：EntityName |
| 死信| 佇列/主題中死字母消息的計數。 <br/><br/> 單位：計數 <br/> 彙總類型：平均 <br/>維度：EntityName |
| 排定的訊息| 佇列/主題中計畫的消息計數。 <br/><br/> 單位：計數 <br/> 彙總類型：平均  <br/> 維度：EntityName |

> [!NOTE]
> 以下指標的值是時間點值。 在時間點之後立即使用的傳入消息可能不會反映在這些指標中。 
> - 訊息
> - 活動消息 
> - 死信 
> - 排定的訊息 

## <a name="connection-metrics"></a>連接計量

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|ActiveConnections|命名空間及實體上的作用中連線數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="resource-usage-metrics"></a>資源使用量計量

> [!NOTE] 
> 下列計量僅適用於**進階**層。 
> 
> 監視高級層命名空間的任何中斷的重要指標是：**每個命名空間的 CPU 使用率**和**每個命名空間的記憶體大小**。 使用 Azure 監視器設置這些指標的[警報](../azure-monitor/platform/alerts-metric.md)。
> 
> 可以監視的另一個指標是：**限制請求**。 只要命名空間保持在其記憶體、CPU 和代理連接限制內，這不應該是問題。 有關詳細資訊，請參閱[Azure 服務匯流排高級層中的限制](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| 標準名稱 | 描述 |
| ------------------- | ----------------- |
|每個命名空間的 CPU 使用量|命名空間的 CPU 使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大 <br/> 維度：EntityName|
|每個命名空間的記憶體大小使用量|命名空間的記憶體使用量百分比。<br/><br/> 單位：百分比 <br/> 彙總類型：最大 <br/> 維度：EntityName|

## <a name="metrics-dimensions"></a>計量維度

Azure 服務匯流排支援下列的 Azure 監視器計量維度。 將維度新增至計量為選擇性。 如果不新增維度，則會在命名空間層級指定計量。 

|維度名稱|描述|
| ------------------- | ----------------- |
|EntityName| 服務匯流排支援命名空間下的訊息實體。|

## <a name="set-up-alerts-on-metrics"></a>設定計量警示

1. 在 [服務匯流排命名空間]**** 頁面的 [計量]**** 索引標籤上，選取 [設定警示]****。 

    ![[計量] 頁面 - [設定警示] 功能表](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. 選擇 **"選擇目標**"選項，並在 **"選擇資源**"頁上執行以下操作： 
    1. 針對 [依資源類型篩選]**** 欄位，選取 [服務匯流排命名空間]****。 
    2. 針對 [依訂用帳戶篩選] **** 欄位，選取您的訂用帳戶。
    3. 從清單中選取 [服務匯流排命名空間]****。 
    4. 選擇 **"完成**"。 
    
        ![選取命名空間](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. 選取 [新增準則]****，然後在 [設定訊號邏輯]**** 頁面上執行下列動作：
    1. 針對 [訊號類型]****，選取 [計量]****。 
    2. 選取一個訊號。 例如：**服務錯誤**。 

        ![選取伺服器錯誤](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. 針對 [條件]****，選取 [大於]****。
    2. 針對 [時間彙總]****，選取 [總計]****。 
    3. 針對 [閾值]****，輸入 **5**。 
    4. 選擇 **"完成**"。    

        ![指定條件](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. 在 [建立規則]**** 頁面上，展開 [定義警示詳細資料]****，然後執行下列動作：
    1. 輸入警報**的名稱**。 
    2. 輸入警示的**描述**。
    3. 選取警示規則的**嚴重性**。 

        ![警示詳細資料](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. 在 [建立規則]**** 頁面上，展開 [定義動作群組]****，選取 [新增動作群組]****，然後在 [新增動作群組]**** 頁面上執行下列動作。 
    1. 輸入動作群組的名稱。
    2. 輸入動作群組的簡短名稱。 
    3. 選取您的訂用帳戶。 
    4. 選取資源群組。 
    5. 針對此逐步解說，輸入 **Send email** 作為 [動作名稱]****。
    6. 針對 [動作類型]****，選取 [電子郵件/簡訊/推播/語音]****。 
    7. 選取 [編輯詳細資料]****。 
    8. 在 [電子郵件/簡訊/推播/語音]**** 頁面上，執行下列動作：
        1. 選取 [電子郵件]****。 
        2. 輸入**電子郵件地址**。 
        3. 選取 [確定]****。

            ![警示詳細資料](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. 在 [新增動作群組]**** 頁面上，選取 [確定]****。 
1. 在 [建立規則]**** 頁面上，選取 [建立警示規則]****。 

    ![[建立警示規則] 按鈕](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>後續步驟

請參閱[Azure 監視器概述](../monitoring-and-diagnostics/monitoring-overview.md)。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


