---
title: Azure 事件方格中的系統主題
description: 說明 Azure 事件方格中的系統主題。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: spelluru
ms.openlocfilehash: 190c6ccb13a0853913c96ac5d2d3f5faf4594433
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887919"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件方格中的系統主題
事件方格中的系統主題代表 Azure 服務（例如 Azure 儲存體和 Azure 事件中樞）所發佈的一個或多個事件。 例如，系統主題可能代表**所有 blob 事件**，或是只為**特定儲存體帳戶**發佈**的 blob 和** **blob 刪除**事件。 在此範例中，將 blob 上傳至儲存體帳戶時，Azure 儲存體服務會將**blob 建立**的事件發佈至事件方格中的系統主題，然後將事件轉寄給接收和處理事件的主題[訂閱者](event-handlers.md)。 

> [!NOTE] 
> 只有 Azure 服務可以將事件發佈至系統主題。 因此，您不會取得可用來發佈事件的端點或便捷鍵，就像自訂主題或網域一樣。

## <a name="azure-services-that-support-system-topics"></a>支援系統主題的 Azure 服務
以下是目前支援在其上建立系統主題的 Azure 服務清單。

- [Azure 應用程式組態](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
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

## <a name="system-topics-as-azure-resources"></a>做為 Azure 資源的系統主題
在過去，系統主題是隱含的，而且不會為了簡單起見而公開。 系統主題現在會顯示為 Azure 資源，並提供下列功能：

- [查看 Azure 入口網站中的系統主題](create-view-manage-system-topics.md#view-all-system-topics)
- 匯出 Azure 入口網站中系統主題和事件訂閱 Resource Manager 範本
- [設定系統主題的診斷記錄](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 設定發佈和傳遞失敗的警示 

## <a name="lifecycle-of-system-topics"></a>系統主題的生命週期
您可以透過兩種方式建立系統主題： 

- 在[Azure 資源上建立事件訂用帳戶作為擴充功能資源](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)，這會自動建立名稱為的系統主題，格式為： `<Azure resource name>-<GUID>` 。 當主題的最後一個事件訂用帳戶被刪除時，會自動刪除以這種方式建立的系統主題。 
- 建立 Azure 資源的系統主題，然後建立該系統主題的事件訂用帳戶。 當您使用這個方法時，可以指定系統主題的名稱。 刪除最後一個事件訂用帳戶時，系統主題不會自動刪除。 您必須手動刪除它。 

    當您使用 Azure 入口網站時，一律會使用這個方法。 當您使用 Azure 資源的 [ [**事件**] 頁面](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)建立事件訂用帳戶時，系統會先建立該主題，然後才會建立該主題的訂用帳戶。 您可以先使用[**事件方格系統主題**頁面](create-view-manage-system-topics.md#create-a-system-topic)明確建立系統主題，然後建立該主題的訂用帳戶。 

當您使用[CLI](create-view-manage-system-topics-cli.md)、 [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)或[Azure Resource Manager 範本](create-view-manage-system-topics-arm.md)時，您可以選擇上述任一種方法。 我們建議您先建立系統主題，然後在主題上建立訂用帳戶，因為這是建立系統主題的最新方式。

如果您已設定 Azure 原則，讓事件方格服務無法建立它，則系統主題建立會失敗。 例如，您可能會有一個原則，允許只在訂用帳戶中建立特定類型的資源（例如： Azure 儲存體、Azure 事件中樞等）。 

## <a name="location-and-resource-group-for-a-system-topic"></a>系統主題的位置和資源群組
針對特定區域/位置中的 Azure 事件來源，系統主題會建立在與 Azure 事件來源相同的位置中。 例如，如果您為美國東部的 Azure blob 儲存體建立事件訂用帳戶，系統主題會建立在美國東部。 對於全域 Azure 事件來源（例如 Azure 訂用帳戶、資源群組或 Azure 地圖服務），事件方格會在**全域**位置中建立系統主題。 

一般而言，系統主題會建立在 Azure 事件來源所在的相同資源群組中。 針對在 Azure 訂用帳戶範圍建立的事件訂閱，系統主題會建立在資源群組**預設-EventGrid**之下。 如果資源群組不存在，Azure 事件方格會先建立它，再建立系統主題。 

## <a name="next-steps"></a>後續步驟
查看下列文章： 

- [使用 Azure 入口網站建立、查看和管理系統主題](create-view-manage-system-topics.md)。
- [使用 Azure CLI 建立、查看和管理事件方格系統主題](create-view-manage-system-topics-cli.md)
- [使用 Azure Resource Manager 範本來建立 Event Grid 系統主題](create-view-manage-system-topics-arm.md)
