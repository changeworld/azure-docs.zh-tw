---
title: 針對 Azure 服務匯流排中的 AMQP 錯誤進行疑難排解 |Microsoft Docs
description: 提供當您使用 Azure 服務匯流排時可能會收到的 AMQP 錯誤清單，以及這些錯誤的原因。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "60402780"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP Azure 服務匯流排中的錯誤
本文提供使用 AMQP 搭配 Azure 服務匯流排時所收到的一些錯誤。 這些全都是服務的標準行為。 您可以在連線/連結上發出傳送/接收呼叫，這會自動重新建立連線/連結，以避免這些問題。

## <a name="link-is-closed"></a>連結已關閉 
當 AMQP 連線和連結作用中，但沒有使用連結10分鐘的呼叫（例如，傳送或接收）時，您會看到下列錯誤。 因此，連結已關閉。 連接仍為開啟狀態。

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>連接已關閉
當連接中的所有連結都已關閉，因為沒有活動（閒置），而且未在5分鐘內建立新的連結時，您會在 AMQP 連接上看到下列錯誤。

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>未建立連結 
建立新的 AMQP 連線時，您會看到此錯誤，但不會在建立 AMQP 連線的1分鐘內建立連結。

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
