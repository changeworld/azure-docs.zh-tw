---
title: 什麼是 Azure 事件方格？
description: 使用 Azure 事件方格，將事件資料從來源傳送至處理常式。 建置事件型應用程式，並與 Azure 服務整合。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 02/20/2020
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 75ee828c659aba895f65d0cf14070966428a1d19
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682500"
---
# <a name="what-is-azure-event-grid"></a>什麼是 Azure 事件方格？

Azure Event Grid 可讓您以事件型架構輕鬆地建立應用程式。 首先，請選取您想要訂閱的 Azure 資源，然後提供事件處理常式或 WebHook 端點以作為事件的傳送目的地。 對於來自 Azure 服務 (例如儲存體 Blob 和資源群組) 的事件，Event Grid 內建了支援功能。 事件格線也支援您本身使用自訂主題的事件。 

您可以使用篩選器將特定事件路由傳送到不同的端點、多點傳送至多個端點，並確定您的事件會可靠地進行傳遞。

Azure 事件方格的部署方式是以原生方式分散到每個區域中中的多個容錯網域，以及分散到可用性區域 (在支援可用性區域的區域中)，藉此來最大化可用性。 如需事件方格支援的區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

本文提供 Azure Event Grid 的概觀。 若要開始使用 Event Grid，請參閱[使用 Azure Event Grid 建立和路由傳送自訂事件](custom-event-quickstart.md)。 

![來源和處理常式的事件方格模型](./media/overview/functional-model.png)

此圖顯示事件方格如何連接來源和處理常式，但這並非完整的支援整合清單。

## <a name="event-sources"></a>事件來源

目前支援將事件傳送到事件格線的 Azure 服務如下：

- [Azure 應用程式組態](event-schema-app-configuration.md)
- [Azure Blob 儲存體](event-schema-blob-storage.md)
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure 事件中樞](event-schema-event-hubs.md)
- [Azure IoT 中心](event-schema-iot-hub.md)
- [Azure 金鑰保存庫](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure 地圖服務](event-schema-azure-maps.md)
- [Azure 媒體服務](event-schema-media-services.md)
- [Azure 資源群組](event-schema-resource-groups.md)
- [Azure 服務匯流排](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure 訂用帳戶](event-schema-subscriptions.md)

## <a name="event-handlers"></a>事件處理常式

如需各個處理常式的功能以及相關文件的完整資料，請參閱[處理常式來源](event-handlers.md)。 目前支援處理來自事件格線之事件的 Azure 服務如下： 

* [Azure 自動化](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [事件中樞](handler-event-hubs.md)
* [轉送混合式連線](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Power Automate (先前稱為 Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [服務匯流排](handler-service-bus.md)
* [佇列儲存體](handler-storage-queues.md)
* [WebHook](handler-webhooks.md)

## <a name="concepts"></a>概念

Azure Event Grid 中有五個概念可讓您開始進行：

* **事件** - 發生了什麼事。
* **事件來源** - 事件發生位置。
* **主題** - 要作為發行者所傳送之事件的目的地端點。
* **事件訂閱** - 用來路由傳送事件的端點或內建機制，有時會路由至多個處理常式。 處理常式也會使用訂閱來智慧地篩選內送事件。
* **事件處理常式** - 對事件做出回應的應用程式或服務。

如需這些概念的詳細資訊，請參閱 [Azure Event Grid 中的概念](concepts.md)。

## <a name="capabilities"></a>功能

以下是 Azure Event Grid 的一些主要功能︰

* **簡便性** - 點按即可瞄準從 Azure 資源到任何事件處理常式或端點的事件。
* **進階篩選** - 篩選事件類型或事件發佈路徑，以確定事件處理常式只會收到相關的事件。
* **展開傳送** - 讓數個端點訂閱相同的事件，以便將事件的複本傳送到所需的多個位置。
* **可靠性** - 24 小時的指數輪詢重試，可確保事件能夠傳遞出去。
* **依事件支付** - 只需就您使用的 Event Grid 數量來付費。
* **高輸送量** - 在每秒支援數百萬個事件的 Event Grid 上建置大量的工作負載。
* **內建事件** - 利用資源定義的內建事件來快速地啟動並執行。
* **自訂事件** - 使用事件方格來路由傳送、篩選並在應用程式中可靠地傳遞自訂事件。

如需事件格線、事件中樞和服務匯流排的比較，請參閱[選擇傳遞訊息的 Azure 服務](compare-messaging-services.md)。

## <a name="what-can-i-do-with-event-grid"></a>我可以用 Event Grid 來做什麼？

Azure 事件方格提供好幾種功能，可大幅提升無伺服器、作業自動化和[整合](https://azure.com/integration)工作： 

### <a name="serverless-application-architectures"></a>無伺服器應用程式架構

![無伺服器應用程式架構](./media/overview/serverless_web_app.png)

Event Grid 可連線資料來源與事件處理常式。 例如，使用事件方格觸發可在影像新增至 Blob 儲存體容器時加以分析的無伺服器函式。 

### <a name="ops-automation"></a>作業自動化

![作業自動化](./media/overview/Ops_automation.png)

Event Grid 可讓您加快自動化速度並簡化原則強制執行。 例如，使用事件方格在建立虛擬機器或 SQL Database 時通知 Azure 自動化。 使用事件自動檢查服務組態是否符合規範、將中繼資料放入作業工具、標記虛擬機器，或將工作項目歸檔。

### <a name="application-integration"></a>應用程式整合

![與 Azure 的應用程式整合](./media/overview/app_integration.png)

Event Grid 可連線您的應用程式與其他服務。 例如，建立自訂主體，將應用程式的事件資料傳送至 Event Grid，並利用其可靠的傳遞、進階路由以及與 Azure 的直接整合。 或者，您可以使用事件方格搭配 Logic Apps 隨處處理資料，而不需撰寫程式碼。 

## <a name="how-much-does-event-grid-cost"></a>Event Grid 的費用有多少？

Azure Event Grid 使用依事件支付計價模式，因此您只需就使用量來付費。 每個月的前 100000 個作業是免費的。 作業的定義為事件輸入、訂用帳戶傳遞嘗試、管理呼叫，以及以主旨後置詞進行篩選。 如需詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="next-steps"></a>後續步驟

* [路由儲存體 Blob 事件](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  使用 Event Grid 回應儲存體 blob 事件。
* [建立並訂閱自訂事件](custom-event-quickstart.md)  
  直接進入正題並開始使用 Azure Event Grid 快速入門來將您自己的自訂事件傳送至任何端點。
* [使用 Logic Apps 來作為事件處理常式](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  有關使用 Logic Apps 建置應用程式以回應 Event Grid 所推送之事件的教學課程。
* [將巨量資料串流處理至資料倉儲](event-grid-event-hubs-integration.md)  
  使用 Azure Functions 將資料從事件中樞串流到 SQL 資料倉儲的教學課程。
* [Event Grid REST API 參考](/rest/api/eventgrid)  
  提供參考內容來管理事件訂閱、路由和篩選。