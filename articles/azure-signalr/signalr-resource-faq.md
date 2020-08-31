---
title: Azure SignalR Service 常見問題集
description: 快速存取 Azure SignalR Service 的常見問題，以了解疑難排解的方式和一般使用案例。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 6d104e41a0cae906c346e81a26617a9d29795fb3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853280"
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

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure 入口網站中顯示的類似計量 (例如訊息計數或連線計數) 有何意義？ 我應該選擇哪一種彙總類型？

如需如何計算這些計量的詳細資料，請參閱[此處](signalr-concept-messages-and-connections.md)。

我們已在 Azure SignalR Service 資源的概觀刀鋒視窗中，為您選擇適當的彙總類型。 如果您移至計量刀鋒視窗，可以將[這裡](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)的彙總類型作為參考。

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>服務模式 `Default`/`Serverless`/`Classic` 的意義為何？ 我該如何選擇？

模式：
* `Default` 模式 *需要*中樞伺服器。 在此模式中，Azure SignalR 會將用戶端流量路由傳送至其連線的中樞伺服器連線。 Azure SignalR 會檢查已連線的中樞伺服器。 如果找不到已連線的中樞伺服器，Azure SignalR 便會拒絕連入的用戶端連線。 您也可以在此模式下使用**管理 API**，直接透過 Azure SignalR 來管理已連線的用戶端。
* `Serverless` 模式*不會*允許任何伺服器連線，也就是說，其會拒絕所有伺服器連線。 所有用戶端都必須處於無伺服器模式。 用戶端會連線到 Azure SignalR，而使用者通常會使用無伺服器技術 (例如 **Azure Function**) 來處理中樞邏輯。 請參閱使用 Azure SignalR 無伺服器模式的[簡單範例](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu)。
* `Classic` 模式是混合的狀態。 當中樞具有伺服器連線時，新的用戶端將會路由至中樞伺服器，否則用戶端會進入無伺服器模式。

  這可能會造成一些問題；例如，所有伺服器都有一段時間無法連線，有些用戶端會進入無伺服器模式，而不會路由至中樞伺服器。

選擇：
1. 沒有中樞伺服器，請選擇 `Serverless`。
1. 所有中樞都有中樞伺服器，請選擇 `Default`。
1. 有些中樞有中樞伺服器，有些沒有，請選擇 `Classic`，但這可能會造成一些問題；更好的方法是建立兩個執行個體，一個是 `Serverless`，另一個則是 `Default`。

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>使用適用於 ASP.NET SignalR 的 Azure SignalR 時會有任何功能差異嗎？
使用 Azure SignalR 時，已不再支援 ASP.NET SignalR 的某些 API 和功能：
- 使用 Azure SignalR 時，不支援在用戶端與中樞之間傳遞任意狀態的功能 (通常稱為 `HubState`)
- 使用 Azure SignalR 時，尚不支援 `PersistentConnection` 類別
- 使用 Azure SignalR 時，不支援**永久的框架傳輸**
- 當用戶端離線時，Azure SignalR 不再重新執行傳送至用戶端的訊息
- 使用 Azure SignalR 時，一律會將用戶端連線的流量 (也稱為 **長時間**) 傳送至連線期間的應用程式伺服器執行個體

對 ASP.NET SignalR 的支援著重於相容性，因此不支援 ASP.NET Core SignalR 的所有新功能。 例如，**MessagePack**、**串流**等，僅適用於 ASP.NET Core SignalR 應用程式。

SignalR Service 可以設定為不同的服務模式：`Classic`/`Default`/`Serverles`。 在此 ASP.NET 支援中，不支援 `Serverless` 模式。 資料平面 REST API 也不受支援。

## <a name="where-do-my-data-reside"></a>我的資料位於何處？

Azure SignalR Service 是以資料處理器服務的形式運作， 不會儲存任何客戶內容，而且設計為提供資料落地功能。 如果您將 Azure SignalR Service 與其他 Azure 服務搭配使用，例如用於診斷的 Azure 儲存體，請查看[此處](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)以取得如何在 Azure 區域中使用資料落地的指引。
