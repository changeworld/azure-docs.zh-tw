---
title: Azure SignalR Service 中的服務模式
description: 概述 Azure SignalR Service 中的不同服務模式，說明其差異和適用的使用者案例
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143259"
---
# <a name="service-mode-in-azure-signalr-service"></a>Azure SignalR Service 中的服務模式

服務模式是 Azure SignalR Service 中的重要概念。 當您建立新的 SignalR 資源時，系統會要求您指定服務模式：

:::image type="content" source="media/concept-service-mode/create.png" alt-text="選擇建立時的服務模式":::

您也可以稍後在 [設定] 功能表中加以變更：

:::image type="content" source="media/concept-service-mode/update.png" alt-text="選擇建立時的服務模式":::

Azure SignalR Service 目前支援三種服務模式： **預設**、 **無伺服器** 和 **傳統**。 在不同的模式下，您的 SignalR 資源會有不同的行為。 在本文中，您將瞭解其差異，並說明如何根據您的案例選擇正確的服務模式。

## <a name="default-mode"></a>預設模式

當您建立新的 SignalR 資源時，預設模式是服務模式的預設值。 在此模式中，您的應用程式會作為一般 ASP.NET Core (或 ASP.NET) SignalR 應用程式，其中您的 web 伺服器會裝載稱為中樞伺服器以下的中樞 () 而且用戶端可以與中樞伺服器進行雙工即時通訊。 唯一的差別是，用戶端和伺服器都不會直接連接到 SignalR 服務，而是使用服務作為 proxy。 以下圖表說明預設模式中的一般應用程式結構：

:::image type="content" source="media/concept-service-mode/default.png" alt-text="選擇建立時的服務模式":::

因此，如果您有 SignalR 應用程式，而且想要與 SignalR 服務整合，則在大部分的情況下，預設模式應該是正確的選擇。

### <a name="connection-routing-in-default-mode"></a>預設模式下的連接路由

在預設模式下，中樞伺服器與 SignalR 服務 (之間會有 websocket 連線) 的 [伺服器連接]。 這些連接是用來在伺服器和用戶端之間傳送訊息。 當新的用戶端連線時，SignalR 服務會將用戶端路由傳送至一部中樞伺服器 (假設您有多部伺服器) 透過現有的伺服器連接。 然後，用戶端連線會在其存留期間保持相同的中樞伺服器。 當用戶端傳送訊息時，一律會移至相同的中樞伺服器。 您可以使用此行為，安全地維護中樞伺服器上個別連接的某些狀態。 例如，如果您想要在伺服器和用戶端之間串流某個東西，就不需要考慮資料封包移至不同伺服器的情況。

> [!IMPORTANT]
> 這也表示在預設模式下，用戶端無法在沒有先連接伺服器的情況下連線。 如果您的所有中樞伺服器因為網路中斷或伺服器重新開機而中斷連線，您的用戶端連線會收到錯誤，告知您沒有任何伺服器連線。 因此，您必須負責確定至少有一部中樞伺服器連線至 SignalR 服務 (例如，擁有多部中樞伺服器，並確定它們不會同時針對維護) 之類的專案離線。

此路由模型也表示當中樞伺服器離線時，路由傳送該伺服器的連線將會中斷。 因此，當您的中樞伺服器離線進行維護並適當地處理重新連接時，您應該會收到連接中斷，使其不會對您的應用程式造成負面影響。

## <a name="serverless-mode"></a>無伺服器模式

無伺服器模式（顧名思義）是一種模式，不能有任何中樞伺服器。 相較于預設模式，在此模式中，用戶端不需要中樞伺服器就能連線。 所有連線都是以「無伺服器」模式連線到服務，而服務則負責維護用戶端連線，例如在預設模式下處理用戶端 ping (這是由中樞伺服器) 處理。

