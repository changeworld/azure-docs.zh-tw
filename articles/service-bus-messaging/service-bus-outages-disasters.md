---
title: 將 Azure 服務匯流排應用程式與中斷和災難隔離
description: 這篇文章提供保護應用程式免于潛在 Azure 服務匯流排中斷的技術。
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 29eb0625ceebf4fee75d0c1accef7ae03b5f61b9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208375"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>將應用程式與服務匯流排中斷和災難隔絕的最佳做法

關鍵任務應用程式必須持續作業，即使發生非預期的中斷或災害亦然。 本文說明您可用來保護服務匯流排應用程式，避免潛在服務中斷或災害發生的技巧。

中斷的定義是暫時無法使用 Azure 服務匯流排。 中斷可能會影響服務匯流排的否些元件，例如訊息存放區或甚至整個資料中心。 修正問題之後，服務匯流排可再次使用。 中斷通常不會導致訊息或其他資料遺失。 元件失敗的範例是特定的訊息存放區無法使用。 資料中心全面中斷的範例是資料中心電源中斷、或錯誤的資料中心網路交換器。 中斷可能持續數分鐘到數天。

災害的定義是服務匯流排縮放單位或資料中心永久遺失。 資料中心不一定能再次使用。 災害通常會造成部分或所有訊息或其他資料遺失。 災害的範例包括火災、水災或地震。

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>防止中斷與災害 - 服務匯流排進階層
「高可用性」和「災害復原」概念已直接內建於「Azure 服務匯流排進階層」中，不論是相同區域內 (透過「可用性區域」) 還是跨不同區域 (透過「異地災害復原」) 都適用。

### <a name="geo-disaster-recovery"></a>異地災害復原

「服務匯流排進階層」支援命名空間層級的異地災害復原。 如需詳細資訊，請參閱[Azure 服務匯流排異地](service-bus-geo-dr.md)嚴重損壞修復。 災害復原功能僅適用於[進階 SKU](service-bus-premium-messaging.md)，其會實作中繼資料災害復原，並依賴主要和次要災害復原命名空間。

### <a name="availability-zones"></a>可用性區域

「服務匯流排進階 SKU」支援[可用性區域](../availability-zones/az-overview.md)，可在 Azure 區域內提供錯誤隔離位置。 服務匯流排管理訊息存放區的三個複本（1個主要和2個次要）。 服務匯流排會將三個複本保持同步，以進行資料和管理作業。 如果主要複本失敗，其中一個次要複本會升級為主要複本，而不會察覺到停機時間。 如果應用程式看到暫時性中斷與服務匯流排的連線，SDK 中的重試邏輯會自動重新連線到服務匯流排。 

> [!NOTE]
> 「Azure 服務匯流排進階層」的「可用性區域」支援僅適用於有可用性區域存在的 [Azure 區域](../availability-zones/az-region.md)。

使用 Azure 入口網站時，只能在新的命名空間上啟用可用性區域。 服務匯流排不支援移轉現有的命名空間。 在命名空間上啟用區域備援之後，便無法停用。

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>防止中斷與災害 - 服務匯流排標準層
使用標準傳訊定價層時，為了達成對資料中心中斷情況的恢復能力，「服務匯流排」支援兩種方法：「主動」** 和「被動」** 複寫。 無論使用哪個方法，如果特定佇列或主題必須在資料中心發生中斷時保持可存取狀態，您可以在這兩個命名空間建立該佇列或主題。 這兩個實體可以具有相同的名稱。 例如，可在 **contosoPrimary.servicebus.windows.net/myQueue** 下找到主要佇列，並在 **contosoSecondary.servicebus.windows.net/myQueue** 下找到其次要的對應項目。

>[!NOTE]
> **主動複寫**和**被動複寫**設定是一般用途解決方案，不是「服務匯流排」的特定功能。 複寫邏輯 (傳送至 2 個不同的命名空間) 存在於傳送者應用程式上，而接收者必須有自訂邏輯來偵測重複項。

如果應用程式不需要永久的傳送者至接收者通訊，應用程式可以實作持續的用戶端佇列，以防止訊息遺失並避免傳送者發生任何暫時性服務匯流排錯誤。

### <a name="active-replication"></a>主動複寫
主動複寫會針對每個作業使用兩個命名空間中的實體。 傳送訊息的任何用戶端會傳送相同訊息的兩個複本。 第一個複本傳送至主要實體 (例如，**contosoPrimary.servicebus.windows.net/sales**)，而訊息的第二個複本傳送至次要實體 (例如，**contosoSecondary.servicebus.windows.net/sales**)。

