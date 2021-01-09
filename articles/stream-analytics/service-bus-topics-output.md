---
title: Azure 串流分析的服務匯流排主題輸出
description: 本文說明服務匯流排主題作為 Azure 串流分析的輸出。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 584d73acf36d22f59fbbcb6dff8b2f53cbc7437d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014173"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Azure 串流分析的服務匯流排主題輸出

服務匯流排佇列提供從傳送者到接收者的一對一通訊方法。 [服務匯流排主題](/previous-versions/azure/hh367516(v=azure.100))提供一對多的通訊形式。

下表列出屬性名稱及其描述以建立服務匯流排主題輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排主題。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 建立新的事件中樞時，也會建立服務匯流排命名空間。 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 其設計目的是要從裝置和服務收集事件串流。 建立主題時也會給予其特定名稱。 除非已建立訂用帳戶，否則無法使用傳送至主題的訊息，所以請確保該主題內有一或多個訂用帳戶。 |
| 主題原則名稱 |當您建立服務匯流排主題時，您也可以在主題的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |如果您使用 CSV 或 JSON 格式，則必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的索引鍵值組，以及必須附加至外寄郵件而非承載的對應資料行名稱。 |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="partitioning"></a>資料分割

系統會自動選擇資料分割。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。 輸出寫入器的數目與輸出主題中的分割區數目相同。

## <a name="output-batch-size"></a>輸出批次大小

標準層的訊息大小上限為 256 KB，而進階層的訊息大小上限為1MB。 如需詳細資訊，請參閱 [服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 若要優化，請使用每個訊息的單一事件。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性

您可以將查詢資料行當做使用者屬性附加至外寄郵件。 這些資料行不會進入承載。 屬性會以輸出訊息上的字典形式呈現。 「索引鍵」是資料行名稱，「值」是屬性字典中的資料行值。 除了「記錄」和「陣列」以外，支援所有串流分析資料類型。

在下列範例中，會將欄位 `DeviceId` 和 `DeviceStatus` 加入至中繼資料。

1. 使用下列查詢：

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. `DeviceId,DeviceStatus`在輸出中將設定為屬性資料行。

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="屬性資料行":::

下圖是使用 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)在 EventHub 中檢查的預期輸出訊息屬性。

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="事件自訂屬性":::

## <a name="system-properties"></a>系統屬性

您可以將查詢資料行當做[系統屬性](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties)附加至外寄服務匯流排佇列或主題訊息。 這些資料行不會進入承載，而是以查詢資料行值填入的對應 BrokeredMessage [系統屬性](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties)。
支援這些系統屬性 - `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`。

這些資料行的字串值會剖析為對應的系統屬性值類型，而任何剖析失敗都會被視為資料錯誤。
此欄位是以 JSON 物件格式提供。 此格式的詳細資料如下所示：

* 以大括號 {} 括住。
* 以索引鍵/值組寫入。
* 索引鍵和值必須是字串。
* 索引鍵是系統屬性名稱，而值則是查詢資料行名稱。
* 索引鍵和值以冒號分隔。
* 每個索引鍵/值組都以逗號分隔。

這會顯示如何使用這個屬性 -

* 查詢： `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* 系統屬性資料行：`{ "MessageId": "column1", "PartitionKey": "column2"}`

這會使用 `column1` 的值來設定服務匯流排佇列訊息上的 `MessageId`，以及使用 `column2` 的值來設定 PartitionKey。

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)