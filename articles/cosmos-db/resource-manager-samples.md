---
title: 適用於 Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure Resource Manager 範本來建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193253"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

下表包含 Azure Cosmos DB Azure Resource Manager 範本的連結：

## <a name="sql-core-api"></a>SQL (Core) API

|**範本**|**說明**|
|---|---|
|[建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-resource) | 此範本會在兩個區域中建立 SQL （核心） API 帳戶，其中包含兩個具有共用資料庫輸送量的容器，以及一個具有專用輸送量的容器。 藉由重新提交具有更新的輸送量屬性值的範本，即可更新輸送量。 |
|[使用預存程式、觸發程式和 UDF 建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本會在兩個區域中建立 SQL （核心） API 帳戶，其中包含容器的預存程式、觸發程式和 UDF。 |
|[為現有的 Azure Cosmos 帳戶建立私人端點](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此範本會在現有的虛擬網路中建立現有 Azure Cosmos SQL API 帳戶的私人端點。 |
|[建立免費層的 Azure Cosmos 帳戶](manage-sql-with-resource-manager.md#free-tier) |  此範本會在免費層上建立 Azure Cosmos DB 帳戶。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、資料庫、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此範本會使用適用于 MongoDB 的 Azure Cosmos DB API，在已啟用多宿主的兩個區域中建立帳戶。 Azure Cosmos 帳戶將會有兩個共用資料庫層級輸送量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、keyspace、資料表](manage-cassandra-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立 Cassandra API 帳戶。 Azure Cosmos 帳戶將會有兩個共用 keyspace 層級輸送量的資料表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶、資料庫、圖表](manage-gremlin-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立 Gremlin API 帳戶。 Azure Cosmos 帳戶將會有兩個共用資料庫層級輸送量的圖形。 |

## <a name="table-api"></a>資料表 API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳戶，資料表](manage-table-with-resource-manager.md#create-resource) | 此範本會在已啟用多宿主的兩個區域中建立資料表 API 帳戶。 Azure Cosmos 帳戶將會有單一資料表。 |

> [!TIP]
> 若要在使用資料表 API 時啟用共用輸送量，請在 Azure 入口網站中啟用帳戶層級輸送量。

如需參考檔，請參閱 Azure Cosmos DB 頁面的[Azure Resource Manager 參考](/azure/templates/microsoft.documentdb/allversions)。
