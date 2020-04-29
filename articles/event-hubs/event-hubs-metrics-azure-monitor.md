---
title: Azure 監視器中的計量 - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何使用「Azure 監視」來監視「Azure 事件中樞」的資訊
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162645"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure 監視器中的 Azure 事件中樞計量

事件中樞計量會提供您 Azure 訂用帳戶中事件中樞資源的狀態。 您可以使用一組豐富的計量資料，來評估事件中樞的整體健康情況 (不僅是命名空間層級，還包括實體層級)。 這些統計資料相當重要，因為它們可協助您監視事件中樞的狀態。 計量也可協助針對問題的根本原因進行疑難排解，而不需要連絡 Azure 支援。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱 [Microsoft Azure 中的監視](../monitoring-and-diagnostics/monitoring-overview.md)和 GitHub 上的 [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (使用 .NET 擷取 Azure 監視計量) 範例。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以透過 [Azure 入口網站](https://portal.azure.com)來存取計量，或使用 Azure 監視器 API (REST 和 .NET) 和分析解決方案 (例如 Log Analytics 和事件中樞) 來存取計量。 如需詳細資訊，請參閱[監視 Azure 監視器所收集的資料](../azure-monitor/platform/data-platform.md)。

計量是預設啟用的功能，您可以存取最近 30 天的資料。 如果您需要延長這些資料的保留時間，您可以將計量資料封存到 Azure 儲存體帳戶。 此功能可於 Azure 監視器的[診斷設定](../azure-monitor/platform/diagnostic-settings.md)中進行設定。


## <a name="access-metrics-in-the-portal"></a>在入口網站中存取計量

您可以在 [Azure 入口網站](https://portal.azure.com)中監視一段時間的計量。 下列範例示範如何檢視帳戶層級的成功要求及連入要求：

![檢視成功計量][1]

您也可以直接透過命名空間來存取計量。 若要這麼做，請選取您的命名空間，然後按一下 [**計量**]。 若要顯示篩選至事件中樞範圍的計量，請選取事件中樞，然後按一下 [計量]****。

針對支援維度的計量，您必須使用所需的維度值來進行篩選，如以下範例所示：

![使用維度值來進行篩選][2]

## <a name="billing"></a>計費

在 Azure 監視器中使用計量目前是免費的服務。 不過，如果您使用內嵌計量資料的額外解決方案，可能就需支付這些解決方案的使用費。 例如，如果您將計量資料封存到 Azure 儲存體帳戶，就要支付 Azure 儲存體的使用費。 如果您將計量資料串流至 Azure 監視器記錄以進行先進的分析，則也會向您收取 Azure 的費用。

下列計量會提供您服務健康狀態的概觀。 

> [!NOTE]
> 我們正在取代許多計量，因為它們移至不同的名稱下。 這可能需要您更新參考。 此後不再支援標記「已取代」關鍵字的計量。

所有計量值都會每分鐘傳送至「Azure 監視器」。 時間細微性會定義呈現計量值的時間間隔。 針對所有「事件中樞」計量，支援的時間間隔為 1 分鐘。

## <a name="request-metrics"></a>要求計量

計算資料和管理作業要求的數目。

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
| 傳入的要求  | 在指定的期間內，向「Azure 事件中樞」服務提出的要求數目。 <br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName |
| 成功的要求    | 在指定的期間內，向「Azure 事件中樞」服務提出的成功要求數目。 <br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName |
| 伺服器錯誤  | 在指定的期間內，因「Azure 事件中樞」服務發生錯誤而未處理的要求數目。 <br/><br/>單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName |
|使用者錯誤 |在指定的期間內，因使用者錯誤而未處理的要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|超過配額錯誤 |超出可用配額的要求數目。 如需有關「事件中樞」配額的詳細資訊，請參閱[這篇文章](event-hubs-quotas.md)。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="throughput-metrics"></a>輸送量計量

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
|節流要求 |因為超出輸送量單位使用量而節流的要求數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="message-metrics"></a>訊息計量

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
|傳入訊息 |在指定的期間內，傳送到「事件中樞」的事件或訊息數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|外送訊息 |在指定的期間內，從「事件中樞」擷取的事件或訊息數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|傳入位元組 |在指定的期間內，傳送到「Azure 事件中樞」服務的位元組數目。<br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|傳出位元組數 |在指定的期間內，從「Azure 事件中樞」服務擷取的位元組數目。<br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="connection-metrics"></a>連接計量

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
|ActiveConnections |命名空間及實體上的作用中連線數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|已開啟連接 |已開啟的連線數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|連接已關閉 |已關閉的連線數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="event-hubs-capture-metrics"></a>事件中樞擷取計量

當您針對事件中樞啟用「擷取」功能時，即可監視「事件中樞」的「擷取」計量。 下列計量說明當您啟用「擷取」時可以監視的項目。

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
|Capture 待處理專案 |尚未擷取到所選目的地的位元組數目。<br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|已捕獲的訊息 |在指定的期間內，擷取到所選目的地的訊息或事件數目。<br/><br/> 單位：計數 <br/> 彙總類型：總計 <br/> 維度：EntityName|
|已捕獲的位元組 |在指定的期間內，擷取到所選目的地的位元組數目。<br/><br/> 單位：位元組 <br/> 彙總類型：總計 <br/> 維度：EntityName|

## <a name="metrics-dimensions"></a>計量維度

「Azure 事件中樞」針對「Azure 監視器」中的計量支援下列維度。 將維度新增至計量為選擇性。 如果不新增維度，則會在命名空間層級指定計量。 

| 標準名稱 | 說明 |
| ------------------- | ----------------- |
|EntityName| 「事件中樞」可在命名空間底下支援事件中樞實體。|

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure 監視器與 SIEM 工具整合
使用 Azure 監視器將您的監視資料（活動記錄、診斷記錄等）路由傳送至事件中樞，可讓您輕鬆地與安全性資訊和事件管理（SIEM）工具整合。 如需詳細資訊，請參閱下列文章/blog 文章：

- [將 Azure 監視資料串流至事件中樞以供外部工具取用](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Azure 記錄整合簡介](../security/fundamentals/azure-log-integration-overview.md)
- [使用 Azure 監視器以與 SIEM 工具整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

在 SIEM 工具從事件中樞取用記錄資料的案例中，如果您未看到任何內送訊息，或在計量圖表中看不到傳入訊息，但沒有任何外寄訊息，請遵循下列步驟：

- 如果沒有內送**訊息**，這表示 Azure 監視器服務不會將 audit/diagnostics 記錄移至事件中樞。 在此案例中，向 Azure 監視器小組開啟支援票證。 
- 如果有傳入訊息，但**沒有外寄訊息**，這表示 SIEM 應用程式不會讀取訊息。 請洽詢 SIEM 提供者，以判斷事件中樞的設定是否正確。


## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure 監視概觀](../monitoring-and-diagnostics/monitoring-overview.md)。
* GitHub 上的[使用 .NET 擷取 Azure 監視計量](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) \(英文\) 範例。 

如需事件中樞的詳細資訊，請造訪下列連結：

- 開始使用事件中樞教學課程
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [使用事件中樞的完整範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



