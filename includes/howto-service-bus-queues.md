---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: d3d33c87dc1adf65a53b71cc4c833e7f4a191670
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "67174219"
---
## <a name="what-are-service-bus-queues"></a>什麼是服務匯流排佇列？
服務匯流排佇列支援 **代理傳訊** 通訊模型。 使用佇列時，分散式應用程式的元件彼此不直接通訊，相反的，他們會透過扮演中繼角色 (代理人) 的佇列來交換訊息。 訊息產生者 (傳送者) 會將訊息遞交給佇列，然後繼續其處理工作。 訊息取用者 (接收者) 非同步地從佇列中提取訊息並處理。 產生者不必等待取用者的回覆，即可繼續處理及傳送其他訊息。 如果有一或多個競爭取用者，佇列會採取 **先進先出 (FIFO)** 訊息傳遞機制。 亦即，通常由接收者依訊息加入佇列的順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。

![佇列概念](./media/howto-service-bus-queues/sb-queues-08.png)

服務匯流排佇列為適用於各種情況的通用技術：

* 多層式 Azure 應用程式中 Web 角色和背景工作角色之間的通訊。
* 混合式解決方案中的內部部署應用程式和 Azure 代管應用程式之間的通訊。
* 在不同組織或同一組織的不同部門中，在內部部署執行之分散式應用程式的各元件之間的通訊。

使用佇列可讓應用程式更容易地進一步延展，提高架構的備援能力。