用戶端會從這兩個佇列接收訊息。 接收者會處理訊息的第一個複本，並隱藏第二個複本。 若要隱藏重複的訊息，傳送者必須利用唯一的識別碼標記每個訊息。 訊息的兩個複本必須以相同的識別碼標記。 您可以使用 [BrokeredMessage.MessageId][BrokeredMessage.MessageId]、[BrokeredMessage.Label][BrokeredMessage.Label] 屬性或自訂屬性來標記訊息。 接收者必須維護其已收到的訊息清單。

[搭配服務匯流排標準層的異地複寫][Geo-replication with Service Bus Standard Tier] \(英文\) 範例示範傳訊實體的主動複寫。

> [!NOTE]
> 主動複寫方法會讓作業數目加倍，因此這個方法會導致更高的成本。
> 
> 

### <a name="passive-replication"></a>被動複寫
在沒有錯誤的情況下，被動複寫只會使用兩個訊息實體的其中之一。 用戶端會傳送訊息至作用中的實體。 如果作用中實體上的作業失敗，並出現錯誤碼指出裝載作用中實體的資料中心可能會無法使用，用戶端會傳送訊息的複本至備份實體。 作用中和備份實體會在這一點交換角色：傳送用戶端會將舊的使用中實體視為新的備份實體，而將舊的備份實體視為新的作用中實體。 如果這兩個傳送作業都失敗，兩個實體的角色會保持不變並傳回錯誤。

用戶端會從這兩個佇列接收訊息。 因為接收者可能會收到相同訊息的兩個複本，所以接收者必須隱藏重複的訊息。 您可以主動複寫所描述的相同方式隱藏重複訊息。

一般而言，被動複寫比主動複寫更具經濟效益，因為大部分的情形只會執行一項作業。 延遲、輸送量和成本與非複寫案例相同。

使用被動複寫時，訊息可能會在下列案例中遺失或收到兩次：

* **訊息延遲或遺失**：假設傳送者成功傳送訊息 m1 至主要佇列，佇列在接收者收到 m1 之前無法使用。 傳送者會將後續的訊息 m2 傳送至次要佇列。 如果主要佇列暫時無法使用，接收者會在佇列可再次使用之後收到 m1。 若發生災害，接收者可能永遠無法收到 m1。
* **重複接收**：假設傳送者傳送訊息 m 到主要佇列。 服務匯流排已成功處理 m 但無法傳送回應。 傳送作業逾時之後，傳送者會將 m 的相同複本傳送至次要佇列。 如果接收者可以在主要佇列無法使用之前收到 m 的第一個複本，接收者會幾乎同時接收 m 的兩個複本。 如果接收者無法在主要佇列無法使用之前收到 m 的第一個複本，接收者一開始只會收到 m 的第二個複本，但會接著在主要佇列可以使用時會收到 m 的第二個複本。

[搭配服務匯流排標準層的異地複寫][Geo-replication with Service Bus Standard Tier] \(英文\) 範例示範傳訊實體的被動複寫。

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>保護轉送端點免於發生資料中心中斷或災害
[Azure 轉送](../service-bus-relay/relay-what-is-it.md)端點的異地複寫可讓公開轉送端點的服務在發生服務匯流排中斷時可連線。 若要達到異地複寫，服務必須在不同的命名空間中建立兩個轉送端點。 命名空間必須位於不同的資料中心而兩個端點必須具有不同的名稱。 例如，可在 **contosoPrimary.servicebus.windows.net/myPrimaryService** 下找到主要端點，並在 **contosoSecondary.servicebus.windows.net/mySecondaryService** 下找到其次要的對應項目。

然後服務會接聽這兩個端點，且用戶端可以透過任一端點叫用服務。 用戶端應用程式會隨機挑選其中一個轉送做為主要端點，並將其要求傳送至作用中的端點。 如果作業失敗並出現錯誤代碼，此失敗代表轉送端點無法使用。 應用程式會開啟備份端點的通道並重新發出要求。 作用中和備份端點會在這一點交換角色：用戶端應用程式會將舊的使用中端點視為新的備份端點，而將舊的備份端點視為新的作用中端點。 如果這兩個傳送作業都失敗，兩個實體的角色會保持不變並傳回錯誤。

## <a name="next-steps"></a>後續步驟
若要深入了解災害復原，請參閱這些文章：

* [Azure 服務匯流排地理災害復原](service-bus-geo-dr.md)
* [Azure SQL Database 商務持續性][Azure SQL Database Business Continuity]
* [為 Azure 設計有彈性的應用程式][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
