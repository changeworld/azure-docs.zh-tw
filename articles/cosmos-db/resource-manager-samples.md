---
title: 適用於 Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure 資源管理器範本創建和配置 Azure 宇宙資料庫。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961846"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

下表包括指向 Azure Cosmos DB 的 Azure 資源管理器範本的連結：

## <a name="sql-core-api"></a>SQL (Core) API

|**範本**|**描述**|
|---|---|
|[建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建 SQL （Core） API 帳戶，其中兩個區域具有共用資料庫輸送量和一個具有專用輸送量的容器。 可以通過重新提交具有更新輸送量屬性值的範本來更新輸送量。 |
|[使用預存程序、觸發器和 UDF 創建 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本在具有預存程序的兩個區域（容器的觸發器和 UDF）中創建 SQL （核心）API 帳戶。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**描述**|
|---| ---|
|[創建 Azure Cosmos 帳戶、資料庫、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此範本在啟用多主機的兩個區域中為 MongoDB 使用 Azure Cosmos DB API 創建帳戶。 Azure Cosmos 帳戶將有兩個共用資料庫級輸送量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**描述**|
|---| ---|
|[創建 Azure Cosmos 帳戶、鍵空間、表](manage-cassandra-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個卡珊多拉 API 帳戶，並啟用多主機。 Azure Cosmos 帳戶將有兩個共用金鑰空間級輸送量的表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**描述**|
|---| ---|
|[創建 Azure 宇宙帳戶、資料庫、圖形](manage-gremlin-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個 Gremlin API 帳戶，並啟用多主機。 Azure Cosmos 帳戶將有兩個共用資料庫級輸送量的圖形。 |

## <a name="table-api"></a>資料表 API

|**範本**|**描述**|
|---| ---|
|[創建 Azure 宇宙帳戶表](manage-table-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個已啟用多主機的區域的表 API 帳戶。 Azure Cosmos 帳戶將具有一個表。 |

> [!TIP]
> 要在使用表 API 時啟用共用輸送量，請使用 Azure 門戶中啟用帳戶級輸送量。

有關參考文檔，請參閱[Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)頁的 Azure 資源管理器參考。
