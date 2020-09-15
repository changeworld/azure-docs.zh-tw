---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068725"
---
**如何收到即將淘汰 SDK 的通知？**

Microsoft 將在淘汰 SDK 結束支援之前，先提供12個月的事先通知，以協助順利轉換至支援的 SDK。 我們會透過各種通道通知您： Azure 入口網站、Azure 更新，以及指派服務系統管理員的直接通訊。

**我可以在12個月期間內使用即將淘汰的 Azure Cosmos DB SDK 來撰寫應用程式嗎？** 

是的，您將能夠在12個月的通知期間，使用已淘汰的 Azure Cosmos DB SDK 來撰寫、部署及修改應用程式。 建議您視需要在12個月的通知期間，遷移至較新支援的 Azure Cosmos DB SDK 版本。 

**淘汰日期之後，使用不支援的 Azure Cosmos DB SDK 的應用程式會發生什麼事？** 

淘汰日期之後，Azure Cosmos DB 將不再進行錯誤修正、新增功能，或提供對已淘汰 SDK 版本的支援。 如果您不想要升級，Azure Cosmos DB 服務會繼續提供從已淘汰版本的 SDK 傳送的要求。 

**哪些 SDK 版本會有最新的功能和更新？**

新功能和更新僅會新增至最新支援之主要 SDK 版本的最新次要版本。 建議您一律使用最新版本，以利用新功能、效能改進和 bug 修正。 如果您使用舊版的非淘汰版本的 SDK，您對 Azure Cosmos DB 的要求仍會運作，但您將無法存取任何新功能。  

**如果我無法在截止日期之前更新應用程式，該怎麼辦？**

我們建議您盡早升級至最新的 SDK。 將 SDK 標記為停用之後，您將會有12個月的時間來更新您的應用程式。 如果您無法在淘汰日期前進行更新，Azure Cosmos DB 將會繼續提供從已淘汰版本的 SDK 傳送的要求，讓您執行中的應用程式仍可正常運作。 但 Azure Cosmos DB 將不再進行錯誤修正、新增功能，或提供對已淘汰 SDK 版本的支援。 

如果您有支援方案並需要技術支援，請提出支援票證來 [與我們聯絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 。
    


