---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 837231ca07abcfdbd6ce932bb24bd890d91506a8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "69541444"
---
您現在可以在 Azure 入口網站中使用 [資料總管] 工具，建立圖形資料庫。 

1. 選取 [資料總管]   > [新增圖形]  。

    [新增圖形]  區域會顯示在最右邊，您可能需要向右捲動才會看到。

    ![Azure 入口網站資料總管 [新增圖形] 頁面](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. 在 [新增圖形]  頁面上，輸入新圖形的設定。

    設定|建議的值|描述
    ---|---|---
    資料庫識別碼|sample-database|輸入 *sample-database* 作為新資料庫的名稱。 資料庫名稱的長度必須介於 1 到 255 個字元，且不能包含 `/ \ # ?` 或尾端空格。
    Throughput|400 RU|將輸送量變更為每秒 400 個要求單位 (RU/秒)。 如果您想要降低延遲，稍後可以擴大輸送量。
    圖形識別碼|sample-graph|輸入 *sample-graph* 作為新集合的名稱。 圖形名稱與資料庫識別碼具有相同的字元需求。
    資料分割索引鍵| /pk |所有 Cosmos DB 帳戶都需要分割索引鍵，才能進行水準調整。 了解如何在[圖表資料分割](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)一文中選取適當的分割區索引鍵。

3. 填妥表單後，選取 [確定]  。
