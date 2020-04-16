---
title: 適用於 Azure Cosmos DB 的 Azure Resource Manager 範本
description: 使用 Azure 資源管理器樣本建立和配置 Azure 宇宙資料庫。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390885"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

下表包含 Azure Cosmos DB 的 Azure 資源管理員樣本的連結:

## <a name="sql-core-api"></a>SQL (Core) API

|**範本**|**說明**|
|---|---|
|[建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建 SQL (Core) API 帳戶,其中兩個區域具有共享資料庫輸送量和一個具有專用輸送量的容器。 可以通過重新提交具有更新輸送量屬性值的範本來更新輸送量。 |
|[使用儲存程序、觸發器和 UDF 建立 Azure Cosmos 帳戶、資料庫和容器](manage-sql-with-resource-manager.md#create-sproc) | 此範本在具有儲存過程的兩個區域(容器的觸發器和 UDF)中創建 SQL (核心)API 帳戶。 |
|[為現有 Azure Cosmos 帳號建立專用終結點](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此範本為現有虛擬網路中的現有 Azure Cosmos SQL API 帳戶創建專用終結點。 |

## <a name="mongodb-api"></a>MongoDB API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳號、資料庫、集合](manage-mongodb-with-resource-manager.md#create-resource) | 此範本在啟用多主機的兩個區域中為 MongoDB 使用 Azure Cosmos DB API 創建帳戶。 Azure Cosmos 帳戶將有兩個共享資料庫級輸送量的容器。 |

## <a name="cassandra-api"></a>Cassandra API

|**範本**|**說明**|
|---| ---|
|[建立 Azure Cosmos 帳號、鍵空間、表](manage-cassandra-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個卡桑德拉 API 帳戶,並啟用多主機。 Azure Cosmos 帳戶將有兩個共用密鑰空間級輸送量的表。 |

## <a name="gremlin-api"></a>Gremlin API

|**範本**|**說明**|
|---| ---|
|[建立 Azure 宇宙帳戶、資料庫、圖形](manage-gremlin-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個 Gremlin API 帳戶,並啟用多主機。 Azure Cosmos 帳戶將有兩個共享資料庫級輸送量的圖形。 |

## <a name="table-api"></a>資料表 API

|**範本**|**說明**|
|---| ---|
|[建立 Azure 宇宙帳戶表](manage-table-with-resource-manager.md#create-resource) | 此範本在兩個區域中創建一個已啟用多主機的區域的表 API 帳戶。 Azure Cosmos 帳戶將具有一個表。 |

> [!TIP]
> 要在使用表 API 時啟用共用輸送量,請使用 Azure 門戶中啟用帳戶級輸送量。

有關參考文件,請參閱[Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)頁的 Azure 資源管理器參考。
