---
title: 概觀 - 使用 SQL 隨選來查詢儲存體中的資料 (預覽)
description: 本節包含您可以用來在 Azure Synapse Analytics 中試用 SQL 隨選 (預覽) 資源的範例查詢。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: d42dac0899c5ab9c4f817df40095dd08891fb8e7
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85207697"
---
# <a name="overview-query-data-in-storage"></a>概觀：查詢儲存體中的資料

本節包含您可以用來在 Azure Synapse Analytics 中試用 SQL 隨選 (預覽) 資源的範例查詢。
目前支援的檔案格式：  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>必要條件

要發出查詢所需的工具：

- 您選擇的 SQL 用戶端：
    - Azure Synapse Studio (預覽)
    - Azure Data Studio
    - SQL Server Management Studio

此外，參數如下：

| 參數                                 | 描述                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 隨選服務端點位址    | 會用來作為伺服器名稱。                                   |
| SQL 隨選服務端點區域     | 會用來判斷範例中使用的儲存體。 |
| 用來存取端點的使用者名稱和密碼 | 將用來存取端點。                               |
| 您將用來建立檢視的資料庫     | 此資料庫會作為範例的起點。       |

## <a name="first-time-setup"></a>第一次設定

您的第一個步驟是**建立資料庫**，您將會對其執行查詢。 然後藉由在該資料庫上執行[安裝指令碼](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)來初始化物件。 此安裝指令碼會建立資料來源、資料庫範圍認證，以及用來讀取這些範例中資料的外部檔案格式。

> [!NOTE]
> 資料庫僅用於檢視中繼資料而非實際資料。  請記下您使用的資料庫名稱以供稍後使用。

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>所提供的示範資料

示範資料包含下列資料集：

- NYC 計程車 - 黃色計程車車程記錄 - 公用 NYC 資料集的一部分
  - CSV 格式
  - Parquet 格式
- 人口資料集
  - CSV 格式
- 具有巢狀資料行的範例 Parquet 檔案
  - Parquet 格式
- 書籍 JSON
  - JSON 格式

| 資料夾路徑                                                  | 描述                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | CSV 格式資料的父資料夾                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 有不同 CSV 格式人口資料檔案的資料夾。 |
| /csv/taxi/                                                   | 有 CSV 格式 NYC 公用資料檔案的資料夾              |
| /parquet/                                                    | Parquet 格式資料的父資料夾                     |
| /parquet/taxi                                                | 使用 Hive/Hadoop 資料分割配置，依年份和月份來分割的 Parquet 格式 NYC 公用資料檔案。 |
| /parquet/nested/                                             | 具有巢狀資料行的範例 Parquet 檔案                     |
| /json/                                                       | JSON 格式資料的父資料夾                        |
| /json/books/                                                 | 具有書籍資料的 JSON 檔案                                   |

### <a name="sample-query"></a>範例查詢

驗證的最後一個步驟是執行下列查詢：

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

上述查詢應該會傳回此數字：**8945574**。

## <a name="next-steps"></a>後續步驟

您現在已準備好繼續進行下列操作說明文章：

- [查詢單一 CSV 檔案](query-single-csv-file.md)

- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)

- [查詢特定檔案](query-specific-files.md)

- [查詢 Parquet 檔案](query-parquet-files.md)

- [查詢 Parquet 巢狀型別](query-parquet-nested-types.md)

- [查詢 JSON 檔案](query-json-files.md)

- [建立和使用檢視](create-use-views.md)
