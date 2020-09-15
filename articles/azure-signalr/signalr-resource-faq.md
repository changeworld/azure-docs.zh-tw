---
title: Azure SignalR Service 常見問題集
description: 取得 Azure SignalR Service 的常見問題解答，包括疑難排解和一般使用案例。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489556"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service 常見問題集

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service 是否已可用於生產用途？

是。
如需正式運作公告，請參閱 [Azure SignalR Service 現在已正式運作](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)。 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) 受到完整支援。

ASP.NET SignalR 的支援仍處於「公開預覽」階段。 [這裡提供程式碼範例](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>用戶端連線關閉，並出現錯誤訊息「沒有可用的伺服器。」 這代表什麼意思？

只有在用戶端將訊息傳送至 Azure SignalR Service 時，才會發生此錯誤。

如果您沒有任何應用程式伺服器，而僅使用 Azure SignalR Service REST API，則會有此*原先設計*的行為。
在無伺服器架構中，用戶端連線會處於「接聽」模式，而不會傳送任何訊息至 Azure SignalR Service。
閱讀[深入了解 REST API](./signalr-quickstart-rest-api.md)。

如果您有應用程式伺服器，則此錯誤訊息表示沒有任何應用程式伺服器連線到您的 Azure SignalR Service 執行個體。

可能的原因包括：
- 沒有與 Azure SignalR Service 連線的應用程式伺服器。 請檢查應用程式伺服器記錄中是否有可能的連線錯誤。 此案例在具有多個應用程式伺服器的高可用性設定中很少見。
- Azure SignalR Service 執行個體有連線問題。 這是暫時性的問題，執行個體會自動復原。
如果問題持續超過一小時，請[在 GitHub 上提出問題](https://github.com/Azure/azure-signalr/issues/new)或[在 Azure 中建立支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

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

您可以設定用戶端傳輸，如 [ASP.NET Core SignalR 組態](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)中所述。

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure 入口網站中顯示的類似計量 (例如訊息計數或連線計數) 有何意義？ 我應該選擇哪一種彙總類型？

您可以在 [Azure SignalR Service中的訊息和連線](signalr-concept-messages-and-connections.md)中，找到有關我們如何計算這些計量的詳細資料。

我們已在 Azure SignalR Service 資源的概觀窗格中，為您選擇適當的彙總類型。 如果您移至計量窗格，可以採用 [Azure SignalR Service 中的訊息和連線](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)的彙總類型作為參考。

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>服務模式 `Default``Serverless` 和 `Classic` 的意義為何？ 我該如何選擇？

模式的相關資訊如下：
* `Default` 模式「需要」中樞伺服器。 在此模式中，Azure SignalR Service 會將用戶端流量路由傳送至其連線的中樞伺服器連線。 Azure SignalR Service 會檢查已連線的中樞伺服器。 如果服務找不到已連線的中樞伺服器，便會拒絕連入的用戶端連線。 您也可以在此模式中使用*管理 API*，直接透過 Azure SignalR Service 來管理已連線的用戶端。
* `Serverless` 模式「不會」允許任何伺服器連線。 也就是說，該模式會拒絕所有的伺服器連線。 所有用戶端都必須處於無伺服器模式。 用戶端會連線到 Azure SignalR Service，而使用者通常會使用無伺服器技術 (例如 *Azure Function*) 來處理中樞邏輯。 請參閱 Azure SignalR Service 中使用無伺服器模式的[簡單範例](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu)。
* `Classic` 模式是混合的狀態。 當中樞具有伺服器連線時，新的用戶端將會路由傳送至中樞伺服器。 否則，用戶端將會進入無伺服器模式。 

  這可能會造成問題。 例如，所有伺服器都有一段時間無法連線，有些用戶端會進入無伺服器模式，而不會路由至中樞伺服器。

以下是選擇模式的一些指導方針：
- 如果沒有中樞伺服器，請選擇 `Serverless`。
- 如果所有中樞都有中樞伺服器，請選擇 `Default`。
- 如果有些中樞具有中樞伺服器，但有些沒有，可以選擇 `Classic`，但這可能會造成問題。 較好的方式是建立兩個執行個體：一個是 `Serverless`，另一個則是 `Default`。

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
