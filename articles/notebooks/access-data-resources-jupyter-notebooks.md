---
title: 訪問聚居筆記本中的資料 - Azure 筆記本預覽
description: 瞭解如何從 Jupyter 筆記本訪問檔、REST API、資料庫和不同的 Azure 存儲資源。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646342"
---
# <a name="access-cloud-data-in-a-notebook"></a>在 Notebook 中存取雲端資料

要在 Jupyter Notebook 中執行有趣的工作，必須要有資料。 資料是 Notebook 的原動力。

您可以[將資料檔案匯入專案中](work-with-project-data-files.md)，即使是在 Notebook 內使用 `curl` 之類的命令直接下載檔案，也都沒有問題。 不過，您很可能需要處理更龐雜的資料，而這些資料可能來自於非檔案的來源，例如 REST API、關聯式資料庫，以及雲端儲存體 (例如 Azure 資料表)。

本文將簡要概述這些不同的選項。 由於資料存取在執行時最易於觀察，您可以在 [Azure Notebooks 範例 - 存取您的資料](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)中找到可執行的程式碼。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>REST API

一般而言，來自網際網路的大量資料並非透過檔案而存取的，而是透過 REST API 存取的。 幸而，由於 Notebook 資料格能夠包含您所需的任何程式碼，因此您可以使用程式碼來傳送要求和接收 JSON 資料。 接著，您可以將該 JSON 轉換成您想要使用的任何格式，例如 Pandas 資料框架。

若要使用 REST API 來存取資料，請在 Notebook 的程式碼資料格中使用您在任何其他應用程式中使用的相同程式碼。 使用要求程式庫的一般結構如下所示：

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL 資料庫

您可以藉由 pyodbc 或 pymssql 程式庫的輔助來存取 SQL Server 資料庫。

[使用 Python 查詢 Azure SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python)提供了相關指示，說明如何建立包含 AdventureWorks 資料的資料庫，以及如何查詢該資料。 本文的範例 Notebook 中顯示了相同的程式碼。

## <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體提供數種不同類型的非關聯式儲存體，視您所具備的資料類型和您需要如何加以存取而定：

- 資料表儲存體：為表格式資料提供低成本、高容量的儲存體，這類資料包括收集到的感應器記錄、診斷記錄等等。
- Blob 儲存體：為任何類型的資料提供類似於檔案的儲存體。

範例 Notebook 會示範如何使用資料表和 Blob，包括如何使用共用存取簽章允許對 Blob 的唯讀存取。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB 針對 JSON 文件提供了具完整索引的 NoSQL 存放區。 下列文章提供了多種不同從 Python 使用 Cosmos DB 的方式：

- [使用 Python 建置 SQL API 應用程式](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [使用適用於 MongoDB 的 Azure Cosmos DB API 建置 Flask 應用程式](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [使用 Python 和 Gremlin API 建立圖形資料庫](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [使用 Python 和 Azure 宇宙資料庫構建卡珊多拉應用](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [使用 Python 和 Azure 宇宙資料庫構建表 API 應用](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

使用 Cosmos DB 時，您可以使用 [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) 程式庫。

## <a name="other-azure-databases"></a>其他 Azure 資料庫

Azure 提供了許多其他資料庫類型供您使用。 以下文章提供了從 Python 存取這些資料庫的指引：

- [Azure Database for PostgreSQL︰使用 Python 連線及查詢資料](https://docs.microsoft.com/azure/postgresql/connect-python)
- [快速入門：搭配使用 Azure Redis 快取與 Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL︰使用 Python 連線和查詢資料](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory 的複製精靈](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>後續步驟

- [操作方式：使用專案資料檔案](work-with-project-data-files.md)
