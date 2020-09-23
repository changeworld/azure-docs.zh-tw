---
title: 服務匯流排將 Azure 串流分析的輸出排在佇列
description: 本文說明服務匯流排佇列作為 Azure 串流分析的輸出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ba4b8f1d3aaa9b06f3bc24e9e267f6778734152a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903737"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>服務匯流排將 Azure 串流分析的輸出排在佇列

如果有一或多個競爭取用者，[服務匯流排佇列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)會採取 FIFO 訊息傳遞機制。 一般來說，接收者會以其新增至佇列的時態順序來接收和處理訊息。 每則訊息只會由一個訊息取用者接收和處理。

在[相容性層級 1.2](stream-analytics-compatibility-level.md) 中，Azure 串流分析使用[進階訊息佇列通訊協定 (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) 訊息通訊協定來寫入服務匯流排佇列和主題。 透過開放式標準通訊協定，AMQP 可讓您打造一個跨平台的混合式應用程式。

## <a name="output-configuration"></a>輸出設定

下表列出屬性名稱及其描述以建立佇列輸出。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排佇列。 |
| 服務匯流排命名空間 |一組訊息實體的容器。 |
| Queue name |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |當您建立佇列時，您也可以在佇列的 [設定] 索引標籤上建立共用存取原則。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰。 |
| 事件序列化格式 |輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] |僅適用於 JSON 類型。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。 |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |
| 系統屬性資料行 | 選擇性。 系統屬性的索引鍵值組，以及必須附加至外寄郵件而非承載的對應資料行名稱。  |

分割區數目是[根據服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。

## <a name="partitioning"></a>資料分割

系統會自動選擇資料分割。 分割區數目是根據[服務匯流排 SKU 和大小](../service-bus-messaging/service-bus-partitioning.md)。 分割區索引鍵是每個分割區的唯一整數值。 輸出寫入器的數目與輸出佇列中的分割區數目相同。

## <a name="output-batch-size"></a>輸出批次大小

標準層的訊息大小上限為 256 KB，而進階層的訊息大小上限為1MB。 如需詳細資訊，請參閱 [服務匯流排限制](../service-bus-messaging/service-bus-quotas.md)。 若要優化，請使用每個訊息的單一事件。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性

您可以將查詢資料行當做使用者屬性附加至外寄郵件。 這些資料行不會進入承載。 屬性會以輸出訊息上的字典形式呈現。 「索引鍵」是資料行名稱，「值」是屬性字典中的資料行值。 除了「記錄」和「陣列」以外，支援所有串流分析資料類型。

## <a name="system-properties"></a>系統屬性

您可以將查詢資料行當做[系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)附加至外寄服務匯流排佇列或主題訊息。

這些資料行不會進入承載，而是以查詢資料行值填入的對應 BrokeredMessage [系統屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true)。
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
