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
ms.openlocfilehash: a2674ca0f4808cb6f01781565e57369ca5d3ac37
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478779"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>從內建端點讀取裝置對雲端訊息

根據預設，訊息會路由到與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的內建服務對應端點 (**訊息/事件**)。 此端點目前只連接埠 5671 上使用 [AMQP](https://www.amqp.org/) 通訊協定公開。 IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

| 屬性            | 描述 |
| ------------------- | ----------- |
| **分割區計數** | 在創建時設置此屬性以定義設備到雲端事件的[分區](../event-hubs/event-hubs-features.md#partitions)數。 |
| **保留時間**  | 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。 |

IoT 中心允許在內置事件中心中保留數據最多 7 天。 您可以在創建 IoT 中心期間設置保留時間。 IoT 中心中的數據保留時間取決於 IoT 中心層和單位類型。 在大小方面,內置事件中心可以保留最大消息大小的消息,最長配額最多為 24 小時。 例如,對於 1 S1 單元 IoT 中心,它提供了足夠的儲存空間,可以保留每個郵件至少 4k 大小的 400K 消息。 如果您的設備發送的簡訊較小,則它們可能會保留更長時間(最多 7 天),具體取決於消耗的存儲量。 我們保證在指定的保留時間內保留數據。 郵件將過期,在保留時間過後無法訪問。 

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。 每個 IoT 中心最多只能有 20 個消費者組。

如果使用[消息路由](iot-hub-devguide-messages-d2c.md)並啟用[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route),則與任何路由上的查詢不匹配的所有消息都將轉到內置終結點。 如果禁用此回退路由,將刪除與任何查詢不匹配的消息。

您可以使用 [IoT 中樞資源提供者 REST API](/rest/api/iothub/iothubresource) 採取程式設計方式，或透過 [Azure 入口網站](https://portal.azure.com)來修改保留期時間。

IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

## <a name="read-from-the-built-in-endpoint"></a>從內建端點讀取

某些產品整合和事件中心 SDK 瞭解 IoT 中心,允許您使用 IoT 中心服務連接字串連接到內建終結點。

當您使用不瞭解 IoT 中心的事件中心 SDK 或產品整合時,需要與事件中心相容的終結點和事件中心相容的名稱。 可以從門戶檢索這些值,如下所示:

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 按一下 [內建端點]****。

3. **事件**「 部份包含以下值:**群組**」 ,**事件中心相容名稱**,**事件中心相容終結點**,**保留時間與****使用者群組**。

    ![裝置到雲端設定](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在門戶中,事件中心相容終結點欄位包含一個完整的事件中心連接字串,如下所示:**終結點_sb://abcd1234命名空間.服務總線.windows.net/;共用AccessKeyName_iothub擁有者;共享存取金鑰_金鑰金鑰鍵*;實體路徑_iothub-ehub-abcd-1234-123456**。 如果您使用的 SDK 需要其他值,則它們是:

| 名稱 | 值 |
| ---- | ----- |
| 端點 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主機名稱 | abcd1234namespace.servicebus.windows.net |
| 命名空間 | abcd1234 命名空間 |

然後，您可以使用具有 **ServiceConnect** 權限的任何共用存取原則，連接至指定的事件中樞。

可用於連接到 IoT 中心公開的內建事件中心相容終結點的 SDK 包括:

| Language | SDK | 範例 | 注意 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [快速入門](quickstart-send-telemetry-dotnet.md) | 使用事件中心相容的資訊 |
 Java | https://github.com/Azure/azure-event-hubs-java | [快速入門](quickstart-send-telemetry-java.md) | 使用事件中心相容的資訊 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [快速入門](quickstart-send-telemetry-node.md) | 使用 IoT 中心連接字串 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | 使用 IoT 中心連接字串 |

與 IoT 中心公開的內建事件中心相容終結點的產品整合包括:

* [Azure 函數](https://docs.microsoft.com/azure/azure-functions/)。 請參考[Azure 函數處理來自 IoT 中心的資料](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)。 請參考[串流資料 。](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)
* [時間序列的見解](https://docs.microsoft.com/azure/time-series-insights/)。 請參考[此工作的執行的資料來源到 IoT 中心事件來源](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure 資料塊](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* [快速入門](quickstart-send-telemetry-node.md)示範如何從模擬裝置傳送裝置到雲端的訊息，以及從內建端點讀取訊息。 

如需詳細資料，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程。

* 如果您想要將裝置到雲端訊息路由至自訂端點，請參閱[針對裝置到雲端訊息使用訊息路由和自訂端點](iot-hub-devguide-messages-read-custom.md)。
