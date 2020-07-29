---
title: Azure 服務匯流排節流的總覽 |Microsoft Docs
description: 服務匯流排節流的總覽-標準層和進階層。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340948"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure 服務匯流排上的節流作業

雲端原生解決方案提供無限制資源的概念，可隨您的工作負載調整。 雖然在雲端中，這種概念比內部部署系統更是如此，但仍存在於雲端中的限制。

這些限制可能會導致標準和高階層級的用戶端應用程式要求節流，如本文所述。 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure 服務匯流排標準層中的節流

Azure 服務匯流排標準層會以隨用隨付定價模型的多租使用者設定運作。 在相同叢集中的多個命名空間會共用配置的資源。 標準層是適用于開發人員、測試和 QA 環境的建議選項，以及低輸送量生產系統。

在過去，Azure 服務匯流排具有粗略的節流限制，嚴格相依于資源使用率。 不過，有機會改善節流邏輯，並為共用這些資源的所有命名空間提供可預測的節流行為。

若要嘗試確保在使用相同資源的所有 Azure 服務匯流排標準命名空間中公平使用和散發資源，節流邏輯已經修改為以信用額度為基礎。

> [!NOTE]
> 請務必注意，節流不是 Azure 服務匯流排或任何雲端原生服務的***新***功能。
>
> 以信用額度為基礎的節流只是精簡各種命名空間在多租使用者標準層環境中共用資源的方式，因此可讓共用資源的所有命名空間公平使用。

### <a name="what-is-credit-based-throttling"></a>什麼是以信用額度為基礎的節流？

以信用額度為基礎的節流會限制在特定時間週期內，指定命名空間上可執行檔作業數目。 

以下是以信用額度為基礎之節流的工作流程- 

  * 在每個時間週期開始時，我們會為每個命名空間提供一定數目的信用額度。
  * 傳送者或接收者用戶端應用程式所執行的任何作業，都會根據這些信用額度來計算（並從可用的信用額度減去）。
  * 如果點數已耗盡，後續作業將會受到節流，直到下一段時間開始為止。
  * 信用額度會在下一段時間開始補充。

### <a name="what-are-the-credit-limits"></a>信用限制有哪些？

點數限制目前設定為每秒的 ' 1000 ' 信用額度（每個命名空間）。

並非所有作業都是以相等的建立。 以下是每項作業的信用成本- 

| 操作 | 信用成本|
|-----------|-----------|
| 資料作業（Send、SendAsync、Receive、ReceiveAsync、Peek） |每個訊息1個信用額度 |
| 管理作業（建立、讀取、更新、刪除佇列、主題、訂用帳戶、篩選） | 10信用額度 |

> [!NOTE]
> 請注意，傳送至主題時，每個訊息都會針對篩選準則進行評估，然後才在訂用帳戶上提供。
> 每個篩選準則評估也會計算點數限制（也就是每個篩選評估1個信用額度）。
>

### <a name="how-will-i-know-that-im-being-throttled"></a>我要如何知道我正在進行節流？

當用戶端應用程式要求受到節流處理時，您的應用程式會收到下列伺服器回應並加以記錄。

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>如何避免受到節流？

使用共用資源時，請務必在共用這些資源的各種服務匯流排命名空間中強制執行某種公平的使用方式。 節流可確保單一工作負載中的任何尖峰都不會造成相同資源上的其他工作負載受到節流。

如本文稍後所述，由於用戶端 Sdk （和其他 Azure PaaS 供應專案）已內建預設的重試原則，因此不會有節流的風險。 任何節流的要求都會以指數輪詢重試，而且最終會在補充信用額度時通過。

更，有些應用程式可能會受到節流的影響。 在此情況下，建議您將[目前的服務匯流排標準命名空間遷移至 Premium](service-bus-migrate-standard-premium.md)。 

在遷移時，您可以將專用資源配置給服務匯流排命名空間，並在工作負載尖峰時適當地相應增加資源，並降低節流的可能性。 此外，當您的工作負載縮減為一般層級時，您可以相應減少配置給命名空間的資源。

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure 服務匯流排 Premium 層中的節流

[Azure 服務匯流排](service-bus-premium-messaging.md)進階層會根據訊息單位，將專用資源配置給客戶所設定的每個命名空間。 這些專用資源提供可預測的輸送量和延遲，並建議用於高輸送量或敏感的生產等級系統。

此外，高階層也可讓客戶在工作負載尖峰時，相應增加輸送量容量。

### <a name="how-does-throttling-work-in-service-bus-premium"></a>服務匯流排 Premium 中的節流如何運作？

針對服務匯流排 Premium 的專屬資源配置，節流只會受到配置給命名空間之資源的限制所驅動。

如果要求數目超過目前的資源可以服務，則會對要求進行節流。

### <a name="how-will-i-know-that-im-being-throttled"></a>我要如何知道我正在進行節流？

有各種方式可以在 Azure 服務匯流排 Premium 中識別節流- 
  * **節流要求**會顯示在[Azure 監視器要求計量](service-bus-metrics-azure-monitor.md#request-metrics)上，以識別已節流的要求數目。
  * [高**CPU 使用量**] 表示目前的資源配置很高，而且如果目前的工作負載不會降低，要求可能會受到節流處理。
  * 高**記憶體使用量**指出目前的資源配置很高，如果目前的工作負載不會降低，要求可能會受到節流。

### <a name="how-can-i-avoid-being-throttled"></a>如何避免受到節流？

由於服務匯流排 Premium 命名空間已經有專用資源，因此您可以在工作負載的事件（或預期）中相應增加配置給命名空間的訊息單位數，藉以降低節流的可能性。

藉由建立可由上述計量中的變更觸發的[runbook](../automation/automation-create-alert-triggered-runbook.md) ，即可達到相應增加/減少的規模。

## <a name="faqs"></a>常見問題集

### <a name="how-does-throttling-affect-my-application"></a>節流如何影響我的應用程式？

當要求受到節流處理時，即表示服務忙碌中，因為它所面臨的要求比資源所允許的還要多。 如果在幾分鐘後再次嘗試相同的作業，則一旦服務已完成其目前的工作負載，就可以接受要求。

因為節流是任何雲端原生服務的預期行為，所以我們已在 Azure 服務匯流排 SDK 本身內建立重試邏輯。 預設值是設定為 [自動以指數輪詢重試]，以確保每次都不會對相同的要求進行節流。

預設的重試邏輯會套用至每個作業。

### <a name="does-throttling-result-in-data-loss"></a>節流會導致資料遺失嗎？

Azure 服務匯流排已針對持續性優化，我們確保傳送至服務匯流排的所有資料在服務確認要求成功之前都會認可至儲存體。

一旦服務匯流排成功「ACK」（認可），表示服務匯流排已成功處理要求。 如果服務匯流排傳回 ' NACK ' （失敗），則表示服務匯流排無法處理要求，而且用戶端應用程式必須重試要求。

不過，當要求受到節流處理時，服務表示它現在無法接受並處理要求，因為資源的限制。 這**並不**表示資料遺失，因為服務匯流排只是看不到要求。 在此情況下，依賴服務匯流排 SDK 的預設重試原則可確保最終會處理要求。

## <a name="next-steps"></a>後續步驟

如需使用服務匯流排傳訊的詳細資訊和範例，請參閱下列進階主題：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [快速入門：使用 Azure 入口網站和 .NET 傳送和接收訊息](service-bus-quickstart-portal.md)
* [教學課程：使用 Azure 入口網站和主題/訂用帳戶來更新庫存](service-bus-tutorial-topics-subscriptions-portal.md)

