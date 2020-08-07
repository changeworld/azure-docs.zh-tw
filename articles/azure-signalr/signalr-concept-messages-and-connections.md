---
title: Azure SignalR Service 中的訊息和連線
description: 概略說明與 Azure SignalR Service 中的訊息和連線有關的重要概念。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 5483e10e817ce8a0a7e7c82d817b7bdbbdd9176b
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853444"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Azure SignalR Service 中的訊息和連線

Azure SignalR Service 的計費模型是以連線數目和訊息數目為基礎。 本文說明如何定義及計算訊息和連線，以供計費。


## <a name="message-formats"></a>訊息格式 

Azure SignalR Service 支援與 ASP.NET Core SignalR： [JSON](https://www.json.org/)和[MessagePack](/aspnet/core/signalr/messagepackhubprotocol)相同的格式。

## <a name="message-size"></a>訊息大小

Azure SignalR Service 沒有訊息大小限制。

大型訊息會分割成各自不超過 2 KB 的較小訊息，且個別地傳輸。 SDK 會處理訊息的分割和組合。 開發人員不需要投入這方面的人力。

訊息過大會對傳訊效能造成負面影響。 請盡可能使用較小的訊息，並經由測試來決定每個使用案例的的最佳訊息大小。

## <a name="how-messages-are-counted-for-billing"></a>如何計算訊息以供計費

為了計費，只會計算僅來自 Azure SignalR Service 的輸出訊息。 用戶端與伺服器之間的 Ping 訊息會被忽略。

系統會將大於 2 KB 的訊息計算為多則 2 KB 的訊息。 Azure 入口網站中的訊息計數圖表會在每個中樞達到 100 則訊息時更新一次。

例如，假設您有一個應用程式伺服器和三個用戶端：

應用程式伺服器會將 1 KB 的訊息廣播給所有已連線的用戶端，從應用程式伺服器到服務的訊息會被視為免費的輸入訊息。 只有三個從服務傳送到每個用戶端的訊息，才會以輸出訊息的形式計費。

用戶端 A 會傳送 1 KB 的訊息給另一個用戶端 B，而不會通過應用程式伺服器。 從用戶端 A 到服務的訊息是免費的輸入訊息。 從服務到用戶端 B 的訊息會以輸出訊息的形式計費。

如果您有三個用戶端和一個應用程式伺服器。 一個用戶端傳送了一則 4 KB 的訊息，讓伺服器廣播到所有用戶端。 計費的訊息計數為8：從服務到應用程式伺服器的一則訊息，以及從服務到用戶端的三個訊息。 每則訊息分別計為兩則 2 KB 的訊息。

## <a name="how-connections-are-counted"></a>如何計算連線

有 Azure SignalR Service 的伺服器連接和用戶端連線。 根據預設，每個應用程式伺服器一開始會有五個中樞的初始連線，而且每個用戶端都有一個用戶端連接。

Azure 入口網站中顯示的連線計數包括伺服器連線和用戶端連線。

例如，假設您有兩個應用程式伺服器，而且您在程式碼中定義了五個中樞。 伺服器連接計數將是50：2個應用程式伺服器 * 5 個中樞 * 5 個連線。

ASP.NET SignalR 會以不同的方式計算伺服器連線。 除了您定義的中樞以外，它還包含一個預設中樞。 根據預設，每個應用程式伺服器需要5個以上的初始伺服器連接。 預設中樞的初始連接計數會與其他中樞保持一致。

服務和應用程式伺服器會持續同步線上狀態，並調整伺服器連接，以取得更佳的效能和服務穩定性。  因此，您可能會看到伺服器連接編號變更的時間。

## <a name="how-inboundoutbound-traffic-is-counted"></a>如何計算輸入/輸出流量

傳送至服務的訊息是輸入訊息。 從服務送出的訊息是輸出訊息。 流量會以位元組為單位計算。

## <a name="related-resources"></a>相關資源

- [Azure 監視器中的彙總類型](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR 組態](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
