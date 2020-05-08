---
title: 適用於 Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791624"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

下表包含 Azure Cosmos DB Azure Resource Manager 範本的連結：

## <a name="core-sql-api"></a>Core (SQL) API

|**範本**|**說明**|
|---|---|
|[建立 Azure Cosmos 帳戶、資料庫、具有自動調整輸送量的容器](manage-sql-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立核心（SQL） API 帳戶，這是具有自動調整輸送量的資料庫和容器。 |
|[建立 Azure Cosmos 帳戶、資料庫、具有標準（手動）輸送量的容器](manage-sql-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立核心（SQL） API 帳戶，這是具有標準輸送量的資料庫和容器。 |
|[使用預存程式、觸發程式和 UDF 建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本會在兩個區域中建立核心（SQL） API 帳戶，其中包含容器的預存程式、觸發程式和 UDF。 |
|[為現有的 Azure Cosmos 帳戶建立私人端點](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此範本會在現有的虛擬網路中建立現有 Azure Cosmos Core （SQL） API 帳戶的私人端點。 |
|[建立免費層的 Azure Cosmos 帳戶](manage-sql-with-resource-manager.md#free-tier) |  此範本會在免費層上建立 Azure Cosmos DB Core （SQL） API 帳戶。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、資料庫、集合](manage-mongodb-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中使用適用于 MongoDB 的 Azure Cosmos DB API 建立帳戶，其中包含兩個共用資料庫層級自動調整輸送量的容器。 |
|[建立 Azure Cosmos 帳戶、資料庫、具有標準（手動）輸送量的集合](manage-mongodb-with-resource-manager.md#create-manual) | 此範本會在兩個區域中使用適用于 MongoDB 的 Azure Cosmos DB API 建立帳戶，其中包含兩個共用資料庫層級標準輸送量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、keyspace、資料表](manage-cassandra-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立 Cassandra API 帳戶，其中具有具有自動調整輸送量的 keyspace 和資料表。 |
|[建立 Azure Cosmos 帳戶，keyspace，具有標準（手動）輸送量的資料表](manage-cassandra-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立一個 Cassandra API 帳戶，其中具有具有手動輸送量的 keyspace 和資料表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、資料庫、具有自動調整輸送量的圖形](manage-gremlin-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立 Gremlin API 帳戶，其中具有具有自動調整輸送量的資料庫和圖形。 |
|[建立 Azure Cosmos 帳戶、資料庫、具有標準（手動）輸送量的圖形](manage-gremlin-with-resource-manager.md#create-manual) | 此範本會在兩個區域中建立 Gremlin API 帳戶，其中具有具有標準輸送量的資料庫和圖形。 |

## <a name="table-api"></a>資料表 API

|**範本**|**說明**|
|---| ---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶和資料表](manage-table-with-resource-manager.md#create-autoscale) | 此範本會在兩個區域中建立一個資料表 API 帳戶，以及一個具有自動調整輸送量的單一資料表。 |
|[建立 Azure Cosmos 帳戶，具有標準（手動）輸送量的資料表](manage-table-with-resource-manager.md#create-manual) | 此範本會建立兩個區域中的資料表 API 帳戶，以及具有標準輸送量的單一資料表。 |

如需參考檔，請參閱 Azure Cosmos DB 頁面的[Azure Resource Manager 參考](/azure/templates/microsoft.documentdb/allversions)。
