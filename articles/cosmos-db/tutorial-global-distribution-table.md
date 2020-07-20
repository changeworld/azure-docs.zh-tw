---
title: 適用於資料表 API 的 Azure Cosmos DB 全域散發教學課程
description: 了解 Azure Cosmos DB 資料表 API 帳戶中的全域散發如何運作，以及如何設定慣用的區域清單
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "76900191"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>使用資料表 API 來設定 Azure Cosmos DB 全域散發

本文涵蓋下列工作： 

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用[資料表 API](table-introduction.md) 來設定全域散發

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用資料表 API 來連線到慣用的區域

為了充分利用[全域散發](distribute-data-globally.md)，用戶端應用程式應指定其應用程式目前執行所在的位置。 此動作可藉由設定 `CosmosExecutorConfiguration.CurrentRegion` 屬性來完成。 `CurrentRegion` 屬性應包含單一位置。 每個用戶端執行個體都可指定本身的區域，以利進行低延遲讀取。 這類區域必須以其[顯示名稱](https://msdn.microsoft.com/library/azure/gg441293.aspx)命名，例如「美國西部」。 

Azure Cosmos DB 資料表 API SDK 會根據帳戶組態和目前的區域可用性，自動挑選最適合通訊的端點。 它會排定最近區域的優先順序，為用戶端提供較短的延遲性。 設定目前的 `CurrentRegion` 屬性之後，會依照下列方式導入讀取和寫入要求：

* **讀取要求：** 所有讀取要求都會傳送至已設定的 `CurrentRegion`。 根據鄰近性，SDK 會自動選取高可用性所需的後援異地複寫區域。

* **寫入要求：** SDK 會自動將所有寫入要求傳送至目前的寫入區域。 在多重主機帳戶中，目前的區域也會處理寫入要求。 根據鄰近性，SDK 會自動選取高可用性所需的後援異地複寫區域。

若未指定 `CurrentRegion` 屬性，SDK 將會對所有作業使用目前的寫入區域。

例如，假設 Azure Cosmos 帳戶位於「美國西部」和「美國東部」區域。 如果「美國西部」是寫入區域，且應用程式出現在「美國東部」。 若未設定 CurrentRegion 屬性，則所有的讀取和寫入要求一律會導向至「美國西部」區域。 若已設定 CurrentRegion 屬性，則會從「美國東部」區域處理所有讀取要求。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列操作：

> [!div class="checklist"]
> * 使用 Azure 入口網站來設定全域散發
> * 使用 Azure Cosmos DB 資料表 API 來設定全域散發

