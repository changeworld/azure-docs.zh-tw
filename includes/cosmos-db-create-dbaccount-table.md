---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5015371afc67574a214097f8d3eef661df29c22f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85115028"
---
1. 在新的瀏覽器視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在左側功能表中，選取 [建立資源]。
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="在 Azure 入口網站中建立資源":::
   
3. 在 [新增] 頁面上，選取 [資料庫] > [Azure Cosmos DB]。
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="在 Azure 入口網站中建立資源":::
   
3. 在 [建立 Azure Cosmos DB 帳戶] 頁面上，輸入新 Azure Cosmos DB 帳戶的設定。 
 
    設定|值|描述
    ---|---|---
    訂用帳戶|您的訂用帳戶|選取要用於此 Azure Cosmos DB 帳戶的 Azure 訂用帳戶。 
    資源群組|選取 [建立新的]，然後按選取帳戶名稱|選取 [建立新的]。 然後為您的帳戶輸入新的資源群組名稱。 為求簡化，請使用與 Azure Cosmos DB 帳戶名稱相同的名稱。 
    帳戶名稱|唯一的名稱|輸入唯一名稱來識別您的 Azure Cosmos DB 帳戶。<br><br>帳戶名稱只能使用小寫字母、數字和連字號 (-)，且長度必須介於 3 到 31 個字元之間。
    API|Table|API 會決定要建立的帳戶類型。 Azure Cosmos DB 提供五個 API：Core(SQL) (適用於文件資料庫)、Gremlin (適用於圖形資料庫)、MongoDB (適用於文件資料庫)、Azure 資料表及 Cassandra。 您必須為每個 API 建立個別帳戶。 <br><br>選取 [Azure 資料表]，因為在此本快速入門中，您會建立可搭配資料表 API 使用的資料表。 <br><br>[深入了解資料表 API](../articles/cosmos-db/table-introduction.md)。|
    Location|最接近使用者的區域|選取用來裝載 Azure Cosmos DB 帳戶的地理位置。 使用最接近使用者的位置，以便他們能以最快速度存取資料。

    您可以將 [異地備援] 和 [多重區域寫入] 選項保留為 [停用]，以避免產生額外的費用，然後略過 [網路] 和 [標記] 區段。

5. 請選取 [檢閱 + 建立]。 驗證完成之後，請選取 [建立] 來建立帳戶。 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="在 Azure 入口網站中建立資源":::

6. 建立帳戶需要幾分鐘的時間。 您將會看到一個訊息，指出 [您的部署正在進行]。 等待部署完成，然後選取 [前往資源]。

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="在 Azure 入口網站中建立資源":::

