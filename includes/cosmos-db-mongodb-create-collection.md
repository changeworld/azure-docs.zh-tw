---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 8d12645bd302a7ea808f13c07e2a72f66846bad7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2020
ms.locfileid: "85121341"
---
您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立適用於 MongoDB 資料庫和容器的 Azure Cosmos DB API。 

1. 選取 [資料總管] > [新增容器]。 
    
    [新增容器] 區域會顯示在最右邊，您可能需要向右捲動才能看到它。

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Azure 入口網站資料總管，[新增容器] 窗格":::

2. 在 [新增容器] 頁面上，輸入新容器的設定。

    |設定|建議的值|描述
    |---|---|---|
    |**資料庫識別碼**|db|輸入 db 做為新資料庫的名稱。 資料庫名稱必須包含從 1 到 255 個字元，且不能包含 `/, \\, #, ?` 或尾端空格。 核取 [佈建資料庫輸送量] 選項，它可讓您在資料庫中的所有容器內共用佈建到資料庫的輸送量。 此選項也有助於節省成本。 |
    |**輸送量**|400|讓輸送量保持在每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以擴大輸送量。 您也可以選擇[自動調整模式](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale)，這會提供您一系列視需要動態增加和減少的 RU/秒。| 
    |**集合識別碼**|coll|輸入 `coll` 來為您的新容器命名。 容器識別碼與資料庫名稱具有相同的字元需求。|
    |**儲存體容量**|固定 (10GB)|輸入此應用程式的「固定 (10GB)」。 如果您選取「無限制」，就必須建立所有插入項目都需要的 `Shard Key`。|
    |**分區金鑰**| /_id| 本文所述的範例不會使用分區金鑰，因此將其設定為 /_id 會使用 [自動產生的識別碼] 欄位做為分區金鑰。 若要深入了解分區化 (也稱為資料分割)，請參閱[在 Azure Cosmos DB 中進行資料分割](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    選取 [確定]。 [資料總管] 會顯示新的資料庫和容器。
