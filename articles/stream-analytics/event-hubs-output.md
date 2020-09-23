---
title: Azure 串流分析的事件中樞輸出
description: 本文說明如何將資料從 Azure 串流分析輸出至 Azure 事件中樞。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 50d2d974815e0921d99154bce67f604b7314970d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892029"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Azure 串流分析的事件中樞輸出

[Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/) 服務是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。 當串流分析作業成為另一個串流作業的輸入時，就會使用事件中樞作為輸出。 如需訊息大小上限和批次大小最佳化的詳細資訊，請參閱[輸出批次大小](#output-batch-size)一節。

## <a name="output-configuration"></a>輸出設定

下表包含從事件中樞將資料流程設定為輸出所需的參數。

| 屬性名稱 | 描述 |
| --- | --- |
| 輸出別名 | 此為易記名稱，用於在查詢中將查詢輸出指向這個事件中樞。 |
| 事件中樞命名空間 | 一組訊息實體的容器。 建立新的事件中樞時，也會建立事件中樞命名空間。 |
| 事件中樞名稱 | 事件中樞輸出的名稱。 |
| 事件中樞原則名稱 | 共用存取原則，您可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則都會有名稱、權限 (由您設定) 和存取金鑰。 |
| 事件中樞原則金鑰 | 用來驗證事件中樞命名空間存取權的共用存取金鑰。 |
| 分割區索引鍵資料行 | 選擇性。 資料行包含事件中樞輸出的分割區索引鍵。 |
| 事件序列化格式 | 輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。 |
| 編碼 | 對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式。 |
| 分隔符號 | 僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| [格式] | 僅適用於 JSON 序列化。 **分隔的行**會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 如果您選取**分隔的行**，JSON 會一次讀取一個物件。 整個內容本身不是有效的 JSON。 **陣列**會指定輸出將會格式化為 JSON 物件的陣列。  |
| 屬性資料行 | 選擇性。 以逗號分隔的資料行，必須附加為外寄郵件的使用者屬性，而不是承載。 如需這項功能的詳細資訊，請參閱[輸出的自訂中繼資料屬性](#custom-metadata-properties-for-output)一節。 |

## <a name="partitioning"></a>資料分割

資料分割會根據資料分割的對齊方式而有所不同。 當事件中樞輸出中的分割區索引鍵與上游 (先前的) 查詢步驟同等對齊時，寫入器的數目將會和事件中樞輸出中的分割區數目相同。 每個寫入器都會使用 [EventHubSender 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true)來將事件傳送至特定的分割區。 當事件中樞輸出中的分割區索引鍵沒有與上游 (先前的) 查詢步驟同等對齊時，寫入器的數目將會和先前步驟中的分割區數目相同。 每個寫入器會使用 **EventHubClient** 中的 [SendBatchAsync 類別](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true)，將事件傳送至所有輸出分割區。 

## <a name="output-batch-size"></a>輸出批次大小

訊息大小上限為 256 KB 或每則訊息 1 MB。 如需詳細資訊，請參閱 [事件中樞限制](../event-hubs/event-hubs-quotas.md)。 當輸入/輸出資料分割沒有對齊時，每個事件會個別封裝於 `EventData` 中，並在以訊息大小上限為限的情況下，以批次的方式傳送。 如果使用[自訂中繼資料屬性](#custom-metadata-properties-for-output)，也會發生這種情況。 當輸入/輸出資料分割有對齊時，多個事件會封裝為單一 `EventData` 執行個體，以訊息大小上限為限並傳送。

## <a name="custom-metadata-properties-for-output"></a>輸出的自訂中繼資料屬性

您可以將查詢資料行當做使用者屬性附加至外寄郵件。 這些資料行不會進入承載。 屬性會以輸出訊息上的字典形式呈現。 「索引鍵」是資料行名稱，「值」是屬性字典中的資料行值。 除了「記錄」和「陣列」以外，支援所有串流分析資料類型。  

## <a name="next-steps"></a>後續步驟

* [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [快速入門：使用 Azure CLI 建立 Azure 串流分析作業](quick-create-azure-cli.md)
* [快速入門：使用 ARM 範本建立 Azure 串流分析作業](quick-create-azure-resource-manager.md)
* [快速入門：使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)
* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
