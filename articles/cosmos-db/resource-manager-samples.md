---
title: 適用於 Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586145"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

下表包含 Azure Cosmos DB 的 Azure App Service 範本連結：

## <a name="core-sql-api"></a>Core (SQL) API

|**範本**|**說明**|
|---|---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及具有自動調整輸送量的資料庫和容器。 |
|[建立具有分析存放區的 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-analytical-store) | 此範本會在一個區域中建立 Core (SQL) API 帳戶，以及已啟用並設定分析 TTL、並且有選項可使用手動或自動調整輸送量的容器。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及具有標準輸送量的資料庫和容器。 |
|[建立具有預存程序、觸發程序和 UDF 的 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及容器的預存程序、觸發程序和 UDF。 |
|[為現有的 Azure Cosmos 帳戶建立私人端點](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此範本會在現有的虛擬網路中，為現有的 Azure Cosmos Core (SQL) API 帳戶建立私人端點。 |
|[建立免費層的 Azure Cosmos 帳戶](manage-sql-with-resource-manager.md#free-tier) |  此範本會在免費層上建立 Azure Cosmos DB Core (SQL) API 帳戶。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、資料庫和集合](manage-mongodb-with-resource-manager.md#create-autoscale) | 此範本會使用適用於 MongoDB 的 Azure Cosmos DB API 在兩個區域中建立帳戶，以及兩個共用資料庫層級自動調整輸送量的容器。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶、資料庫和集合](manage-mongodb-with-resource-manager.md#create-manual) | 此範本會使用適用於 MongoDB 的 Azure Cosmos DB API 在兩個區域中建立帳戶，以及兩個共用資料庫層級標準輸送量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、keyspace 和資料表](manage-cassandra-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立 Cassandra API 帳戶，以及具有自動調整輸送量的 keyspace 和資料表。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶、keyspace 和資料表](manage-cassandra-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立 Cassandra API 帳戶，以及具有手動輸送量的 keyspace 和資料表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、資料庫和圖表](manage-gremlin-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立 Gremlin API 帳戶，以及具有自動調整輸送量的資料庫和圖表。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶、資料庫和圖表](manage-gremlin-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立 Gremlin API 帳戶，以及具有標準輸送量的資料庫和圖表。 |

## <a name="table-api"></a>資料表 API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶和資料表](manage-table-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立資料表 API 帳戶，以及具有自動調整輸送量的單一資料表。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶和資料表](manage-table-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立資料表 API 帳戶，以及具有標準輸送量的單一資料表。 |

如需參考文件，請參閱 [Azure Cosmos DB 的 Azure Resource Manager 參考](/azure/templates/microsoft.documentdb/allversions)頁面。
