---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012787"
---
下表提供 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs/)的特定配額與限制。 如需事件中樞價格的相關資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

下列限制在基本和標準層之間是共通的。 

| 限制 | 影響範圍 | 注意 | 值 |
| --- | --- | --- | --- |
| 每一訂用帳戶的「事件中樞」命名空間數目 |訂用帳戶 |- |100 |
| 每個命名空間的事件中樞數目 |命名空間 |建立新的事件中樞的後續要求將遭到拒絕。 |10 |
| 事件中樞內的資料分割數目 |單位 |- |32 |
| 事件中樞名稱的大小上限 |單位 |- | 256 個字元 |
| 取用者群組名稱的大小上限 |單位 |- | 256 個字元 |
| 每個取用者群組的非 epoch 接收者數目 |單位 |- |5 |
| 最大輸送量單位 |命名空間 |超過輸送量單位限制會導致您的資料受到節流，並產生[伺服器忙碌例外狀況](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 若要為標準層要求大量的輸送量單位，請提出[支援要求](/azure/azure-portal/supportability/how-to-create-azure-support-request)。 [更多輸送量單位](../articles/event-hubs/event-hubs-auto-inflate.md)依承諾購買方式，以 20 個為一組來取得。 |20 |
| 每個命名空間的授權規則數目 |命名空間|建立授權規則的後續要求將遭到拒絕。|12 |
| 對 GetRuntimeInformation 方法的呼叫次數 | 單位 | - | 每秒 50 個 | 
| 虛擬網路 (VNet)和 IP 設定規則的數目 | 單位 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>事件中樞基本和標準 - 配額和限制
| 限制 | 影響範圍 | 注意 | 基本 | 標準 |
| --- | --- | --- | -- | --- |
| 事件中樞事件的大小上限|單位 | &nbsp; | 256 KB | 1 MB |
| 每一個事件中樞取用者群組數目 |單位 | &nbsp; |1 |20 |
| 每個命名空間的 AMQP 連線數目 |命名空間 |後續對更多連線的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 |100 |5,000|
| 事件資料的最大保留期間 |單位 | &nbsp; |1 日 |1-7 天 |
|已啟用 Apache Kafka 的命名空間|命名空間 |使用 Kafka 通訊協定的事件中樞命名空間串流應用程式。 如需詳細資訊，請參閱[從 Apache Kafka 應用程式使用 Azure 事件中樞](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)。 |否 | 是 |
|擷取 |單位 | 啟用時，相同串流上的微批次。 如需詳細資訊，請參閱[透過 Azure 事件中樞在 Azure Blob 儲存體或 Azure Data Lake Storage 中擷取事件](../articles/event-hubs/event-hubs-capture-overview.md)。 |否 |是 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>事件中樞專用 - 配額和限制
事件中樞專用供應項目以每月固定價格計費，最低為 4 小時的使用量。 專用層提供標準方案的所有功能，但可對工作負載需求高的客戶提供企業規模容量和限制。 

| 功能 | 限制 |
| --- | ---|
| 頻寬 |  20 個 CU |
| 命名空間 | 每個 CU 50 個 |
| 事件中樞 |  每一命名空間 1000 個 |
| 輸入事件 | 已包括 |
| 訊息大小 | 1 MB |
| 資料分割 | 每個 CU 2000 個 |
| 用戶群組 | 沒有每個 CU 的限制，每個事件中樞 1000 個 |
| 代理連線 | 包含 10 萬個 |
| 訊息保留期 | 90 天，每個 CU 包含 10 TB |
| 擷取 | 已包括 |
