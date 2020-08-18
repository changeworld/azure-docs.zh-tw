---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515293"
---
**1.SDK 將要停用時，將如何通知客戶？**

Microsoft 會提供12個月的事先通知，讓您終止對淘汰 SDK 的支援，以便順利轉換至支援的 SDK。 此外，客戶將透過各種不同的通道通知，Azure 入口網站、Azure 更新，以及與指派的服務系統管理員的直接通訊。

**2. 在12個月的期間內，客戶是否可以使用「即將」淘汰的 Azure Cosmos DB SDK 來撰寫應用程式？** 

可以，在12個月的通知期間內，客戶可以使用「即將」淘汰的 Azure Cosmos DB SDK 來撰寫、部署及修改應用程式。 在12個月的通知期間，建議客戶視需要遷移至較新支援的 Azure Cosmos DB SDK 版本。 

**3. 在淘汰日期之後，使用不受支援之 Azure Cosmos DB SDK 的應用程式會發生什麼事？** 

淘汰日期之後，Azure Cosmos DB 將不會再進行錯誤修正、新增功能，以及提供對已淘汰 SDK 版本的支援。 如果您不想要升級，Azure Cosmos DB 服務會繼續提供從已淘汰版本的 SDK 傳送的要求。 

**4. 哪些 SDK 版本會有最新的功能和更新？**

新功能和更新僅會新增至最新支援之主要 SDK 版本的最新次要版本。 建議您一律使用最新版本，以利用新功能、效能改善和 bug 修正。 如果您使用舊版、非淘汰版本的 SDK，您 Azure Cosmos DB 的要求仍會運作，但您將無法存取任何新功能。  

**5. 如果我無法在截止日期之前更新應用程式，該怎麼辦？**

我們建議您盡早升級至最新的 SDK。 一旦將 SDK 標記為停用，您將會有12個月的時間來更新您的應用程式。 如果您無法在停用日期前進行更新，則會繼續 Azure Cosmos DB 從已停用的 SDK 版本傳送的要求，讓您的執行中應用程式仍能正常運作。 不過，Azure Cosmos DB 將不再進行錯誤修正、新增功能，以及提供對已淘汰 SDK 版本的支援。 

如果您有支援方案並需要技術支援，請提出支援票證來 [與我們聯絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 。
    


