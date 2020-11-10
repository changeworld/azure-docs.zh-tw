---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426560"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務
當您啟用 [ **允許信任的 Microsoft 服務略過此防火牆** ] 設定時，系統會將您的服務匯流排資源存取權授與下列服務。

| 信任的服務 | 支援的使用案例 | 
| --------------- | ------------------------- | 
| Azure Event Grid | 允許 Azure 事件方格將事件傳送至服務匯流排命名空間中的佇列或主題。 您也必須執行下列步驟： <ul><li>為主題或網域啟用系統指派的身分識別</li><li>將身分識別新增至服務匯流排命名空間上的 Azure 服務匯流排資料寄件者角色</li><li>然後，將使用服務匯流排佇列或主題的事件訂閱設定為端點，以使用系統指派的身分識別。</li></ul> <p>如需詳細資訊，請參閱 [使用受控識別傳遞事件](../articles/event-grid/managed-service-identity.md)</p>|
