---
title: vCore 資源限制-單一資料庫
description: 此頁面會針對 Azure SQL Database 中的單一資料庫，說明一些常見的 vCore 資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481060"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>使用 vCore 購買模型的單一資料庫資源限制

本文提供使用 vCore 購買模型 Azure SQL Database 單一資料庫的詳細資源限制。

如需 SQL Database 伺服器上單一資料庫的 DTU 購買模型限制，請參閱[SQL Database 伺服器上的資源限制總覽](sql-database-resource-limits-database-server.md)。

您可以使用 [Azure 入口網站](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、[Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases)、[PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)、[Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 或 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) 來為單一資料庫設定服務層、計算大小與儲存體數量。

> [!IMPORTANT]
> 如需調整指引和考慮，請參閱[調整單一資料庫](sql-database-single-database-scale.md)。

## <a name="general-purpose---serverless-compute---gen5"></a>一般用途-無伺服器計算-第5代

[無伺服器計算層](sql-database-serverless.md)目前僅適用于第5代硬體。

### <a name="gen5-compute-generation-part-1"></a>第5代計算世代（第1部分）

|計算大小|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|
|最小值-最大虛擬核心|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|最小值-最大記憶體（GB）|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|最小自動暫停延遲（分鐘）|60|60|60|60|60|
|資料行存放區支援|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|512|1024|1024|1024|1536|
|記錄大小上限 (GB)|154|307|307|307|461|
|TempDB 最大資料大小（GB）|32|64|128|192|256|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|320|640|1280|1920|2560|
|最大記錄速率（MBps）|3.8|7.5|15|22.5|30|
|並行背景工作 (要求) 數上限|75|150|300|450|600|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第5代計算世代（第2部分）

|計算大小|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|
|最小值-最大虛擬核心|1.25-10|1.50-12|1.75-14|2.00-16|
|最小值-最大記憶體（GB）|3.75-30|4.50-36|5.25-42|6.00-48|
|最小自動暫停延遲（分鐘）|60|60|60|60|
|資料行存放區支援|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|1536|3072|3072|3072|
|記錄大小上限 (GB)|461|461|461|922|
|TempDB 最大資料大小（GB）|320|384|448|512|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|3200|3840|4480|5120|
|最大記錄速率（MBps）|30|30|30|30|
|並行背景工作 (要求) 數上限|750|900|1050|1200|
|並行工作階段數上限|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="hyperscale---provisioned-compute---gen4"></a>超大規模資料庫-布建的計算-第4代

### <a name="gen4-compute-generation-part-1"></a>第4代計算世代（第1部分）

|效能等級|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|1|2|3|4|5|6|
|記憶體 (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)容量|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (TB)|100 |100 |100 |100 |100 |100|
|記錄大小上限 (TB)|1 |1 |1 |1 |1 |1 |
|TempDB 最大資料大小（GB）|32|64|96|128|160|192|
|儲存體類型| [附注1](#notes) |[附注1](#notes)|[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |
|最大資料 IOPS *|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|
|最大記錄速率（MBps）|100 |100 |100 |100 |100 |100 |
|IO 延遲 (大約)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|您應該|0-4|0-4|0-4|0-4|0-4|0-4|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|是|是|是|是|是|是|
|備份儲存體保留期|7 天|7 天|7 天|7 天|7 天|7 天|
|||

### <a name="gen4-compute-generation-part-2"></a>第4代計算世代（第2部分）

|效能等級|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|7|8|9|10|16|24|
|記憶體 (GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)容量|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (TB)|100 |100 |100 |100 |100 |100 |
|記錄大小上限 (TB)|1 |1 |1 |1 |1 |1 |
|TempDB 最大資料大小（GB）|224|256|288|320|512|768|
|儲存體類型| [附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |
|最大資料 IOPS *|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|
|最大記錄速率（MBps）|100 |100 |100 |100 |100 |100 |
|IO 延遲 (大約)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|
|並行背景工作 (要求) 數上限|1400|1600|1800|2000|3200|4800|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|您應該|0-4|0-4|0-4|0-4|0-4|0-4|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|是|是|是|是|是|是|
|備份儲存體保留期|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="hyperscale---provisioned-compute---gen5"></a>超大規模資料庫-布建的計算-第5代

### <a name="gen5-compute-generation-part-1"></a>第5代計算世代（第1部分）

|效能等級|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|2|4|6|8|10|12|14|
|記憶體 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)容量|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (TB)|100 |100 |100 |100 |100 |100 |100|
|記錄大小上限 (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB 最大資料大小（GB）|64|128|192|256|320|384|448|
|儲存體類型| [附注1](#notes) |[附注1](#notes)|[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |
|最大資料 IOPS *|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|
|最大記錄速率（MBps）|100 |100 |100 |100 |100 |100 |100 |
|IO 延遲 (大約)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|1400|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|您應該|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|是|是|是|是|是|是|是|
|備份儲存體保留期|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第5代計算世代（第2部分）

|效能等級|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|16|18|20|24|32|40|80|
|記憶體 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute)容量|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|記憶體3倍|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (TB)|100 |100 |100 |100 |100 |100 |100 |
|記錄大小上限 (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB 最大資料大小（GB）|512|576|640|768|1024|1280|2560|
|儲存體類型| [附注1](#notes) |[附注1](#notes)|[附注1](#notes)|[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |[附注1](#notes) |
|最大資料 IOPS *|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|[附注2](#notes)|
|最大記錄速率（MBps）|100 |100 |100 |100 |100 |100 |100 |
|IO 延遲 (大約)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|[附注3](#notes)|
|並行背景工作 (要求) 數上限|1600|1800|2000|2400|3200|4000|8000|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|您應該|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|是|是|是|是|是|是|是|
|備份儲存體保留期|7 天|7 天|7 天|7 天|7 天|7 天|7 天|
|||

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

#### <a name="notes"></a>備忘錄

**附注 1**：超大規模資料庫是多層式架構，具有不同的計算和儲存元件：[超大規模資料庫服務層架構](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**附注 2**：超大規模資料庫多層式架構有多個層級的快取。 有效的 IOPS 將視工作負載而定。

**附注 3**：計算複本上以 RBPEX SSD 為基礎的快取中的資料延遲為1-2 毫秒，這會快取最常使用的資料頁。 從頁面伺服器抓取資料的延遲較高。

## <a name="general-purpose---provisioned-compute---gen4"></a>一般用途-布建的計算-第4代

> [!IMPORTANT]
> 澳大利亞東部或巴西南部區域不再支援新的第4代資料庫。

### <a name="gen4-compute-generation-part-1"></a>第4代計算世代（第1部分）

|計算大小|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|1|2|3|4|5|6|
|記憶體 (GB)|7|14|21|28|35|42|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|1024|1024|1536|1536|1536|3072|
|記錄大小上限 (GB)|307|307|461|461|461|922|
|TempDB 最大資料大小（GB）|32|64|96|128|160|192|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|320|640|960|1280|1600|1920|
|最大記錄速率（MBps）|3.75|7.5|11.25|15|18.75|22.5|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>第4代計算世代（第2部分）

|計算大小|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|7|8|9|10|16|24|
|記憶體 (GB)|49|56|63|70|112|159.5|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|3072|3072|3072|3072|4096|4096|
|記錄大小上限 (GB)|922|922|922|922|1229|1229|
|TempDB 最大資料大小（GB）|224|256|288|320|512|768|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)
|最大資料 IOPS *|2240|2560|2880|3200|5120|7680|
|最大記錄速率（MBps）|26.3|30|30|30|30|30|
|並行背景工作 (要求) 數上限|1400|1600|1800|2000|3200|4800|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---gen5"></a>一般用途-布建的計算-第5代

### <a name="gen5-compute-generation-part-1"></a>第5代計算世代（第1部分）

|計算大小|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|2|4|6|8|10|12|14|
|記憶體 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|1024|1024|1536|1536|1536|3072|3072|
|記錄大小上限 (GB)|307|307|461|461|461|922|922|
|TempDB 最大資料大小（GB）|64|128|192|256|320|384|384|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|640|1280|1920|2560|3200|3840|4480|
|最大記錄速率（MBps）|7.5|15|22.5|30|30|30|30|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|1400|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第5代計算世代（第2部分）

|計算大小|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|16|18|20|24|32|40|80|
|記憶體 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|
|記錄大小上限 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 最大資料大小（GB）|512|576|640|768|1024|1280|2560|
|儲存體類型|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|5120|5760|6400|7680|10240|12800|25600|
|最大記錄速率（MBps）|30|30|30|30|30|30|30|
|並行背景工作 (要求) 數上限|1600|1800|2000|2400|3200|4000|8000|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>一般用途-布建的計算-Fsv2 系列

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 系列計算產生（預覽）

|計算大小|GP_Fsv2_72|
|:--- | --: |
|計算世代|Fsv2 系列|
|虛擬核心|72|
|記憶體 (GB)|136.2|
|資料行存放區支援|是|
|OLTP 記憶體內部儲存體 (GB)|N/A|
|資料大小上限 (GB)|4096|
|記錄大小上限 (GB)|1024|
|TempDB 最大資料大小（GB）|333|
|儲存體類型|遠端 SSD|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|最大資料 IOPS *|12,800|
|最大記錄速率（MBps）|30|
|並行背景工作 (要求) 數上限|3600|
|並行登入數上限|3600|
|並行工作階段數上限|30,000|
|複本數目|1|
|多重 AZ|N/A|
|讀取向外延展|N/A|
|內含備份儲存體|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen4"></a>商務關鍵性-布建的計算-第4代

> [!IMPORTANT]
> 澳大利亞東部或巴西南部區域不再支援新的第4代資料庫。

### <a name="gen4-compute-generation-part-1"></a>第4代計算世代（第1部分）

|計算大小|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|1|2|3|4|5|6|
|記憶體 (GB)|7|14|21|28|35|42|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|1|2|3|4|5|6|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限 (GB)|307|307|307|307|307|307|
|TempDB 最大資料大小（GB）|32|64|96|128|160|192|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|最大資料 IOPS *|4,000|8,000|12,000|16,000|20,000|24,000|
|最大記錄速率（MBps）|8|16|24|32|40|48|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|
|並行登入數上限|200|400|600|800|1000|1200|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|4|4|4|4|4|4|
|多重 AZ|是|是|是|是|是|是|
|讀取向外延展|是|是|是|是|是|是|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen4-compute-generation-part-2"></a>第4代計算世代（第2部分）

|計算大小|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|計算世代|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|虛擬核心|7|8|9|10|16|24|
|記憶體 (GB)|49|56|63|70|112|159.5|
|資料行存放區支援|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|7|8|9.5|11|20|36|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限 (GB)|307|307|307|307|307|307|
|TempDB 最大資料大小（GB）|224|256|288|320|512|768|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|最大資料 IOPS |28,000|32,000|36000|40,000|64,000|76,800|
|最大記錄速率（MBps）|56|64|64|64|64|64|
|並行背景工作 (要求) 數上限|1400|1600|1800|2000|3200|4800|
|最大並行登入（要求）|1400|1600|1800|2000|3200|4800|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|4|4|4|4|4|4|
|多重 AZ|是|是|是|是|是|是|
|讀取向外延展|是|是|是|是|是|是|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---gen5"></a>商務關鍵性-布建的計算-第5代

### <a name="gen5-compute-generation-part-1"></a>第5代計算世代（第1部分）

|計算大小|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|2|4|6|8|10|12|14|
|記憶體 (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|資料大小上限 (GB)|1024|1024|1536|1536|1536|3072|3072|
|記錄大小上限 (GB)|307|307|461|461|461|922|922|
|TempDB 最大資料大小（GB）|64|128|192|256|320|384|448|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|最大資料 IOPS *|8000|16,000|24,000|32,000|40,000|48000|56000|
|最大記錄速率（MBps）|24|48|72|96|96|96|96|
|並行背景工作 (要求) 數上限|200|400|600|800|1000|1200|1400|
|並行登入數上限|200|400|600|800|1000|1200|1400|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|4|4|4|4|4|4|4|
|多重 AZ|是|是|是|是|是|是|是|
|讀取向外延展|是|是|是|是|是|是|是|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

### <a name="gen5-compute-generation-part-2"></a>第5代計算世代（第2部分）

|計算大小|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|計算世代|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|虛擬核心|16|18|20|24|32|40|80|
|記憶體 (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|資料行存放區支援|是|是|是|是|是|是|是|
|OLTP 記憶體內部儲存體 (GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|資料大小上限 (GB)|3072|3072|3072|4096|4096|4096|4096|
|記錄大小上限 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 最大資料大小（GB）|512|576|640|768|1024|1280|2560|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|最大資料 IOPS *|64,000|72000|80,000|96000|128000|160,000|204800|
|最大記錄速率（MBps）|96|96|96|96|96|96|96|
|並行背景工作 (要求) 數上限|1600|1800|2000|2400|3200|4000|8000|
|並行登入數上限|1600|1800|2000|2400|3200|4000|8000|
|並行工作階段數上限|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|複本數目|4|4|4|4|4|4|4|
|多重 AZ|是|是|是|是|是|是|是|
|讀取向外延展|是|是|是|是|是|是|是|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

## <a name="business-critical---provisioned-compute---m-series"></a>商務關鍵性-布建的計算-M 系列

### <a name="m-series-compute-generation-preview"></a>M 系列計算產生（預覽）

|計算大小|BC_M_128|
|:--- | --: |
|計算世代|M 系列|
|虛擬核心|128|
|記憶體 (GB)|3767.1|
|資料行存放區支援|是|
|OLTP 記憶體內部儲存體 (GB)|1768|
|資料大小上限 (GB)|4096|
|記錄大小上限 (GB)|2048|
|TempDB 最大資料大小（GB）|4096|
|儲存體類型|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|最大資料 IOPS *|160,000|
|最大記錄速率（MBps）|264|
|並行背景工作 (要求) 數上限|12,800|
|並行登入數上限|12,800|
|並行工作階段數上限|30000|
|複本數目|4|
|多重 AZ|是|
|讀取向外延展|是|
|內含備份儲存體|1X DB 大小|

\*IO 大小的最大值，範圍介於 8 KB 到 64 KB 之間。 實際的 IOPS 與工作負載相關。 如需詳細資訊，請參閱[資料 IO 治理](sql-database-resource-limits-database-server.md#resource-governance)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫的 DTU 資源限制，請參閱[使用 DTU 購買模型的單一資料庫資源限制](sql-database-dtu-resource-limits-single-databases.md)
- 如需彈性集區的 vCore 資源限制，請參閱[使用 vCore 購買模型的彈性集區資源限制](sql-database-vcore-resource-limits-elastic-pools.md)
- 如需彈性集區的 DTU 資源限制，請參閱[使用 dtu 購買模型的彈性集區資源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 如需受控執行個體的資源限制，請參閱[受控執行個體資源限制](sql-database-managed-instance-resource-limits.md)。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 如需資料庫伺服器資源限制的相關資訊，請參閱 [SQL Database 伺服器上的資源限制概觀](sql-database-resource-limits-database-server.md)，以了解伺服器和訂用帳戶層級的限制。
