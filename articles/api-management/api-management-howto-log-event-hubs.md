---
title: 如何將事件記錄到 Azure API 管理中的 Azure 事件中樞 | Microsoft Docs
description: 了解如何將事件記錄到 Azure API 管理中的 Azure 事件中樞。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f67079938ddcf4a65e01ef50ab7e5cdf7078b73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260933"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>如何將事件記錄到 Azure API 管理中的 Azure 事件中樞
事件中樞是可高度調整的資料輸入服務，每秒可擷取數百萬個事件，可讓您處理和分析連接的裝置和應用程式所產生的大量資料。 事件中樞能做為事件管線的「大門」，一旦收集的資料進入事件中樞，它可以使用任何即時分析提供者或批次/儲存配接器轉換及儲存資料。 事件中樞能分隔事件串流的生產與這些事件的使用，讓事件消費者依照自己的排程存取事件。

這篇文章附隨於 [整合 Azure API 管理與事件中樞](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) 視訊並說明如何使用 Azure 事件中樞記錄 API 管理事件。

## <a name="create-an-azure-event-hub"></a>建立 Azure 事件中樞

如需如何建立事件中樞，並取得在事件中樞傳送及接收事件所需之連接字串的詳細步驟，請參閱[使用 Azure 入口網站建立事件中樞命名空間和事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。

## <a name="create-an-api-management-logger"></a>建立 API 管理記錄器
現在您已經有事件中樞，下一步是在 API 管理服務中設定 [記錄器](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger) ，以將事件記錄至事件中樞。

可使用 [API 管理 REST API](https://aka.ms/apimapi)來設定 API 管理記錄器。 如需詳細的要求範例，請參閱[如何建立記錄器](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger/createorupdate)。

## <a name="configure-log-to-eventhubs-policies"></a>設定 log-to-eventhubs 原則

您在 API 管理中設定好記錄器後，便可設定 log-to-eventhubs 原則來記錄所需的事件。 log-to-eventhubs 原則可用於輸入原則區段或輸出原則區段。

1. 瀏覽至您的 APIM 執行個體。
2. 選取 [API] 索引標籤。
3. 選取您要在其中新增原則的 API。 在此範例中，我們將原則新增至 [無限制]**** 產品中的 [Echo API]****。
4. 選取 [所有作業]  。
5. 選取畫面頂端的 [設計] 索引標籤。
6. 在 [輸入處理] 或 [輸出處理] 視窗中，按一下三角形 (鉛筆旁邊)。
7. 選取 [程式碼編輯器]。 如需詳細資訊，請參閱[如何設定或編輯原則](set-edit-policies.md)。
8. 將游標置於 `inbound` 或 `outbound` 原則區段。
9. 在右側視窗中，選取 [ **Advanced 原則** > ] [**Log to EventHub**]。 這會插入 `log-to-eventhub` 原則陳述式範本。

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
將 `logger-id` 取代為在前一步驟中用於 URL `{new logger name}` 的值 (建立記錄器)。

您可以使用任何能傳回字串做為 `log-to-eventhub` 項目之值的運算式。 在此範例中，將記錄包含日期和時間、服務名稱、要求識別碼、要求 IP 位址和作業名稱的字串。

按一下 [儲存] **** ，儲存更新的原則組態。 儲存完成後，原則便會啟用，事件會記錄至指定的事件中樞。

## <a name="next-steps"></a>後續步驟
* 深入了解 Azure 事件中樞
  * [開始使用 Azure 事件中樞](../event-hubs/event-hubs-c-getstarted-send.md)
  * [使用 EventProcessorHost 接收訊息](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [事件中樞程式設計指南](../event-hubs/event-hubs-programming-guide.md)
* 深入了解 API 管理和事件中樞的整合
  * [記錄器實體參考](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/logger)
  * [log-to-eventhub 原則參考](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [利用 Azure API 管理、事件中樞與 Moesif 監視您的 API](api-management-log-to-eventhub-sample.md)  
* 深入了解如何[與 Azure Application Insights 整合](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
