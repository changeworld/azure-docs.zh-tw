---
title: Azure SignalR Service 中的復原和災害復原
description: 概述如何設定多個 SignalR 服務執行個體來達到復原和災害復原
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: b1cb48d1ae858dbcd0df80780b4c3cee3deac75b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976502"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Azure SignalR Service 中的復原和災害復原

復原和災害復原是線上系統的常見需求。 Azure SignalR Service 服務已保證有 99.9% 的可用性，但仍是一項區域性服務。
當整個區域中斷時，您的服務實例一律會在一個區域中執行，而不會容錯移轉至另一個區域。

相反地，我們服務 SDK 提供的功能可支援多個 SignalR 服務執行個體，而且會在其中一些執行個體無法使用時，自動切換至其他執行個體。
使用這項功能時，您將能夠在發生嚴重損壞時復原，但您必須自行設定適當的系統拓撲。 您將在本文中了解如何這麼做。

## <a name="high-available-architecture-for-signalr-service"></a>SignalR 服務的高可用性架構

為了讓 SignalR 服務能跨區域進行復原，您需要在不同區域中設定多個服務執行個體。 這樣一來，當某個區域的服務中斷時，其他區域就可用來當做備份。
將多個服務執行個體連線到應用程式伺服器時會有兩個角色：主要和次要。
主要執行個體會接收線上流量，而次要執行個體雖然有完整功能，但是是主要執行個體的備份。
在我們的 SDK 實作中，交涉 (negotiate) 只會傳回主要端點，因此在正常情況下，用戶端只能連線至主要端點。
但當主要執行個體無法運作時，交涉會傳回次要端點，因此，用戶端仍然可以進行連線。
主要實例和應用程式伺服器會透過一般的伺服器連線來連線，但次要實例和應用程式伺服器會透過一種特殊類型的連線（稱為弱連線）來連線。
弱式連接的主要差異在於，它不接受用戶端連線路由，因為次要實例位於另一個區域。 將用戶端路由傳送到另一個區域不是最佳選擇 (會增加延遲) 。

連線至多個應用程式伺服器時，一個服務執行個體可以有不同的角色。
針對區域案例，其中一個標準設定是有兩組 (含) 以上的 SignalR 服務執行個體和應用程式伺服器。
在每一組配對中，應用程式伺服器與 SignalR 服務會位在相同區域中，而 SignalR 服務會連線到作為主要角色的應用程式伺服器。
每一組的應用程式伺服器和 SignalR 服務之間也會連線，但當 SignalR 連線到另一個區域中的伺服器時，就會變成次要角色。

在此拓撲中，來自一部伺服器的訊息仍然可以傳遞至所有用戶端，因為所有應用程式伺服器和 SignalR 服務執行個體都會互相連線。
但是，連線的用戶端一律會路由至相同區域中的應用程式伺服器，以達到最理想的網路延遲。

以下是說明這類拓撲的圖示：

