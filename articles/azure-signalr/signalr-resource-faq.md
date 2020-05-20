---
title: Azure SignalR Service 常見問題集
description: 快速存取 Azure SignalR Service 的常見問題，以了解疑難排解的方式和一般使用案例。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dde11b6097dddb1568f5adfea811606214a9759e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "75891258"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service 常見問題集

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service 是否已可用於生產用途？

是。
如需正式運作公告，請參閱 [Azure SignalR Service 現在已正式運作](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)。 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) 受到完整支援。

ASP.NET SignalR 的支援仍處於*公開預覽*階段。 以下是[程式碼範例](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>用戶端連線關閉，並出現錯誤訊息「沒有可用的伺服器」。 這代表什麼意思？

只有在用戶端將訊息傳送至 SignalR Service 時，才會發生此錯誤。

如果您沒有任何應用程式伺服器，而僅使用 SignalR Service REST API，則會有此**原先設計**的行為。
在無伺服器架構中，用戶端連線會處於**接聽**模式，而不會傳送任何訊息至 SignalR Service。
請參閱 [REST API](./signalr-quickstart-rest-api.md) 以深入了解。

如果您有應用程式伺服器，則此錯誤訊息表示沒有任何應用程式伺服器連線到您的 SignalR Service 執行個體。

可能的原因包括：
- 沒有與 SignalR Service 連線的應用程式伺服器。 請檢查應用程式伺服器記錄中是否有可能的連線錯誤。 此案例在具有多個應用程式伺服器的高可用性設定中很少見。
- SignalR Service 執行個體有連線問題。 這是暫時性的問題，會自動復原。
如果問題持續超過一小時，請[在 GitHub 上提出問題](https://github.com/Azure/azure-signalr/issues/new)或[在 Azure 中建立支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>有多個應用程式伺服器時，用戶端訊息會傳送至所有伺服器還是僅傳送至其中之一？

這是用戶端與應用程式伺服器之間的一對一對應。 來自某一用戶端的訊息，一律會傳送至相同的應用程式伺服器。

在用戶端或應用程式伺服器中斷連線之前，會持續保有用戶端與應用程式伺服器之間的對應。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>如果我的其中一個應用程式伺服器已關閉，我要如何發現並獲得通知？

SignalR Service 會監視來自應用程式伺服器的活動訊號。
如果在指定的一段時間內未收到活動訊號，即會將應用程式伺服器視為離線。 對應至此應用程式伺服器的所有用戶端連線都將中斷。

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>為什麼我的自訂 `IUserIdProvider` 在從 ASP.NET Core SignalR SDK 切換至 Azure SignalR Service SDK 時擲回例外狀況？

在呼叫 `IUserIdProvider` 時，ASP.NET Core SignalR SDK 與 Azure SignalR Service SDK 的參數 `HubConnectionContext context` 不相同。

在 ASP.NET Core SignalR 中，`HubConnectionContext context` 是來自於實體用戶端連線的內容，且所有屬性都具有有效值。

在 Azure SignalR Service SDK 中，`HubConnectionContext context` 則是來自於邏輯用戶端連線的內容。 實體用戶端連線會連線到 SignalR Service 執行個體，因此只會提供有限數量的屬性。

目前只有 `HubConnectionContext.GetHttpContext()` 和 `HubConnectionContext.User` 可供存取。
您可以在[這裡](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)查看原始程式碼。

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>我是否可設定 SignalR Service 中的可用傳輸，如同在伺服器端為 ASP.NET Core SignalR 設定一般？ 例如，停用 WebSocket 傳輸？

否。

Azure SignalR Service 依預設會提供 ASP.NET Core SignalR 所支援的三種傳輸。 您無法加以設定。 SignalR Service 會處理所有用戶端連線的連線與傳輸。

您可以依照[這裡](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)的說明設定用戶端傳輸。
