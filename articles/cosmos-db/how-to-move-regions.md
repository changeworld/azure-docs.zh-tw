---
title: 將 Azure Cosmos DB 帳戶移至另一個區域
description: 瞭解如何將 Azure Cosmos DB 帳戶移至另一個區域。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 14839465c19908653e22fa80f634ef7e3fa28bd9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100145"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>將 Azure Cosmos DB 帳戶移至另一個區域
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何：

- 移動 Azure Cosmos DB 中複寫資料的區域。
- 將帳戶 (Azure Resource Manager) 中繼資料和資料從某個區域移至另一個區域。

## <a name="move-data-from-one-region-to-another"></a>將資料從一個區域移至另一個區域

Azure Cosmos DB 原本就支援資料複寫，因此將資料從某個區域移至另一個區域很簡單。 您可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 來完成這項工作。 它牽涉到下列步驟：

1. 將新區域新增至帳戶。

    若要將新區域新增至 Azure Cosmos DB 帳戶，請參閱 [新增/移除區域至 Azure Cosmos DB 帳戶](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。

1. 執行手動容錯移轉至新的區域。

    當要移除的區域目前是帳戶的寫入區域時，您必須開始容錯移轉到上一個步驟中新增的新區域。 這是零停機的作業。 如果您要移動多區域帳戶中的讀取區域，則可以略過此步驟。 
    
    若要開始進行容錯移轉，請參閱 [在 Azure Cosmos 帳戶上執行手動容錯移轉](how-to-manage-database-account.md#manual-failover)。

1. 移除原始區域。

    若要從 Azure Cosmos DB 帳戶移除區域，請參閱 [從您的 Azure Cosmos DB 帳戶新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。

## <a name="migrate-azure-cosmos-db-account-metadata"></a>遷移 Azure Cosmos DB 帳戶中繼資料

Azure Cosmos DB 原本就不支援將帳戶中繼資料從一個區域遷移至另一個區域。 若要將帳戶中繼資料和客戶資料從一個區域遷移至另一個區域，您必須在所需的區域中建立新的帳戶，然後手動複製資料。 

SQL API 的近乎零停機時間需要使用 [變更](change-feed.md) 摘要或使用它的工具。 如果您要遷移 MongoDB API、Cassandra API 或其他 API，或若要深入瞭解在帳戶之間遷移資料的選項，請參閱 [將內部部署或雲端資料移轉至 Azure Cosmos DB 的選項](cosmosdb-migrationchoices.md)。 

下列步驟示範如何將 SQL API 的 Azure Cosmos DB 帳戶和其資料從一個區域遷移至另一個區域：

1. 在所需的區域中建立新的 Azure Cosmos DB 帳戶。

    若要透過 Azure 入口網站、PowerShell 或 Azure CLI 建立新的帳戶，請參閱 [建立 Azure Cosmos DB 帳戶](how-to-manage-database-account.md#create-an-account)。

1. 建立新的資料庫和容器。

    若要建立新的資料庫和容器，請參閱 [建立 Azure Cosmos 容器](how-to-create-container.md)。

1. 使用 Azure Cosmos DB 即時資料移轉器工具來遷移資料。

    若要在接近零停機時間的情況下遷移資料，請參閱 [Azure Cosmos DB 即時資料移轉工具](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)。

1. 更新應用程式連接字串。

    當即時資料移轉程式工具仍在執行時，請更新應用程式新部署中的連接資訊。 您可以從 Azure 入口網站取出應用程式的端點和金鑰。

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 入口網站中的存取控制，示範 NoSQL 資料庫安全性。":::

1. 將要求重新導向至新的應用程式。

    當新的應用程式連接到 Azure Cosmos DB 之後，您就可以將用戶端要求重新導向至新的部署。

1. 刪除您不再需要的任何資源。

    由於要求現在已完全重新導向至新的實例，因此您可以刪除舊的 Azure Cosmos DB 帳戶和即時資料移轉工具。

## <a name="next-steps"></a>後續步驟

如需有關如何管理 Azure Cosmos 帳戶以及資料庫和容器的詳細資訊和範例，請參閱下列文章：

* [管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [Azure Cosmos DB 中的變更摘要](change-feed.md)
