---
title: DTU 資源限制單一資料庫
description: 此頁面說明 Azure SQL Database 中單一資料庫的一些常見 DTU 資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: fa15a5f95c2163309ecfd9cc0130d1860eb3b634
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542682"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>使用 DTU 購買模型的單一資料庫資源限制-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文提供使用 DTU 購買模型 Azure SQL Database 單一資料庫的詳細資源限制。

如需彈性集區的 DTU 購買模型資源限制，請參閱[DTU 資源限制-彈性](resource-limits-dtu-elastic-pools.md)集區。 如需 vCore 資源限制，請參閱[vCore 資源限制-單一資料庫](resource-limits-vcore-single-databases.md)和[vCore 資源限制-彈性](resource-limits-vcore-elastic-pools.md)集區。 如需不同購買模型的詳細資訊，請參閱[購買模型和服務層](purchasing-models.md)。

## <a name="single-database-storage-sizes-and-compute-sizes"></a>單一資料庫：儲存體大小與計算大小

下表顯示單一資料庫在每個服務層級和計算大小的可用資源。 您可以使用 [Azure 入口網站](single-database-manage.md#the-azure-portal)、[Transact-SQL](single-database-manage.md#transact-sql-t-sql)、[PowerShell](single-database-manage.md#powershell)、[Azure CLI](single-database-manage.md#the-azure-cli) 或 [REST API](single-database-manage.md#rest-api) 來為單一資料庫設定服務層、計算大小與儲存體數量。

> [!IMPORTANT]
> 如需調整指引和考慮，請參閱[調整單一資料庫](single-database-scale.md)

### <a name="basic-service-tier"></a>基本服務層級

| **計算大小** | **基本** |
| :--- | --: |
| 最大 DTU | 5 |
| 內含儲存體 (GB) | 2 |
| 儲存體上限（GB） | 2 |
| OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 並行背景工作 (要求) 數上限 | 30 |
| 並行工作階段數上限 | 300 |
|||

> [!IMPORTANT]
> 基本服務層級提供少於一個 vCore （CPU）。  針對需要大量 CPU 的工作負載，建議使用 S3 或更高的服務層。
>
>關於資料儲存體，基本服務層會放在標準分頁 Blob 上。 標準分頁 Blob 使用硬碟（HDD）為基礎的存放裝置媒體，最適合用於開發、測試及其他較不常存取效能變化的工作負載。
>

### <a name="standard-service-tier"></a>標準服務層級

| **計算大小** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 最大 DTU | 10 | 20 | 50 | 100 |
| 內含存放裝置（GB） <sup>1</sup> | 250 | 250 | 250 | 250 |
| 儲存體上限（GB） | 250 | 250 | 250 | 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |
| 並行背景工作 (要求) 數上限| 60 | 90 | 120 | 200 |
| 並行工作階段數上限 |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup>如需因布建任何額外的儲存體而產生的額外成本詳細資料，請參閱[SQL Database 定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/)。

> [!IMPORTANT]
> 標準 S0、S1 和 S2 層提供少於一個 vCore （CPU）。  針對需要大量 CPU 的工作負載，建議使用 S3 或更高的服務層。
>
>關於資料儲存，標準 S0 和 S1 服務層會放在標準分頁 Blob 上。 標準分頁 Blob 使用硬碟（HDD）為基礎的存放裝置媒體，最適合用於開發、測試及其他較不常存取效能變化的工作負載。
>

### <a name="standard-service-tier-continued"></a>標準服務層級 (續)

| **計算大小** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 內含存放裝置（GB） <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| 儲存體上限（GB） | 1024 | 1024 | 1024 | 1024 | 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |N/A |
| 並行背景工作 (要求) 數上限| 400 | 800 | 1600 | 3200 |6000 |
| 並行工作階段數上限 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup>如需因布建任何額外的儲存體而產生的額外成本詳細資料，請參閱[SQL Database 定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/)。

### <a name="premium-service-tier"></a>高階服務層級

| **計算大小** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 內含存放裝置（GB） <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 儲存體上限（GB） | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| OLTP 記憶體內部儲存體上限 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 並行背景工作 (要求) 數上限| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| 並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup>如需因布建任何額外的儲存體而產生的額外成本詳細資料，請參閱[SQL Database 定價選項](https://azure.microsoft.com/pricing/details/sql-database/single/)。

<sup>2</sup>從 1024 gb 到 4096 gb，以 256 GB 為單位遞增。

> [!IMPORTANT]
> 所有區域目前均可取得進階層中超過 1 TB 的儲存空間，但不包括：中國東部、中國北部、德國中部和德國東北部。 在這些區域中，進階層中的儲存空間上限為 1 TB。  如需詳細資訊，請參閱 [P11-P15 目前的限制](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。
> [!NOTE]
> 如需 `tempdb` 限制，請參閱[tempdb 限制](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫的 vCore 資源限制，請參閱[使用 vCore 購買模型的單一資料庫資源限制](resource-limits-vcore-single-databases.md)
- 如需彈性集區的 vCore 資源限制，請參閱[使用 vCore 購買模型的彈性集區資源限制](resource-limits-vcore-elastic-pools.md)
- 如需彈性集區的 DTU 資源限制，請參閱[使用 dtu 購買模型的彈性集區資源限制](resource-limits-dtu-elastic-pools.md)
- 如需 Azure SQL 受控執行個體中受控實例的資源限制，請參閱[SQL 受控執行個體資源限制](../managed-instance/resource-limits.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需有關邏輯 SQL server 上資源限制的資訊，請參閱[邏輯 sql server 上的資源限制總覽](resource-limits-logical-server.md)，以取得有關伺服器和訂用帳戶層級的限制資訊。
