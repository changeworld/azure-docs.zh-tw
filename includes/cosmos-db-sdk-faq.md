---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173730"
---
**1.SDK 將要停用時，將如何通知客戶？**

Microsoft 會在停用 SDK 支援結束的 12 個月前事先通知，以利順暢轉換至支援的 SDK。 此外，客戶會透過多種通訊管道收到通知，包括 Azure 管理入口網站、開發人員中心、部落格文章，以及直接連絡指派的服務管理員。

**2. 客戶是否可以在12個月期間，使用「已淘汰」 Azure Cosmos DB SDK 來撰寫應用程式？** 

可以，在 12 個月的寬限期內，客戶可以使用「即將」停用的 Azure Cosmos DB SDK 來完整撰寫、部署及修改應用程式。 仍建議客戶在 12 個月的寬限期內，視情況移轉至較新的 Azure Cosmos DB SDK 支援版本。

**3. 客戶是否可以在12個月的通知期間之後，使用已淘汰的 Azure Cosmos DB SDK 來撰寫及修改應用程式？**

SDK 在 12 個月的通知期後將會停用， 屆時 Azure Cosmos DB 平台將不允許應用程式使用停用的 SDK 來存取 Azure Cosmos DB。 此外，Microsoft 不會為已停用的 SDK，提供客戶支援服務。

**4. 客戶執行的應用程式使用不支援的 Azure Cosmos DB SDK 版本時，會發生什麼事？**

任何以停用 SDK 版本來連線 Azure Cosmos DB service 服務的嘗試都會被拒絕。 

**5. 是否將新功能和功能套用至所有未淘汰的 Sdk？**

新特性和功能只會新增到新版本。 如果您使用的是未停用的舊版 SDK，您的 Azure Cosmos DB 要求仍會照舊執行，但是無法存取任何新功能。  

**6. 如果我無法在截止日期前更新我的應用程式，該怎麼辦？**

我們建議您盡早升級至最新的 SDK。 在 SDK 標記為停用之後，您有 12 個月的時間可以更新應用程式。 如果您基於任何理由，而無法在此期間內完成應用程式更新，請在截止日前連絡 [Cosmos DB 團隊](mailto:askcosmosdb@microsoft.com) 並要求協助。

