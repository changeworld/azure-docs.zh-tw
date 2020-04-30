---
title: 了解 Azure IoT 中樞內建端點 | Microsoft Docs
description: 開發人員指南：說明如何使用內建的事件中樞相容端點來讀取裝置到雲端訊息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: amqp
ms.openlocfilehash: 169d926e466559bc83ba64ce9e976e0d725f614d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729994"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>從內建端點讀取裝置對雲端訊息

根據預設，訊息會路由到與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的內建服務對應端點 (**訊息/事件**)。 此端點目前只連接埠 5671 上使用 [AMQP](https://www.amqp.org/) 通訊協定公開。 IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

| 屬性            | 描述 |
| ------------------- | ----------- |
| **分割區計數** | 在建立時設定此屬性，以定義裝置到雲端事件[內嵌的分割](../event-hubs/event-hubs-features.md#partitions)區數目。 |
| **保留時間**  | 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。 |

IoT 中樞允許在內建事件中樞中保留最多7天的資料。 您可以在建立 IoT 中樞期間設定保留時間。 IoT 中樞中的資料保留時間取決於您的 IoT 中樞層和單位類型。 就大小而言，內建的事件中樞可以保留最大訊息大小的訊息，最多可達24小時的配額。 例如，針對1個 S1 單位，IoT 中樞提供足夠的儲存空間，以保留至少每個4k 大小的400K 訊息。 如果您的裝置正在傳送較小的訊息，則會保留較長的時間（最多7天），視耗用的儲存體數量而定。 我們保證至少會保留指定保留時間的資料。 訊息將會過期，而且在超過保留時間之後將無法存取。 

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。 每個 IoT 中樞最多可以有20個取用者群組。

如果您使用[訊息路由](iot-hub-devguide-messages-d2c.md)，且已啟用回溯[路由](iot-hub-devguide-messages-d2c.md#fallback-route)，則不符合任何路由上查詢的所有訊息都會移至內建端點。 如果您停用此回復路由，則會捨棄不符合任何查詢的訊息。

您可以使用 [IoT 中樞資源提供者 REST API](/rest/api/iothub/iothubresource) 採取程式設計方式，或透過 [Azure 入口網站](https://portal.azure.com)來修改保留期時間。

IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

## <a name="read-from-the-built-in-endpoint"></a>從內建端點讀取

某些產品整合和事件中樞 Sdk 會感知 IoT 中樞，並可讓您使用 IoT 中樞服務連接字串來連接到內建端點。

當您使用不知道 IoT 中樞的事件中樞 Sdk 或產品整合時，您需要與事件中樞相容的端點和事件中樞相容名稱。 您可以從入口網站取得這些值，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 按一下 [內建端點]****。

3. [**事件**] 區段包含下列**值：資料**分割、**事件中樞相容名稱**、**事件中樞相容端點**、**保留時間**和取用**者群組**。

    ![裝置到雲端設定](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在入口網站中，[事件中樞相容端點] 欄位包含完整的事件中樞連接字串，如下所示：**端點 = sb：//abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName = iothubowner;SharedAccessKey = keykeykeykeykeykey =;EntityPath = iothub-ehub-abcd-1234-123456**。 如果您所使用的 SDK 需要其他值，則會是：

| 名稱 | 值 |
| ---- | ----- |
| 端點 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主機名稱 | abcd1234namespace.servicebus.windows.net |
| 命名空間 | abcd1234namespace |

然後，您可以使用具有 **ServiceConnect** 權限的任何共用存取原則，連接至指定的事件中樞。

您可以用來連線到 IoT 中樞公開之內建事件中樞相容端點的 Sdk 包括：

| Language | SDK | 範例 | 備忘錄 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [快速入門](quickstart-send-telemetry-dotnet.md) | 使用事件中樞相容資訊 |
 Java | https://github.com/Azure/azure-event-hubs-java | [快速入門](quickstart-send-telemetry-java.md) | 使用事件中樞相容資訊 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [快速入門](quickstart-send-telemetry-node.md) | 使用 IoT 中樞連接字串 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | 使用 IoT 中樞連接字串 |

您可以搭配 IoT 中樞公開之內建事件中樞相容端點使用的產品整合包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 請參閱[使用 Azure Functions 處理來自 IoT 中樞的資料](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)。 請參閱將[資料當做輸入串流至串流分析](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* [時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/)。 請參閱[將 IoT 中樞事件來源新增至您的時間序列深入解析環境](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* [快速入門](quickstart-send-telemetry-node.md)示範如何從模擬裝置傳送裝置到雲端的訊息，以及從內建端點讀取訊息。 

如需詳細資料，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程。

* 如果您想要將裝置到雲端訊息路由至自訂端點，請參閱[針對裝置到雲端訊息使用訊息路由和自訂端點](iot-hub-devguide-messages-read-custom.md)。
