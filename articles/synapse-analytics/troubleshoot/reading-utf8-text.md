---
title: 疑難排解：使用無伺服器 SQL 集區從 CSV 或 PARQUET 檔案讀取 UTF-8 文字
description: 在 Azure Synapse Analytics 中使用無伺服器 SQL 集區讀取 CSV 或 PARQUET 檔案中的 UTF-8 文字
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576412"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的無伺服器 SQL 集區從 CSV 或 Parquet 檔案讀取 UTF-8 文字進行疑難排解

本文提供使用 Azure Synapse Analytics 中的無伺服器 SQL 集區，從 CSV 或 Parquet 檔案讀取 UTF-8 文字的疑難排解步驟。

使用無伺服器 SQL 集區從 CSV 或 PARQUET 檔案讀取 UTF-8 文字時，如果查詢傳回具有非 UTF8 定序的 VARCHAR 資料行，則某些特殊字元（例如ü和ö）會不正確地轉換。 這是 SQL Server 和 Azure SQL 的已知問題。 非 UTF8 定序是 Synapse SQL 中的預設值，因此客戶查詢將會受到影響。 使用標準英文字元和某些擴充拉丁字元子集的客戶可能不會注意到轉換錯誤。 在無伺服器的 SQL 集區中，[一律使用 utf-8 定序來讀取 utf-8 文字](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)，將會更詳細地說明不正確的轉換。

## <a name="workaround"></a>因應措施

解決此問題的因應措施是在從 CSV 或 PARQUET 檔案讀取 UTF-8 文字時，一律使用 UTF-8 定序。

- 在許多情況下，您只需要在資料庫 (中繼資料作業) 上設定 UTF8 定序。

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- 您可以在 OPENROWSET 或 external table 中明確定義 VARCHAR 資料行的定序：

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- 如果您未在讀取 UTF8 資料的外部資料表上指定 UTF8 定序，則必須重新建立受影響的外部資料表，並在 VARCHAR 資料行上設定 UTF8 定序 (中繼資料作業) 。


## <a name="next-steps"></a>後續步驟

* [使用 Synapse SQL 查詢 Parquet 檔](../sql/query-parquet-files.md)
* [使用 Synapse SQL 查詢 CSV 檔案](../sql/query-single-csv-file.md)
* [CETAS 搭配 Synapse SQL](../sql/develop-tables-cetas.md)
* [快速入門：使用無伺服器 SQL 集區](../quickstart-sql-on-demand.md)
