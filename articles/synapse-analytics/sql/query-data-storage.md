---
title: 概觀 - 使用 SQL 隨選來查詢儲存體中的資料 (預覽)
description: 本節包含您可以用來在 Azure Synapse Analytics 中試用 SQL 隨選 (預覽) 資源的範例查詢。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e18fc765385e6d703e735a1ca15c539c32f36e93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116242"
---
# <a name="overview-query-data-in-storage"></a>概觀：查詢儲存體中的資料

本節包含您可以用來在 Azure Synapse Analytics 中試用 SQL 隨選 (預覽) 資源的範例查詢。
目前支援的檔案有： 
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Prerequisites

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

在使用本文稍後所包含的範例之前，您有兩個步驟：

- 為您的檢視建立資料庫 (如果您想要使用檢視)
- 建立可讓 SQL 隨選用來存取儲存體中檔案的認證

### <a name="create-database"></a>建立資料庫

您需要有資料庫才能建立檢視。 您會將此資料庫用於本文件中的某些範例查詢。

> [!NOTE]
> 資料庫僅用於檢視中繼資料而非實際資料。  請記下您使用的資料庫名稱以供稍後使用。

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>建立認證

您必須先建立認證才能執行查詢。 SQL 隨選服務會使用此認證來存取儲存體中的檔案。

> [!NOTE]
> 若要在本節中成功執行「操作說明」，您必須使用 SAS 權杖。
>
> 若要開始使用 SAS 權杖，您必須卸除 UserIdentity，如下列[文章](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)所述。
>
> SQL 隨選一律預設使用 AAD 傳遞。

如需如何管理儲存體存取控制的詳細資訊，請查看此[連結](develop-storage-files-storage-access-control.md)。

若要建立 CSV、JSON 和 Parquet 容器的認證，請執行下列程式碼：

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
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

## <a name="validation"></a>驗證

請執行下列三個查詢，並檢查是否已正確建立認證。

> [!NOTE]
> 範例查詢中的所有 URI 都會使用位於北歐 Azure 區域的儲存體帳戶。 請確定您已建立適當的認證。 執行下列查詢，並確認儲存體帳戶有列出。

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

如果找不到適當認證，請檢查[第一次設定](#first-time-setup)。

### <a name="sample-query"></a>範例查詢

驗證的最後一個步驟是執行下列查詢：

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
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
