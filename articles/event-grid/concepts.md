---
title: Azure Event Grid 概念
description: 說明 Azure Event Grid 與其概念。 定義 Event Grid 的數個重要元件。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 348d82f704b89b97e11a09b8f88e92831901b3bf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393469"
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid 中的概念

本文說明 Azure 事件方格中的主要概念。

## <a name="events"></a>事件

事件是完整說明系統中發生內容的最小量資訊。 每個事件都有一般資訊，例如：事件來源、事件發生的時間，以及唯一識別碼。 每個事件也有只與特定事件類型相關的特定資訊。 例如，在 Azure 儲存體中建立新檔案的相關事件含有該檔案的相關詳細資料，例如 `lastTimeModified` 值。 或者，事件中樞事件含有「擷取」檔案的 URL。 

通用可用性 (GA) 服務等級協定 (SLA) 涵蓋大小高達 64 KB 的事件。 當前預覽了對大小為 1 MB 的事件的支援。 超過64 KB的事件以64 KB的增量收費。 


關於事件中傳送的屬性，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="publishers"></a>發行者

發行者是決定將事件傳送至 Event Grid 的使用者或組織。 Microsoft 會發行數個 Azure 服務的事件。 您可以從您自己的應用程式發行事件。 裝載 Azure 外部服務的組織可以透過 Event Grid 發行事件。

## <a name="event-sources"></a>事件來源

事件來源是事件發生的地點。 每個事件來源都與一或多個事件類型相關。 例如，Azure 儲存體是 Blob 建立事件的事件來源。 IoT 中樞是裝置建立事件的事件來源。 您的應用程式是您定義之自訂事件本身的事件來源。 事件來源負責將事件傳送至 Event Grid。

如需與實作任何支援的事件方格來源有關的資訊，請參閱 [Azure 事件方格中的事件來源](overview.md#event-sources)。

## <a name="topics"></a>主題

Event Grid 主題提供來源傳送事件的端點。 發行者會建立 Event Grid 主題，並決定事件來源是否需要一個主題或多個主題。 主題可用於相關事件的集合。 若要回應某些類型的事件，訂閱者會決定要訂閱的主題。

系統主題是由 Azure 服務提供的內建主題。 您不會在您的 Azure 訂用帳戶中看見系統主題，因為發行者擁有主題，但是您可以訂閱這些主題。 若要訂閱，您可以提供與您想要接收事件的資源有關的資訊。 只要您可存取資源，就可以訂閱其事件。

自訂主題是應用程式和協力廠商的主題。 您建立或指派自訂主題的存取時，會在您的訂用帳戶中看見該自訂主題。

在設計應用程式時，您將有足夠的彈性可決定要建立多少個主題。 對於大型解決方案，請為每個類別的相關事件建立一個自訂主題。 例如，請考慮使用應用程式來傳送與修改使用者帳戶和處理訂單有關的事件。 任何事件處理常式不太需要兩種類別的事件。 建立兩個自訂主題，並讓事件處理常式訂閱其感興趣的自訂主題。 對於小型解決方案，您可能會想要將所有事件傳送至單一主題。 事件訂閱者可以篩選出他們想要的事件類型。

## <a name="event-subscriptions"></a>事件訂閱

訂閱會告知事件方格您有興趣接收的主題事件。 您建立訂閱時，可提供處理事件的端點。 您可以篩選傳送至端點的事件。 您可以依事件類型或主旨模式進行篩選。 如需詳細資訊，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。

如需建立訂閱的範例，請參閱：

* [Event Grid 的 Azure CLI 範例](cli-samples.md)
* [Event Grid 的 Azure PowerShell 範例](powershell-samples.md)
* [Event Grid 的 Azure Resource Manager 範本](template-samples.md)

如需取得目前 Event Grid 訂用帳戶的資訊，請參閱[查詢 Event Grid 訂用帳戶](query-event-subscriptions.md)。

## <a name="event-subscription-expiration"></a>事件訂閱過期
事件訂閱會在該日期之後自動過期。 針對只在限定期間內需要的事件訂閱設定到期，而您不需擔心如何清理那些訂閱。 例如，建立事件訂閱來測試案例時，您可能想要設定到期。 

如需設定到期的範例，請參閱[搭配進階篩選進行訂閱](how-to-filter-events.md#subscribe-with-advanced-filters)。

## <a name="event-handlers"></a>事件處理常式

從 Event Grid 的觀點而言，事件處理常式是傳送事件的位置。 處理常式會採取一些進一步的動作來處理事件。 事件方格支援數個處理常式類型。 您可以使用支援的 Azure 服務或您自己的 Webhook 作為處理常式。 視處理常式類型而定，Event Grid 依照不同的機制來保證事件的傳遞。 對於 HTTP Webhook 事件處理常式，事件會重試到處理常式傳回 `200 – OK` 的狀態碼為止。 對於 Azure 儲存體佇列，系統會重試事件，直到佇列服務成功處理將訊息推送到佇列為止。

如需與實作任何支援的事件方格處理常式有關的資訊，請參閱 [Azure 事件方格中的事件處理常式](event-handlers.md)。

## <a name="security"></a>安全性

Event Grid 提供訂閱主題和發佈主題的安全性。 訂閱時，您必須具有資源或事件方格主題的適當權限。 發佈時，您必須具有該主題的 SAS 權杖或金鑰驗證。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](security-authentication.md)。

## <a name="event-delivery"></a>事件傳遞

如果事件方格無法確認訂閱者端點是否收到事件，則會重新傳遞事件。 如需詳細資訊，請參閱 [Event Grid 訊息傳遞與重試](delivery-and-retry.md)。

## <a name="batching"></a>批次處理

在使用自訂主題時，事件必須一律發佈在陣列中。 在低輸送量的案例中，這可以是單一批次，但在大量使用案例中，建議您在每次發佈時一併批次處理數個事件，以達到更高的效率。 批次最多可達 1 MB。 每個事件仍不應大於 64 KB(常規可用性)或 1 MB(預覽)。

> [!NOTE]
> 通用可用性 (GA) 服務等級協定 (SLA) 涵蓋大小高達 64 KB 的事件。 當前預覽了對大小為 1 MB 的事件的支援。 超過64 KB的事件以64 KB的增量收費。 

## <a name="next-steps"></a>後續步驟

* 有關事件網格的簡介,請參閱[有關事件網格](overview.md)。
* 若要快速地開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。
