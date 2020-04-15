---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 408fb2c40e645d9a8b10f1e04d282e134c5489a1
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80502050"
---
您可以在 [資料總管] 中，使用查詢來擷取和篩選您的資料。

1. 在 [資料總管] 中的 [項目]  索引標籤頂端，檢閱預設查詢 `SELECT * FROM c`。 此查詢會依照識別碼順序擷取並顯示容器中的所有文件。 
   
   ![[資料總管] 中的預設查詢為 `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. 若要變更查詢，請選取 [編輯篩選條件]  ，以 `ORDER BY c._ts DESC` 取代預設查詢，然後選取 [套用篩選條件]  。
   
   ![新增 ORDER BY c._ts DESC 並按一下 [套用篩選]，以變更預設查詢](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   經過修改的查詢會根據文件的時間戳記，依遞減順序顯示文件，因此現會最先列出您的第二份文件。 
   
   ![已將查詢變更為 ORDER BY c._ts DESC 並按一下 [套用篩選]](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

若您熟悉 SQL 語法，則可在查詢述詞方塊中輸入任何支援的 [SQL 查詢](../articles/cosmos-db/sql-api-sql-query.md)。 您也可以使用 [資料總管] 來建立伺服器端商務邏輯的預存程序、UDF 和觸發程序。 

[資料總管] 可讓您透過 Azure 入口網站輕鬆存取 API 中所有可用的內建程式設計資料存取功能。 您也可以使用入口網站來調整輸送量、取得金鑰和連接字串，以及檢閱 Azure Cosmos DB 帳戶的計量和 SLA。 

