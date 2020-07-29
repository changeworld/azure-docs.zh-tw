---
title: 了解 Azure IoT 中樞內建端點 | Microsoft Docs
description: 開發人員指南：說明如何使用內建的事件中樞相容端點來讀取裝置到雲端訊息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 941953c75c516a9eceff526a0ced0ec0910f1f1e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327696"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>從內建端點讀取裝置對雲端訊息

根據預設，訊息會路由到與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的內建服務對應端點 (**訊息/事件**)。 此端點目前只連接埠 5671 上使用 [AMQP](https://www.amqp.org/) 通訊協定公開。 IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

| 屬性            | 描述 |
| ------------------- | ----------- |
| **分割計數** | 在建立時設定此屬性，以定義裝置到雲端事件擷取的[分割區](../event-hubs/event-hubs-features.md#partitions)數目。 |
| **保留時間**  | 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。 |

IoT 中樞允許資料在內建事件中樞最多保留 7天。 您可以在建立 IoT 中樞期間設定保留時間。 IoT 中樞的資料保留時間取決於您的 IoT 中樞層和單位類型。 就大小而言，內建事件中樞可以保留的訊息，其訊息大小上限高達至少 24 小時的配額。 例如，針對 1 個 S1 單位，IoT 中樞會提供足夠的儲存空間，以保留至少 400K 則訊息，每則訊息 4K 大小。 如果您的裝置傳送較小的訊息，則這些訊息會保留較長的時間 (最多 7 天)，視耗用的儲存空間而定。 我們保證至少將資料保留指定的保留時間。 訊息將會過期，而且在過了保留時間之後將無法存取。 

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。 每個 IoT 中樞最多可以有 20 個取用者群組。

如果您使用[訊息路由](iot-hub-devguide-messages-d2c.md)且[後援路由](iot-hub-devguide-messages-d2c.md#fallback-route)已啟用，就會將任何路由上未符合查詢的所有訊息移至內建端點。 如果您停用此後援路由，則會捨棄不符合任何查詢的訊息。

您可以使用 [IoT 中樞資源提供者 REST API](/rest/api/iothub/iothubresource) 採取程式設計方式，或透過 [Azure 入口網站](https://portal.azure.com)來修改保留期時間。

IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

## <a name="read-from-the-built-in-endpoint"></a>從內建端點讀取

某些產品整合和事件中樞 SDK 會感知 IoT 中樞，並讓您使用 IoT 中樞服務連接字串來連線到內建端點。

當您使用無法感知 IoT 中樞的事件中樞 SDK 或產品整合時，需要事件中樞相容端點和事件中樞相容名稱。 您可以從入口網站擷取這些值，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 按一下 [內建端點]。

3. [事件] 區段包含下列值：**分割區**、**事件中樞相容名稱**、**事件中樞相容端點**、**保留時間**和**取用者群組**。

    ![裝置到雲端設定](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在入口網站中，[事件中樞相容端點] 欄位包含完整的事件中樞連接字串，如下所示：**Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**。 如果您所使用的 SDK 需要其他值，則其會是：

| 名稱 | 值 |
| ---- | ----- |
| 端點 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主機名稱 | abcd1234namespace.servicebus.windows.net |
| 命名空間 | abcd1234namespace |

接著，您可以從下拉式選單中選擇任何共用存取原則，如上方螢幕擷取畫面所示。 它只會顯示具有**ServiceConnect**許可權的原則，以連線到指定的事件中樞。

您可以用來連線到內建事件中樞相容端點 (由 IoT 中樞公開) 的 SDK 包括：

| Language | SDK | 範例 |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [快速入門](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [快速入門](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [快速入門](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [快速入門](quickstart-send-telemetry-python.md) |

可與內建事件中樞相容端點 (由 IoT 中樞公開) 搭配使用的產品整合包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 請參閱[使用 Azure Functions 處理來自 IoT 中樞的資料](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)。 請參閱[將資料作為輸入串流處理至串流分析中](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* [時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/)。 請參閱[新增 IoT 中樞事件來源到您的時間序列深入解析環境](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* [快速入門](quickstart-send-telemetry-node.md)示範如何從模擬裝置傳送裝置到雲端的訊息，以及從內建端點讀取訊息。 

如需詳細資料，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程。

* 如果您想要將裝置到雲端的訊息路由至自訂端點，請參閱[針對裝置到雲端訊息使用訊息路由與自訂端點](iot-hub-devguide-messages-read-custom.md)。
