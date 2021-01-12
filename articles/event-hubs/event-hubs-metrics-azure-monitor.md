---
title: Azure 監視器中的計量 - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何使用「Azure 監視」來監視「Azure 事件中樞」的資訊
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5b055c02783c40d844d1c6306bbb71cb23d602f2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118790"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure 監視器中的 Azure 事件中樞計量

事件中樞計量會提供您 Azure 訂用帳戶中事件中樞資源的狀態。 您可以使用一組豐富的計量資料，來評估事件中樞的整體健康情況 (不僅是命名空間層級，還包括實體層級)。 這些統計資料相當重要，因為它們可協助您監視事件中樞的狀態。 計量也可協助針對問題的根本原因進行疑難排解，而不需要連絡 Azure 支援。

「Azure 監視器」提供統一的使用者介面，可供您監視各個不同的 Azure 服務。 如需詳細資訊，請參閱 [Microsoft Azure 中的監視](../azure-monitor/overview.md)和 GitHub 上的 [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (使用 .NET 擷取 Azure 監視計量) 範例。

## <a name="access-metrics"></a>存取計量

Azure 監視器提供了多種方法供您存取計量。 您可以透過 [Azure 入口網站](https://portal.azure.com)來存取計量，或使用 Azure 監視器 API (REST 和 .NET) 和分析解決方案 (例如 Log Analytics 和事件中樞) 來存取計量。 如需詳細資訊，請參閱[監視 Azure 監視器所收集的資料](../azure-monitor/platform/data-platform.md)。

計量是預設啟用的功能，您可以存取最近 30 天的資料。 如果您需要延長這些資料的保留時間，您可以將計量資料封存到 Azure 儲存體帳戶。 此功能可於 Azure 監視器的[診斷設定](../azure-monitor/platform/diagnostic-settings.md)中進行設定。


## <a name="access-metrics-in-the-portal"></a>在入口網站中存取計量

您可以在 [Azure 入口網站](https://portal.azure.com)中監視一段時間的計量。 下列範例示範如何檢視帳戶層級的成功要求及連入要求：

![檢視成功計量][1]

您也可以直接透過命名空間來存取計量。 若要這樣做，請選取您的命名空間，然後按一下 [計量]。 若要顯示篩選至事件中樞範圍的計量，請選取事件中樞，然後按一下 [計量]。

針對支援維度的計量，您必須使用所需的維度值來進行篩選，如以下範例所示：

![使用維度值來進行篩選][2]

## <a name="billing"></a>計費

在 Azure 監視器中使用計量目前是免費的服務。 不過，如果您使用內嵌計量資料的額外解決方案，可能就需支付這些解決方案的使用費。 例如，如果您將計量資料封存到 Azure 儲存體帳戶，就要支付 Azure 儲存體的使用費。 如果您將計量資料串流至 Azure 監視器記錄以進行高階分析，也會向您收取費用。

下列計量會提供您服務健康狀態的概觀。 

> [!NOTE]
> 我們正在取代許多計量，因為它們移至不同的名稱下。 這可能需要您更新參考。 此後不再支援標記「已取代」關鍵字的計量。

所有計量值都會每分鐘傳送至「Azure 監視器」。 時間細微性會定義呈現計量值的時間間隔。 針對所有「事件中樞」計量，支援的時間間隔為 1 分鐘。

## <a name="azure-event-hubs-metrics"></a>Azure 事件中樞計量
如需服務所支援的度量清單，請參閱 [Azure 事件中樞](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure 監視器與 SIEM 工具整合
使用 Azure 監視器讓您的監視資料 (活動記錄、診斷記錄等 ) 到事件中樞，可讓您輕鬆地整合安全性資訊和事件管理 (SIEM) 工具。 如需詳細資訊，請參閱下列文章/blog 文章：

- [將 Azure 監視資料串流至事件中樞以供外部工具取用](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Azure 記錄整合簡介](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [使用 Azure 監視器以與 SIEM 工具整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

在 SIEM 工具從事件中樞取用記錄資料的案例中，如果您未看到任何內送訊息，或您在計量圖表中看到內送訊息但沒有任何外寄訊息，請遵循下列步驟：

- 如果沒有內送 **訊息**，則表示 Azure 監視器服務未將 audit/diagnostics 記錄移至事件中樞。 在此案例中，Azure 監視器小組開啟支援票證。 
- 如果有內送訊息，但 **沒有任何外寄訊息**，則表示 SIEM 應用程式未讀取訊息。 請聯絡 SIEM 提供者，以判斷事件中樞的設定是否正確。


## <a name="next-steps"></a>後續步驟

* 請參閱 [Azure 監視概觀](../azure-monitor/overview.md)。
* GitHub 上的[使用 .NET 擷取 Azure 監視計量](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) \(英文\) 範例。 

如需事件中樞的詳細資訊，請造訪下列連結：

- 開始使用事件中樞教學課程
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [事件中樞常見問題集](event-hubs-faq.md)
* [使用事件中樞的完整範例應用程式](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
