---
title: Azure 服務匯流排限制概述 |微軟文檔
description: 服務匯流排限制概述 - 標準和高級層。
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598284"
---
# <a name="throttling-operations-on-azure-service-bus"></a>在 Azure 服務匯流排上限制操作

雲原生解決方案提供了可隨工作負載擴展的無限資源的概念。 雖然這種概念在雲中比使用本地系統更真實，但雲中仍然存在一些限制。

這些限制可能會導致標準層和高級層中的用戶端應用程式請求限制，如本文所述。 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure 服務匯流排標準層中的限制

Azure 服務匯流排標準層作為多租戶設置運行，具有即用即付定價模型。 在這裡，同一群集中的多個命名空間共用分配的資源。 標準層是開發人員、測試和 QA 環境以及低輸送量生產系統的首選。

過去，Azure 服務匯流排具有嚴格依賴于資源利用率的粗限制限制。 但是，有機會優化限制邏輯，並將可預測的限制行為提供給共用這些資源的所有命名空間。

為了確保資源在使用相同資源的所有 Azure 服務匯流排標準命名空間中合理使用和分配，限制邏輯已修改為基於信用。

> [!NOTE]
> 請務必注意，限制對於 Azure 服務匯流排或任何雲本機服務***來說並不新鮮***。
>
> 基於信用的限制只是改進各種命名空間在多租戶標準層環境中共用資源的方式，從而實現共用資源的所有命名空間的公平使用。

### <a name="what-is-credit-based-throttling"></a>什麼是基於信用的限制？

基於信用的限制限制限制在特定時間段內可以在給定命名空間上執行的運算元。 

下面是基於信用的限制工作流 - 

  * 在每個時間段的開始，我們為每個命名空間提供一定數量的配額。
  * 發送方或接收方用戶端應用程式執行的任何操作都將計入這些積分（並從可用積分中減去）。
  * 如果積分耗盡，後續操作將限制到下一個時間段的開始。
  * 積分在下一個時間段開始時補充。

### <a name="what-are-the-credit-limits"></a>信用額度是多少？

信用額度當前設置為每秒"1000"學分（每個命名空間）。

並非所有操作都創建相等。 以下是每個操作的信用成本 - 

| 作業 | 信貸成本|
|-----------|-----------|
| 資料操作（發送、發送同步、接收、接收同步、透視） |每條消息 1 個積分 |
| 管理操作（創建、讀取、更新、刪除佇列、主題、訂閱、篩選器） | 10 學分 |

> [!NOTE]
> 請注意，在發送到主題時，在訂閱上提供之前，會根據篩選器對每條消息進行評估。
> 每個濾波器評估還計入信用額度（即每個篩選器評估 1 個貸項）。
>

### <a name="how-will-i-know-that-im-being-throttled"></a>我怎麼知道我被限制了？

當用戶端應用程式請求被限制時，應用程式將接收並記錄以下伺服器回應。

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被限制？

使用共用資源時，在共用這些資源的各種服務匯流排命名空間中強制實施某種公平使用非常重要。 限制可確保單個工作負荷中的任何峰值不會導致同一資源上的其他工作負載受到限制。

如本文後面所述，由於用戶端 SDK（和其他 Azure PaaS 產品）內置了預設重試策略，因此沒有被限制的風險。 任何節流請求都將使用指數回退重試，並最終在補充積分時完成。

可以理解的是，某些應用程式可能對被限制敏感。 在這種情況下，建議將[當前服務匯流排標準命名空間遷移到高級](service-bus-migrate-standard-premium.md)。 

在遷移時，您可以將專用資源配置給服務匯流排命名空間，並在工作負荷出現峰值時適當擴展資源，並減少被限制的可能性。 此外，當工作負荷減少到正常級別時，可以縮減分配給命名空間的資源。

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure 服務匯流排高級層中的限制

[Azure 服務匯流排高級](service-bus-premium-messaging.md)層將專用資源（按消息傳遞單位）分配給客戶設置的每個命名空間。 這些專用資源提供可預測的輸送量和延遲，建議用於高輸送量或敏感生產級系統。

此外，高級層還使客戶在遇到工作負載高峰時能夠擴展其輸送量容量。

### <a name="how-does-throttling-work-in-service-bus-premium"></a>在服務匯流排高級版中限制如何工作？

使用服務匯流排高級版的獨家資源配置時，限制純粹是受分配給命名空間的資源的限制驅動的。

如果請求數超過當前資源可以提供的服務，則將限制請求。

### <a name="how-will-i-know-that-im-being-throttled"></a>我怎麼知道我被限制了？

在 Azure 服務匯流排高級版中識別限制的方法有多種 - 
  * **受限請求**顯示在[Azure 監視器請求指標](service-bus-metrics-azure-monitor.md#request-metrics)上，以標識有多少請求被限制。
  * **CPU 使用率**高表示當前資源配置較高，如果當前工作負載不減少，請求可能會受到限制。
  * **高記憶體使用**率表示當前資源配置較高，如果當前工作負載不減少，請求可能會受到限制。

### <a name="how-can-i-avoid-being-throttled"></a>如何避免被限制？

由於服務匯流排高級命名空間已具有專用資源，因此可以通過在工作負載高峰事件（或預期）中擴展分配給命名空間的消息單元數來降低被限制的可能性。

通過創建可由上述指標中的更改觸發[的 Runbook，](../automation/automation-create-alert-triggered-runbook.md)可以向上/向下擴展。

## <a name="faqs"></a>常見問題集

### <a name="how-does-throttling-affect-my-application"></a>限制如何影響我的應用程式？

當請求被限制時，這意味著服務處於繁忙，因為它面對的請求比資源允許的請求多。 如果幾分鐘後再次嘗試相同的操作，則一旦服務完成其當前工作負載，則可以遵守請求。

由於限制是任何雲本機服務的預期行為，因此我們已將重試邏輯構建到 Azure 服務匯流排 SDK 本身中。 預設值設置為使用指數回退自動重試，以確保我們每次沒有使用相同的請求被限制。

預設重試邏輯將應用於每個操作。

### <a name="does-throttling-result-in-data-loss"></a>限制是否導致資料丟失？

Azure 服務匯流排針對持久性進行了優化，我們確保發送到服務匯流排的所有資料在服務確認請求成功之前都承諾存儲。

一旦服務匯流排成功"確認"（確認）請求，則意味著服務匯流排已成功處理該請求。 如果服務匯流排返回"NACK"（失敗），則意味著服務匯流排無法處理請求，並且用戶端應用程式必須重試請求。

但是，當請求受到限制時，服務意味著由於資源限制，它現在無法接受和處理請求。 **這並不意味著**任何類型的資料丟失，因為服務匯流排根本沒有查看請求。 在這種情況下，依賴服務匯流排 SDK 的預設重試策略可確保最終處理請求。

## <a name="next-steps"></a>後續步驟

如需使用服務匯流排傳訊的詳細資訊和範例，請參閱下列進階主題：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [快速入門：使用 Azure 入口網站和 .NET 傳送和接收訊息](service-bus-quickstart-portal.md)
* [教學課程：使用 Azure 入口網站和主題/訂用帳戶來更新庫存](service-bus-tutorial-topics-subscriptions-portal.md)

