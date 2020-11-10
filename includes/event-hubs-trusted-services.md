---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427187"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務
當您啟用 [ **允許信任的 Microsoft 服務略過此防火牆** ] 設定時，系統會將您事件中樞資源的存取權授與下列服務。

| 信任的服務 | 支援的使用案例 | 
| --------------- | ------------------------- | 
| Azure Event Grid | 允許 Azure 事件方格將事件傳送至事件中樞命名空間中的事件中樞。 您也必須執行下列步驟： <ul><li>為主題或網域啟用系統指派的身分識別</li><li>將身分識別新增至事件中樞命名空間上的 Azure 事件中樞資料寄件者角色</li><li>然後，將使用事件中樞作為端點的事件訂用帳戶設定為使用系統指派的身分識別。</li></ul> <p>如需詳細資訊，請參閱 [使用受控識別傳遞事件](../articles/event-grid/managed-service-identity.md)</p>|
| Azure 監視器 (診斷設定)  | 允許 Azure 監視器將診斷資訊傳送至事件中樞命名空間中的事件中樞。 |
| Azure 串流分析 | 允許 Azure 串流分析作業從 ([輸入](../articles/stream-analytics/stream-analytics-add-inputs.md)) 讀取資料，或將資料寫入至事件中樞命名空間中) 事件中樞 ([輸出](../articles/stream-analytics/event-hubs-output.md) 。 |
| Azure IoT 中樞 | 允許 IoT 中樞將訊息傳送至事件中樞命名空間中的事件中樞。 您也必須執行下列步驟： <ul><li>為您的 IoT 中樞啟用系統指派的身分識別</li><li>將身分識別新增至事件中樞命名空間上的 Azure 事件中樞資料寄件者角色。</li><li>然後，將使用事件中樞的 IoT 中樞設定為自訂端點，以使用以身分識別為基礎的驗證。</li></ul>
