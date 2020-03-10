---
title: 擴充性-Azure 事件中樞 |Microsoft Docs
description: 本文提供如何使用資料分割和輸送量單位來調整 Azure 事件中樞的相關資訊。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365323"
---
# <a name="scaling-with-event-hubs"></a>使用事件中樞進行調整

有兩個因素會影響事件中樞的調整。
*   輸送量單位
*   資料分割

## <a name="throughput-units"></a>輸送量單位

事件中樞的輸送量容量受「輸送量單位」所控制。 輸送量單位是預先購買的容量單位。 單一輸送量可讓您：

* 輸入：每秒最多 1 MB 或 1000 個事件 (以先達到者為準)。
* 輸出：最高每秒 2 MB 或每秒 4096 個事件。

超出所購買輸送量單位的容量時，輸入就開始節流，並傳回 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)。 輸出不會產生節流例外狀況，但仍受限於所購買輸送量單位的容量。 如果您收到發佈速率例外狀況，或輸出速率低於預期，請務必檢查您為命名空間所購買的輸送量單位數目。 在 [Azure 入口網站](https://portal.azure.com)中，您可以在命名空間的 [調整規模] 刀鋒視窗管理輸送量單位。 您也可以使用[事件中樞 API](event-hubs-api-overview.md) 以程式設計方式管理輸送量單位。

輸送量單位是預先購買制且以每小時計費。 一經購買，您至少必須支付一個小時的輸送量單位費用。 最多可以為一個事件中樞命名空間購買 20 個輸送量單位，讓該命名空間中的所有事件中樞共用。

「事件中樞」的「自動擴充」功能會自動增加輸送量單位數以進行相應增加，進而符合使用量需求。 增加輸送量單位可避免發生節流情況，其中：

- 資料輸入速率會超出所設定的輸送量單位。
- 資料輸出要求速率會超出所設定的輸送量單位。

「事件中樞」服務可在負載超過最低閾值時增加輸送量，不會有任何要求因為發生 ServerBusy 錯誤而失敗。 

如需自動擴充功能的詳細資訊，請參閱[自動調整輸送量單位](event-hubs-auto-inflate.md)。

## <a name="partitions"></a>資料分割
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>分割區索引鍵

您可以使用[資料分割索引鍵](event-hubs-programming-guide.md#partition-key)，將連入事件資料對應至特定的資料分割來組織資料。 資料分割索引鍵是由傳送者提供的值。系統會將它傳遞到事件中樞， 然後透過靜態雜湊函式加以處理，而建立資料分割指派。 如果您在發佈事件時未指定資料分割索引鍵，系統會使用循環配置資源指派。

事件發佈行者只會知道資料分割索引鍵，不會知道事件發佈的目的地資料分割。 索引鍵和資料分割脫鉤的這種機制，讓傳送者不需要知道太多有關下游處理的細節。 每部裝置或每位使用者的唯一身分識別能成為有效的資料分割索引鍵，不過像地理位置之類的其他屬性也能用來將相關事件歸納成一個資料分割。


## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

- [自動調整輸送量單位](event-hubs-auto-inflate.md)
- [事件中樞服務概觀](event-hubs-what-is-event-hubs.md)
