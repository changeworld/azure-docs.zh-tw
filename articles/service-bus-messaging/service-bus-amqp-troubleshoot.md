---
title: 在 Azure 服務匯流排中排除 AMQP 錯誤 |微軟文檔
description: 提供使用 Azure 服務匯流排時可能收到的 AMQP 錯誤的清單，以及這些錯誤的原因。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402780"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Azure 服務匯流排中的 AMQP 錯誤
本文提供了在將 AMQP 與 Azure 服務匯流排一起使用時收到的一些錯誤。 它們都是服務的標準行為。 您可以通過在連接/連結上發出發送/接收呼叫來避免它們，這會自動重新創建連接/連結。

## <a name="link-is-closed"></a>連結已關閉 
當 AMQP 連接和連結處於活動狀態，但 10 分鐘內沒有使用該連結發出呼叫（例如，發送或接收），您將看到以下錯誤。 因此，連結已關閉。 連接仍處於打開狀態。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>連接已關閉
當連接中的所有連結都已關閉，因為沒有活動（空閒）並且 5 分鐘內未創建新連結時，您會看到 AMQP 連接上的以下錯誤。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>未創建連結 
創建新的 AMQP 連接，但在創建 AMQP 連接後的 1 分鐘內未創建連結時，您將看到此錯誤。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>後續步驟

若要深入了解 AMQP 和服務匯流排，請造訪下列連結：

* [服務匯流排 AMQP 概觀]
* [AMQP 1.0 通訊協定指南]
* [Windows Server 服務匯流排中的 AMQP]

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[AMQP 1.0 通訊協定指南]: service-bus-amqp-protocol-guide.md
[Windows Server 服務匯流排中的 AMQP]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