此外，在此模式中沒有伺服器連線 (如果您嘗試使用服務 SDK 來建立伺服器連接，您將會收到錯誤) 。 因此，也不會有連接路由和伺服器用戶端的粘性 (，如) 的預設模式一節中所述。 但是您仍然可以有伺服器端應用程式，將訊息推送至用戶端。 這可以透過兩種方式來完成：使用 [REST api](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 進行一次性傳送，或透過 websocket 連線，讓您可以更有效率地傳送多個訊息 (請注意，此 websocket 連線與伺服器連接) 不同。

> [!NOTE]
> SignalR service [MANAGEMENT SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)都支援 REST API 和 websocket 的方式。 如果您要使用 .NET 以外的語言，您也可以手動叫用遵循此 [規格](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)的 REST api。
>
> 如果您使用 Azure Functions，則可以使用稱為函數系結 [的 Azure Functions (以下的 SignalR 服務](../azure-functions/functions-bindings-signalr-service.md) 系結) 將訊息傳送為輸出系結。

您的伺服器應用程式也可能會接收來自用戶端的訊息和連接事件。 服務會將訊息和連接事件傳遞給預先設定的端點， (使用 webhook 呼叫上游) 。 相較于預設模式，不保證任何的情況下，HTTP 要求的效率可能會比 websocket 連接更低。

如需有關如何設定上游的詳細資訊，請 [參閱此檔](./concept-upstream.md)。

以下是說明無伺服器模式運作方式的圖表：

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="選擇建立時的服務模式":::

> [!NOTE]
> 請注意，在預設模式下，如果您不想要通過中樞伺服器，也可以使用 REST API/管理 SDK/函式系結來直接將訊息傳送至用戶端。 但在預設模式下，用戶端連線仍會由中樞伺服器處理，而上游將無法在該模式下運作。

## <a name="classic-mode"></a>傳統模式

傳統是預設和無伺服器模式的混合模式。 在此模式中，當建立用戶端連線時，連線模式會決定是否已連接中樞伺服器。 如果有中樞伺服器，用戶端連線將會路由傳送至中樞伺服器。 否則，它會進入無伺服器模式，也就是無法將用戶端到伺服器的訊息傳遞至中樞伺服器。 這會造成一些不一致的情況，例如，如果所有中樞伺服器短時間無法使用，則在該時間內建立的所有用戶端連線都會處於無伺服器模式，且無法將訊息傳送至中樞伺服器。

> [!NOTE]
> 傳統模式主要是針對在預設和無伺服器模式之前所建立的應用程式提供回溯相容性。 強烈建議您不要再使用這個模式。 針對新的應用程式，請根據您的案例選擇 [預設] 或 [無伺服器]。 針對現有的應用程式，也建議您檢查您的使用案例，並選擇適當的服務模式。

傳統模式也不支援在無伺服器模式下進行上游之類的一些新功能。

## <a name="choose-the-right-service-mode"></a>選擇正確的服務模式

現在您應該瞭解服務模式之間的差異，並瞭解如何在兩者之間進行選擇。 如您在上一節中所學到的傳統模式，並不建議您在預設和無伺服器之間進行選擇。 以下是一些提示，可協助您針對現有的應用程式，對新的應用程式和淘汰傳統模式做出正確的選擇。

* 如果您已經熟悉 SignalR 程式庫的運作方式，以及想要從自我裝載的 SignalR 移至 Azure SignalR Service，請選擇 [預設模式]。 預設模式的運作方式與自我裝載 SignalR (完全相同，而且您可以在 SignalR 程式庫中使用相同的程式設計模型) ，SignalR 服務只是做為用戶端與中樞伺服器之間的 proxy。

* 如果您要建立新的應用程式，而不想要維護中樞伺服器和伺服器連接，請選擇 [無伺服器模式]。 此模式通常會與 Azure Functions 搭配運作，因此您完全不需要維護任何伺服器。 您仍然可以使用 REST API/管理 SDK/函式系結 + 上游) 來 (雙工通訊，但程式設計模型會與 SignalR 程式庫不同。

* 如果您有兩部中樞伺服器來提供用戶端連線和後端應用程式，以直接將訊息推送至用戶端 (例如透過 REST API) ，您仍應選擇 [預設模式]。 請記住，預設和無伺服器模式之間的主要差異在於您是否擁有中樞伺服器和用戶端連接的路由方式。 您可以在這兩種模式中使用 REST API/管理 SDK/函數系結。

* 例如，如果您真的有混合的案例，則在相同的 SignalR 資源上有兩個不同的中樞，一個用來作為傳統的 SignalR 中樞，另一個則用於 Azure Functions 且沒有中樞伺服器，您應該確實考慮將它們分隔成兩個 SignalR 資源，一個在預設模式下，另一個在無伺服器模式中。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用預設和無伺服器模式，請閱讀下列文章：

* [Azure SignalR Service 內部項目](signalr-concept-internals.md)

* [使用 Azure SignalR Service 來開發與設定 Azure Functions](signalr-concept-serverless-development-config.md)