![下圖顯示兩個區域的每個區域都有應用程式伺服器和 SignalR 服務，其中每一部伺服器都與其區域中的 SignalR 服務相關聯，並與另一個區域中的服務做為次要。](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>設定具有多個 SignalR 服務執行個體的應用程式伺服器

在每個區域中建立 SignalR 服務和應用程式伺服器之後，您可以將應用程式伺服器設定為連線到所有 SignalR 服務執行個體。

執行此動作的方式有兩種：

### <a name="through-config"></a>透過 config

您應該已經知道如何透過環境變數/應用程式設定/cofig，在名為的設定專案中設定 SignalR 服務連接字串 `Azure:SignalR:ConnectionString` 。
如果您有多個端點，您可以使用下列格式在多個 config 項目中設定每個連接字串：

```
Azure:SignalR:ConnectionString:<name>:<role>
```

此處的 `<name>` 表示端點名稱，而 `<role>` 是其角色 (主要或次要)。
名稱為選擇性項目，但此項目有助於您進一步自訂多個端點之間的路由行為。

### <a name="through-code"></a>透過程式碼

如果您想要將連接字串儲存在其他位置，您也可以在程式碼中讀取這些連接字串，並在 `AddAzureSignalR()` ASP.NET Core) 中呼叫 (，或 `MapAzureSignalR()` 在 ASP.NET) 中 (時使用這些字串作為參數。

此為範例程式碼：

ASP.NET Core：

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET：

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

您可以設定多個主要或次要實例。 如果有多個主要和/或次要實例，negotiate 會依下列順序傳回端點：

1. 如果在線上至少有一個主要實例，則會傳回隨機的主要線上實例。
2. 如果所有主要實例都已關閉，則會傳回隨機的次要線上實例。

## <a name="failover-sequence-and-best-practice"></a>容錯移轉順序和最佳做法

現在您已設定正確的系統拓撲。 每當有一個 SignalR 服務執行個體停止運作時，線上流量就會路由至其他執行個體。
以下是主要執行個體停止運作 (並在稍後復原) 時發生的狀況：

1. 主要服務執行個體停止運作時，此執行個體上的所有伺服器連線皆會中斷。
2. 連線到此執行個體的所有伺服器都會將其標示為離線，而交涉會停止傳回此端點，並開始傳回次要端點。
3. 此執行個體上的所有用戶端連線也會關閉，用戶端將會重新連線。 由於應用程式伺服器現在會傳回次要端點，因此用戶端會連線至次要執行個體。
4. 現在，次要執行個體會接收所有線上流量。 所有來自伺服器的訊息仍可傳遞到用戶端，因為次要執行個體會連線到所有應用程式伺服器。 但是從用戶端傳送到伺服器的訊息只會路由至相同區域中的應用程式伺服器。
5. 當主要執行個體復原並回到線上狀態之後，應用程式伺服器就會重新與之建立連線，並將其標示為 [線上]。 交涉現在會再次傳回主要端點，讓新的用戶端連線到主要執行個體。 但現有的用戶端不會遭到捨棄，而且會繼續路由到次要區域，直到這些用戶端自行中斷為止。

以下圖示說明如何在 SignalR 服務進行容錯移轉：

圖 1 容錯移轉之前![容錯移轉之前](media/signalr-concept-disaster-recovery/before-failover.png)

圖 2 容錯移轉之後![容錯移轉之後](media/signalr-concept-disaster-recovery/after-failover.png)

圖 3 主要執行個體復原後的短暫時間![主要執行個體復原後的短暫時間](media/signalr-concept-disaster-recovery/after-recover.png)

在正常的情況下，您只會看到主要應用程式伺服器及 SignalR 服務有線上流量 (藍色)。
容錯移轉之後，次要應用程式伺服器與 SignalR 服務也會開始作用。
當主要 SignalR 服務恢復上線後，新的用戶端將會連線到主要 SignalR。 但現有的用戶端仍是連線至次要執行個體，因此兩個執行個體都有流量。
在所有現有的用戶端都中斷連線後，系統就會回到正常情況 (圖 1)。

實作跨區域的高可用性架構有兩個主要模式：

1. 第一個模式是讓一組應用程式伺服器和 SignalR 服務執行個體接收所有線上流量，並將另一組當作備份 (稱為主動/被動，如圖 1 所示)。 
2. 另一個模式是有兩組 (或兩組以上) 應用程式伺服器和 SignalR 服務執行個體，每一組都接收部分線上流量及作為其他組的備份 (稱為主動/主動，類似圖 3)。

SignalR 服務可支援這兩種模式，主要差異在於您如何實作應用程式伺服器。
如果應用程式伺服器為主動/被動，SignalR 服務也會是主動/被動 (因為主要應用程式伺服器只會傳回其主要的 SignalR 服務執行個體)。
如果應用程式伺服器為主動/主動，SignalR 服務也會是主動/主動 (因為所有應用程式伺服器會傳回自己的主要 SignalR 執行個體，因此所有執行個體都可以接收流量)。

請注意，無論您選擇使用哪種模式，您都必須將每個 SignalR 服務實例連線到應用程式伺服器做為主要複本。

也因為 SignalR 連線的本質 (很長的連線)，用戶端會在發生災害和容錯移轉時遇到連線中斷。
您必須在用戶端處理這類情況，讓終端客戶了解此情況。 例如，在連線關閉後重新連線。

## <a name="next-steps"></a>後續步驟

在本文中，您已經了解如何設定您的應用程式以恢復 SignalR 服務的功能。 若要深入了解 SignalR 服務中的伺服器/用戶端連線及連線路由，您可以閱讀[這篇文章](signalr-concept-internals.md)，以了解 SignalR 服務的內部運作。

針對調整案例（例如分區化），其會使用多個實例來處理大量連接，請參閱 [如何調整多個實例](signalr-howto-scale-multi-instances.md)。
