---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2020
ms.locfileid: "90068725"
---
**我如何獲知 SDK 將要停用？**

Microsoft 會在停用 SDK 支援結束的 12 個月前事先通知，以利順暢轉換至支援的 SDK。 我們會透過各種通訊管道通知您：Azure 入口網站、Azure 更新，以及直接與受指派服務系統管理員通訊。

**在 12 個月的期間內，我是否可以使用「即將」停用的 Azure Cosmos DB SDK 來撰寫應用程式？** 

可以，在 12 個月的通知期內，您可以使用即將停用的 Azure Cosmos DB SDK 來完整撰寫、部署及修改應用程式。 建議您在 12 個月的通知期內，視情況遷移至較新支援的 Azure Cosmos DB SDK 版本。 

**在停用日期之後，使用不受支援的 Azure Cosmos DB SDK 的應用程式會發生什麼情況？** 

在停用日期之後，Azure Cosmos DB 不再進行錯誤 (bug) 修正、新增功能，或對已停用的 SDK 版本提供支援。 如果您不想升級，從已停用的 SDK 版本傳送的要求將會繼續由 Azure Cosmos DB 服務提供服務。 

**哪些 SDK 版本會有最新的功能和更新？**

新的功能和更新只會新增至最新支援主要 SDK 版本的最新次要版本。 我們建議您一律使用最新版本，以利用新功能、效能改進和錯誤 (bug) 修正。 如果您使用的是未停用的舊版 SDK，您的 Azure Cosmos DB 要求仍會執行，但是您無法存取任何新功能。  

**如果我在截止日前無法更新應用程式，該怎麼辦？**

我們建議您盡早升級至最新的 SDK。 在 SDK 標記為停用之後，您有 12 個月的時間可以更新應用程式。 如果您無法在停用日期前進行更新，從已停用的 SDK 版本傳送的要求會繼續由 Azure Cosmos DB 提供服務，因此您執行中的應用程式會繼續運作。 但是 Azure Cosmos DB 不再進行錯誤 (bug) 修正、新增功能，或對已停用的 SDK 版本提供支援。 

如果您有支援方案並需要技術支援，請藉由提出支援票證來[與我們連絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
    


