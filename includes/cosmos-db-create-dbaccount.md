---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: efdd4a065e1eab55f5af420585a44754d42a43e9
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503916"
---
1. 從 Azure 入口網站功能表或[首頁] 頁面，選取 [建立資源]。

1. 在 [新增] 頁面上，搜尋並選取 [Azure Cosmos DB]。

1. 在 [Azure Cosmos DB] 頁面上，選取 [建立]。

1. 在 [建立 Azure Cosmos DB 帳戶] 頁面上，輸入新 Azure Cosmos 帳戶的基本設定。 

    |設定|值|描述 |
    |---|---|---|
    |訂用帳戶|訂閱名稱|選取要用於此 Azure Cosmos 帳戶的 Azure 訂用帳戶。 |
    |資源群組|資源群組名稱|選取資源群組，或選取 [新建]，然後輸入新資源群組的唯一名稱。 |
    |帳戶名稱|唯一的名稱|輸入名稱來識別您的 Azure Cosmos 帳戶。 因為 documents.azure.com 會附加到您所提供的名稱以建立 URI，請使用唯一名稱。<br><br>名稱只能包含小寫字母、數字及連字號 (-) 字元。 其長度必須介於 3 到 44 個字元之間。|
    |API|要建立的帳戶類型|選取 [Core(SQL)]，以使用 SQL 語法建立文件資料庫並進行查詢。 <br><br>API 會決定要建立的帳戶類型。 Azure Cosmos DB 提供五個 API：Core(SQL) 和 MongoDB (適用於文件資料)、Gremlin (適用於圖形資料)、Azure 資料表及 Cassandra。 目前，您必須為每個 API 建立個別個帳戶。 |
    |容量模式|佈建輸送量或無伺服器|選取 [ **佈建的輸送量** ]，以 [ [佈建的輸送量](../articles/cosmos-db/set-throughput.md)] 模式建立帳戶。 選取 [ **無伺服器** ]，以 [ [無伺服器](../articles/cosmos-db/serverless.md)] 模式建立帳戶。|
    |申請免費層折扣|適用或不適用|使用 Azure Cosmos DB 免費層，您將可在帳戶中免費取得前 400 RU/秒和 5 GB 的儲存體。 深入了解[免費層](https://azure.microsoft.com/pricing/details/cosmos-db/)。|
    |Location|最接近使用者的區域|選取用來裝載 Azure Cosmos DB 帳戶的地理位置。 使用最接近使用者的位置，讓他們能以最快速度存取資料。|
    |帳戶類型|生產或非生產|如果帳戶將用於生產工作負載，請選取 [生產]。 如果帳戶將用於非生產工作負載 (例如開發、測試、QA 或預備)，請選取 [非生產]。 這是一種 Azure 資源標記設定，可調整入口網站體驗，但不會影響基礎 Azure Cosmos DB 帳戶。 您可以隨時變更此值。|

    > [!NOTE]
    > 每個 Azure 訂用帳戶最多可以有一個免費層的 Azure Cosmos DB 帳戶，而且必須在建立帳戶時選擇加入。 如果您看不到套用免費層折扣的選項，這表示訂用帳戶中的另一個帳戶已透過免費層啟用。
   
    > [!NOTE]
    > 如果您選取 [無伺服器] 作為 **容量模式** ，則無法使用下列選項：
    > - 申請免費層折扣
    > - 異地備援
    > - 多區域寫入
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Azure Cosmos DB 的新帳戶頁面":::

1. 選取 [檢閱 + 建立]。 您可以略過 [網路] 和 [標記] 區段。

1. 檢閱帳戶設定，然後選取 [建立]。 建立帳戶需要幾分鐘的時間。 等候入口網站頁面顯示 [您的部署已完成] 訊息。 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Azure 入口網站的 [通知] 窗格":::

1. 選取 [移至資源] 以移至 Azure Cosmos DB 帳戶頁面。 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Azure Cosmos DB 帳戶頁面":::
