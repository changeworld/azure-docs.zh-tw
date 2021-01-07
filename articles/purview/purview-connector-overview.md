---
title: 範疇連接器總覽
description: 本文概述範疇中支援的不同資料存放區和功能。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780207"
---
# <a name="supported-data-stores"></a>支援的資料存放區

範疇支援下列資料存放區。 按一下每個資料存放區，以瞭解支援的功能和對應的設定詳細資料。

## <a name="purview-data-sources"></a>範疇資料來源

|**類別**|  **資料存放區**  |**中繼資料提取**|**完整掃描**|**增量掃描**|**範圍掃描**|**分類**|**歷程**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob 儲存體](register-scan-azure-blob-storage-source.md)| 是| 是| 是| 是| 是| 是|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|是| 是| 是| 是| 是| 是|
||[Azure 資料總管](register-scan-azure-data-explorer.md)|是| 是| 是| 是| 是| 是|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|是| 是| 是| 是| 是| 是|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md) \(部分機器翻譯\)|是| 是| 是| 是| 是| 是|
||[Azure SQL Database](register-scan-azure-sql-database.md)|是| 是| 否| 是| 是| 是|
||[Azure SQL Database 受控執行個體](register-scan-azure-sql-database-managed-instance.md)|是| 是| 否| 是| 是| 是|
||[Azure Synapse Analytics (先前為 SQL DW) ](register-scan-azure-synapse-analytics.md)|是| 是| 否| 是| 是| 是|
|資料庫|[SQL Server](register-scan-on-premises-sql-server.md)|是| 是| 否| 是| 是| 是|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|是| 是| 否| 否| 否| 是|

## <a name="next-steps"></a>後續步驟

- [註冊並掃描 Azure Blob 儲存體來源](register-scan-azure-blob-storage-source.md)