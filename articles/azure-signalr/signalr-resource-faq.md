---
title: Azure SignalR Service 常見問題集
description: 取得 Azure SignalR Service 的常見問題解答，包括疑難排解和一般使用案例。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 910de9efbd132fb98a0c4bd596867800f65f5ad5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150957"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service 常見問題集

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service 是否已可用於生產用途？

是，已正式推出 [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) 和 [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) 的支援。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>有多個應用程式伺服器時，用戶端訊息會傳送至所有伺服器還是僅傳送至其中之一？

用戶端與應用程式伺服器之間具有一對一對應。 來自某一用戶端的訊息，一律會傳送至相同的應用程式伺服器。

此對應會保留，直到用戶端或應用程式伺服器中斷連線。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>如果我的其中一個應用程式伺服器已關閉，我要如何發現並獲得通知？

Azure SignalR Service 會監視來自應用程式伺服器的活動訊號。
如果在指定的一段時間內未收到活動訊號，即會將應用程式伺服器視為離線。 對應至此應用程式伺服器的所有用戶端連線都將中斷。

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>為什麼我的自訂 `IUserIdProvider` 在從 ASP.NET Core SignalR SDK 切換至 Azure SignalR Service SDK 時擲回例外狀況？

在呼叫 `IUserIdProvider` 時，ASP.NET Core SignalR SDK 與 Azure SignalR Service SDK 的參數 `HubConnectionContext context` 不相同。

在 ASP.NET Core SignalR 中，`HubConnectionContext context` 是來自於實體用戶端連線的內容，且所有屬性都具有有效值。

在 Azure SignalR Service SDK 中，`HubConnectionContext context` 則是來自於邏輯用戶端連線的內容。 實體用戶端會連線到 Azure SignalR Service 執行個體，因此只會提供有限數量的屬性。

目前只有 `HubConnectionContext.GetHttpContext()` 和 `HubConnectionContext.User` 可供存取。
您可以[檢查原始程式碼](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)。

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>我是否可在具有 ASP.NET Core SignalR 的伺服器端設定 Azure SignalR Service 中的可用傳輸？ 例如，我是否可停用 WebSocket 傳輸？

否。

Azure SignalR Service 依預設會提供 ASP.NET Core SignalR 所支援的三種傳輸。 您無法設定。 Azure SignalR Service 會處理所有用戶端連線的連線與傳輸。

您可以設定用戶端傳輸，如 [ASP.NET Core SignalR 組態](/aspnet/core/signalr/configuration#configure-allowed-transports-1)中所述。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure 入口網站中顯示的類似計量 (例如訊息計數或連線計數) 有何意義？ 我應該選擇哪一種彙總類型？

您可以在 [Azure SignalR Service中的訊息和連線](signalr-concept-messages-and-connections.md)中，找到有關我們如何計算這些計量的詳細資料。

我們已在 Azure SignalR Service 資源的概觀窗格中，為您選擇適當的彙總類型。 如果您移至計量窗格，可以採用 [Azure SignalR Service 中的訊息和連線](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)的彙總類型作為參考。

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>服務模式 `Default``Serverless` 和 `Classic` 的意義為何？ 我該如何選擇？

針對新的應用程式，只應該使用預設和無伺服器模式。 主要差異在於您是否有應用程式伺服器可建立服務的伺服器連線 (也就是使用 `AddAzureSignalR()` 連線到服務)。 如果是，請使用預設模式，否則使用無伺服器模式。

傳統模式是針對現有應用程式的回溯相容性所設計，因此不應該用於新的應用程式。

如需服務模式的詳細資訊，請參閱[本文件](concept-service-mode.md)。

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>我可以在無伺服器模式下從用戶端傳送訊息嗎？

如果您在 SignalR 執行個體中設定上游，則可以從用戶端傳送訊息。 上游是一組可從 SignalR 服務接收訊息和連線事件的端點。 如果未設定上游，則會忽略來自用戶端的訊息。

如需上游的詳細資訊，請參閱[本文件](concept-upstream.md)。

上游目前為公開預覽狀態。

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>使用適用於 ASP.NET SignalR 的 Azure SignalR Service 時會有任何功能差異嗎？

使用 Azure SignalR Service 時，系統已不支援 ASP.NET SignalR 的某些 API 和功能：
- 不支援在用戶端與中樞之間傳遞任意狀態的功能 (通常稱為 `HubState`)。
- 不支援 `PersistentConnection` 類別。
- 不支援「永久的框架傳輸」。
- 當用戶端離線時，Azure SignalR Service 不再重新執行傳送至用戶端的訊息。
- 使用 Azure SignalR Service 時，一個用戶端連線的流量一律會在連線期間路由 (也稱為「相黏」) 到一個應用程式伺服器執行個體。

對 ASP.NET SignalR 的支援著重於相容性，因此不支援 ASP.NET Core SignalR 的所有新功能。 例如，MessagePack 和串流等，僅適用於 ASP.NET Core SignalR 應用程式。

您可以針對 `Classic`、`Default`和 `Serverless` 等不同的服務模式設定 Azure SignalR Service。 ASP.NET Core 不支援 `Serverless` 模式。 資料平面 REST API 也不受支援。

## <a name="where-does-my-data-reside"></a>我的資料在哪裡？

Azure SignalR Service 是以資料處理器服務的形式運作， 不會儲存任何客戶內容，而且設計為包括資料落地功能。 如果您將 Azure SignalR Service 與其他 Azure 服務搭配使用，例如用於診斷的 Azure 儲存體，請查看[此技術白皮書](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)以取得如何在 Azure 區域中使用資料落地的指導。