---
title: 疑難排解指南-Azure 事件中樞 |Microsoft Docs
description: 本文提供 Azure 事件中樞傳訊例外狀況和建議的動作清單。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124664"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure 事件中樞-疑難排解指南
本文提供使用 Azure EventHubs 時，您可能會看到的幾個問題的疑難排解秘訣和建議。

## <a name="connectivity-certificate-or-timeout-issues"></a>連線、憑證或超時問題
下列步驟可協助您針對 *. servicebus.windows.net 下所有服務的連線/憑證/超時問題進行疑難排解。 

- 流覽至或[wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` 。 它有助於檢查您是否有 IP 篩選或虛擬網路或憑證鏈問題（最常見的情況是使用 java SDK）。

    成功訊息的範例：
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    失敗錯誤訊息的範例：

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 執行下列命令，以檢查防火牆上是否有任何埠遭到封鎖。 使用的埠為443（HTTPS）、5671（AMQP）和9093（Kafka）。 視您使用的程式庫而定，也會使用其他埠。 以下是檢查5671埠是否已封鎖的範例命令。

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    在 Linux 上：

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 當發生間歇連線問題時，請執行下列命令來檢查是否有任何已丟棄的封包。 此命令會嘗試使用服務每隔1秒建立25個不同的 TCP 連線。 然後，您可以檢查其中有多少個成功/失敗，同時查看 TCP 連接延遲。 您可以 `psping` 從[這裡](/sysinternals/downloads/psping)下載此工具。

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    如果您使用其他工具（例如、等等），可以使用對等的命令 `tnc` `ping` 。 
- 如果先前的步驟沒有使用[Wireshark](https://www.wireshark.org/)之類的工具來協助並加以分析，請取得網路追蹤。 如有需要，請聯絡[Microsoft 支援服務](https://support.microsoft.com/)。 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>服務升級/重新開機時可能發生的問題
後端服務升級和重新開機，可能會對您的應用程式造成下列影響：

- 要求可能會短暫地進行節流。
- 傳入訊息/要求中可能會有一個下降。
- 記錄檔可能包含錯誤訊息。
- 應用程式可能會在幾秒內中斷與服務的連線。

如果應用程式代碼利用 SDK，則重試原則已經內建且作用中。 應用程式會重新連線，而不會對應用程式/工作流程造成重大影響。


## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](event-hubs-what-is-event-hubs.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)
