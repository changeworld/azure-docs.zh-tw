---
title: '適用于 Azure Cosmos DB Core (SQL API 的 Azure Resource Manager 範本) '
description: 使用 Azure Resource Manager 範本建立和設定 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 3247f35fec8615b9c5e277b7453c340caaf56ffd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283970"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure Resource Manager 範本

本文僅顯示 Core (SQL) API 帳戶的 Azure Resource Manager 範本範例。 您也可以尋找 [Cassandra](templates-samples-cassandra.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](templates-samples-mongodb.md) 和[資料表](templates-samples-table.md) API 的範本範例。

## <a name="core-sql-api"></a>Core (SQL) API

|**範本**|**說明**|
|---|---|
|[建立具有自動調整輸送量的 Azure Cosmos 帳戶、資料庫和容器](manage-with-templates.md#create-autoscale) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及具有自動調整輸送量的資料庫和容器。 |
|[建立具有分析存放區的 Azure Cosmos 帳戶、資料庫和容器](manage-with-templates.md#create-analytical-store) | 此範本會在一個區域中建立 Core (SQL) API 帳戶，以及已啟用並設定分析 TTL、並且有選項可使用手動或自動調整輸送量的容器。 |
|[建立具有標準 (手動) 輸送量的 Azure Cosmos 帳戶、資料庫和容器](manage-with-templates.md#create-manual) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及具有標準輸送量的資料庫和容器。 |
|[建立具有預存程序、觸發程序和 UDF 的 Azure Cosmos 帳戶、資料庫和容器](manage-with-templates.md#create-sproc) | 此範本會在兩個區域中建立 Core (SQL) API 帳戶，以及容器的預存程序、觸發程序和 UDF。 |
|[為現有的 Azure Cosmos 帳戶建立私人端點](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此範本會在現有的虛擬網路中，為現有的 Azure Cosmos Core (SQL) API 帳戶建立私人端點。 |
|[建立免費層的 Azure Cosmos 帳戶](manage-with-templates.md#free-tier) |  此範本會在免費層上建立 Azure Cosmos DB Core (SQL) API 帳戶。 |

如需參考文件，請參閱 [Azure Cosmos DB 的 Azure Resource Manager 參考](/azure/templates/microsoft.documentdb/allversions)頁面。
