---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901750"
---
下表提供了特定于[Azure 事件中心的](https://azure.microsoft.com/services/event-hubs/)配額和限制。 如需事件中樞價格的相關資訊，請參閱[事件中樞價格](https://azure.microsoft.com/pricing/details/event-hubs/)。

以下限制在基本層、標準和專用層中很常見。 

| 限制 | 影響範圍 | 注意 | 值 |
| --- | --- | --- | --- |
| 每一訂用帳戶的「事件中樞」命名空間數目 |訂用帳戶 |- |100 |
| 每個命名空間的事件中樞數目 |命名空間 |後續創建新事件中心的請求將被拒絕。 |10 |
| 事件中樞內的資料分割數目 |單位 |- |32 |
| 事件中樞名稱的大小上限 |單位 |- |50 個字元 |
| 每個取用者群組的非 epoch 接收者數目 |單位 |- |5 |
| 最大輸送量單位 |命名空間 |超過輸送量單位限制會導致資料被限制並生成[伺服器忙異常](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)。 要請求更多標準層的輸送量單位，請提交[支援請求](/azure/azure-portal/supportability/how-to-create-azure-support-request)。 [更多輸送量單位](../articles/event-hubs/event-hubs-auto-inflate.md)依承諾購買方式，以 20 個為一組來取得。 |20 |
| 每個命名空間的授權規則數目 |命名空間|後續授權規則創建請求將被拒絕。|12 |
| 到 GetRuntime 資訊方法的調用數 | 單位 | - | 50 秒 | 
| 虛擬網路 （VNet） 和 IP 配置規則的數量 | 單位 | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>事件中心 基本和標準 - 配額和限制
| 限制 | 影響範圍 | 注意 | 基本 | 標準 |
| --- | --- | --- | -- | --- |
| 事件中樞事件的大小上限|單位 | &nbsp; | 256 KB | 1 MB |
| 每一個事件中樞取用者群組數目 |單位 | &nbsp; |1 |20 |
| 每個命名空間的 AMQP 連線數目 |命名空間 |後續的其他連接請求將被拒絕，並且調用代碼會收到異常。 |100 |5,000|
| 事件資料的最大保留期間 |單位 | &nbsp; |1 日 |1-7 天 |
|阿帕奇卡夫卡啟用命名空間|命名空間 |事件中心命名空間使用卡夫卡協定資料流應用程式 |否 | 是 |
|擷取 |單位 | 啟用後，在同一流上的微批次處理 |否 |是 |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>活動中心專用 - 配額和限制
活動中心專用產品按固定月價計費，至少使用 4 小時。 專用層提供標準計畫的所有功能，但具有企業規模容量和工作負載要求苛刻的客戶的限制。 

| 功能 | 限制 |
| --- | ---|
| 頻寬 |  20 個庫 |
| 命名空間 | 每個 CU 50 個 |
| 事件中樞 |  每個命名空間 1000 個 |
| 輸入事件 | 已包括 |
| 訊息大小 | 1 MB |
| 資料分割 | 每個 CU 2000 |
| 用戶群組 | 每個 CU 沒有限制，每個事件中心 1000 個 |
| 代理連線 | 包含 10 萬個 |
| 訊息保留期 | 90 天，每個 CU 包含 10 TB |
| 擷取 | 已包括 |
