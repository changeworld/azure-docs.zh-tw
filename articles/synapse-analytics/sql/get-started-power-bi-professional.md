---
title: 連線到 Power BI Professional
description: 在本教學課程中，我們將逐步解說如何將 Power BI Desktop 連線到 SQL 隨選 (預覽) 的步驟。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425327"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>使用 Power BI Professional 連線到 Synapse SQL

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

在本教學課程中，我們將逐步解說如何將 Power BI Desktop 連線到 SQL 隨選 (預覽) 的步驟。

## <a name="prerequisites"></a>Prerequisites

用來發出查詢的工具：

- 您選擇的 SQL 用戶端：

  - Azure Data Studio
  - SQL Server Management Studio

- 已安裝 Power BI Desktop

參數：

| 參數                                 | 描述                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL 隨選服務端點位址    | 會用來作為伺服器名稱                                   |
| SQL 隨選服務端點區域     | 會用來決定要在範例中使用的儲存體 |
| 用來存取端點的使用者名稱和密碼 | 將用來存取端點                               |
| 您將用來建立檢視的資料庫     | 此資料庫將用來作為範例中的起點       |

## <a name="first-time-setup"></a>第一次設定

在使用範例前有兩個步驟：

1. 為檢視建立資料庫
2. 建立可讓 SQL 隨選用來存取儲存體中檔案的認證

### <a name="create-database"></a>建立資料庫

由於您將使用示範環境，因此請建立自己的資料庫以供示範之用。 必須有資料庫才能在其中建立檢視。 您會將此資料庫用於本文件中的某些範例查詢。

> [!NOTE]
> 請注意，資料庫僅用於檢視的中繼資料，不適用於實際資料。
>
> 請記下您使用的資料庫名稱以供稍後使用。

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>建立認證

我們必須先建立認證，然後才能執行查詢。 SQL 隨選服務會使用此認證來存取儲存體中的檔案。

> [!NOTE]
> 請注意，您必須建立認證，才能存取儲存體帳戶。 雖然 SQL 隨選可以存取不同區域的儲存體，但在相同區域中擁有儲存體和 Azure Synapse 工作區可提供更好的效能體驗。

**有關如何為人口普查資料容器建立認證的程式碼片段**，請執行：

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>建立 Power BI Desktop 報告

開啟 Power BI Desktop 應用程式並選取 [取得資料] 選項。
![開啟 Power BI Desktop 應用程式並選取 [取得資料]。](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>步驟 1 - 選取資料來源

在功能表中選取 [Azure]，然後按一下 [Azure SQL Database]。
![選取資料來源。](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>步驟 2 - 選取資料庫

寫下資料庫的 URL 和檢視所在資料庫的名稱。
![選取端點上的資料庫。](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>後續步驟

前進到[查詢儲存體檔案](get-started-azure-data-studio.md)以了解如何使用 Azure Data Studio 連線到 SQL 隨選。
 