---
title: 瞭解如何將 Azure Cosmos DB 帳戶移至另一個區域
description: 瞭解如何將 Azure Cosmos DB 帳戶移至另一個區域
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059246"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>如何將 Azure Cosmos DB 帳戶移至另一個區域

本文描述如何移動 Azure Cosmos DB 中複寫資料的區域，或如何將帳戶 (Azure Resource Manager) 中繼資料以及將資料從某個區域移至另一個區域。

## <a name="move-data-from-one-region-to-another"></a>將資料從一個區域移至另一個區域

Azure Cosmos DB 原本就支援資料複寫，因此將資料從某個區域移至另一個區域很簡單，並且可以使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 完成，並包含下列步驟：

1. 將新區域新增至帳戶

    若要將新區域新增至 Azure Cosmos DB 帳戶，請參閱 [將區域新增/移除至 Azure Cosmos DB 帳戶](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. 執行手動容錯移轉至新的區域

    如果要移除的區域目前是帳戶的寫入區域，您將需要起始容錯移轉至上述新增的區域。 這是零停機時間。 如果您要移動多區域帳戶中的讀取區域，則可以略過此步驟。 若要起始容錯移轉，請參閱 [在 Azure Cosmos 帳戶上執行手動容錯移轉](how-to-manage-database-account.md#manual-failover)

1. 移除原始區域

    若要從 Azure Cosmos DB 帳戶移除區域，請參閱 [將區域新增/移除至 Azure Cosmos DB 帳戶](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>遷移 Azure Cosmos DB 帳戶中繼資料

Azure Cosmos DB 原本就不支援將帳戶中繼資料從一個區域遷移至另一個區域。 若要將帳戶中繼資料和客戶資料從一個區域遷移至另一個區域，您必須在所需的區域中建立新的帳戶，然後必須手動複製資料。 SQL API 近乎零的停機時間遷移需要使用 [ChangeFeed](change-feed.md) 或運用它的工具。 如果遷移 MongoDB API、Cassandra 或其他 API，或若要深入瞭解在帳戶之間遷移資料時的選項，請參閱 [將內部部署或雲端資料移轉至 Azure Cosmos DB 的選項](cosmosdb-migrationchoices.md)。 下列步驟示範如何將 SQL API 的 Azure Cosmos DB 帳戶和其資料從一個區域遷移至另一個區域：

1. 在所需的區域中建立新的 Azure Cosmos DB 帳戶

    若要透過 Azure 入口網站、PowerShell 或 CLI 建立新帳戶，請參閱 [建立 Azure Cosmos DB 帳戶](how-to-manage-database-account.md#create-an-account)。

1. 建立新的資料庫和容器

    若要建立新的資料庫和容器，請參閱 [建立 Azure Cosmos 容器](how-to-create-container.md)

1. 使用 Azure Cosmos DB 即時資料移轉工具工具來遷移資料

    若要遷移近乎零停機時間的資料，請參閱 [Azure Cosmos DB 即時資料移轉工具工具](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. 更新應用程式連接字串

    當即時移轉工具仍在執行時，請更新應用程式新部署中的連接資訊。 您可以從 Azure 入口網站取出應用程式的端點和金鑰。

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure 入口網站中的存取控制 (IAM) - 示範 NoSQL 資料庫安全性":::

1. 將要求重新導向至新的應用程式

    當新的應用程式連接到 Azure Cosmos DB 您可以將用戶端要求重新導向至新的部署。

1. 刪除任何不再需要的資源

    由於要求現在已完全重新導向至新的實例，因此您可以刪除舊的 Azure Cosmos DB 帳戶和即時資料移轉工具。

## <a name="next-steps"></a>後續步驟

如需關於如何管理 Azure Cosmos 帳戶以及資料庫和容器的詳細資訊和範例，閱讀下列文章：

* [管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [Azure Cosmos DB 中的變更摘要](change-feed.md)
