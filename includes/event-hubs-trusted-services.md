---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654430"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服務
當您啟用 [ **允許信任的 Microsoft 服務略過此防火牆** ] 設定時，系統會將您事件中樞資源的存取權授與下列服務。

| 信任的服務 | 支援的使用案例 | 
| --------------- | ------------------------- | 
| Azure Event Grid | 允許 Azure 事件方格將事件傳送至事件中樞命名空間中的事件中樞。 您也必須執行下列步驟： <ul><li>為主題或網域啟用系統指派的身分識別</li><li>將身分識別新增至事件中樞命名空間上的 Azure 事件中樞資料寄件者角色</li><li>然後，將使用事件中樞作為端點的事件訂用帳戶設定為使用系統指派的身分識別。</li></ul> <p>如需詳細資訊，請參閱 [使用受控識別傳遞事件](../articles/event-grid/managed-service-identity.md)</p>|
| Azure 監視器 (診斷設定)  | 允許 Azure 監視器將診斷資訊傳送至事件中樞命名空間中的事件中樞。 |