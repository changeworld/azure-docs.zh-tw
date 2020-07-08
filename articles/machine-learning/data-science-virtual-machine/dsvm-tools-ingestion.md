---
title: 資料擷取工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解在資料科學虛擬機器上預先安裝的資料內嵌工具和公用程式。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708003"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>資料科學虛擬機器資料擷取工具

作為資料科學或 AI 專案中的第一個技術步驟，您必須識別要使用的資料集，並將它們帶入您的分析環境中。 資料科學虛擬機器（DSVM）提供工具和程式庫，可將來自不同來源的資料帶入 DSVM 本機上的分析資料存放區，或是放在雲端或內部部署的資料平臺中。

以下是 DSVM 中可用的一些資料移動工具。

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將資料從 Azure Blob 儲存體複製到 Azure Data Lake 存放區的工具。 它也可以在兩個 Azure Data Lake Store 帳戶之間複製資料。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將多個 blob 從 Azure Blob 儲存體匯入至 Azure Data Lake 存放區。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `adlcopy` 以取得協助。    |
| 範例的連結      | [使用 AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 上的相關工具      | AzCopy，Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Azure 的管理工具。 它也包含從 azure 資料平臺（例如 Azure Blob 儲存體和 Azure Data Lake 存放區）移動資料的命令動詞。     |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 在 Azure 儲存體和 Azure Data Lake 存放區中匯入和匯出資料。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `az` 以取得協助。    |
| 範例的連結      | [使用 Azure CLI](https://docs.microsoft.com/cli/azure)     |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一種工具，可將資料複製到本機檔案、Azure Blob 儲存體、檔案和資料表。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將檔案複製到 Azure Blob 儲存體，並在帳戶之間複製 blob。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `azcopy` 以取得協助。    |
| 範例的連結      | [AzCopy on Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM 上的相關工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 資料移轉工具

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 此工具可將各種來源的資料匯入至 Azure Cosmos DB，這是雲端中的 NoSQL 資料庫。 這些來源包括 JSON 檔案、CSV 檔案、SQL、MongoDB、Azure 資料表儲存體、Amazon DynamoDB，以及 Azure Cosmos DB SQL API 集合。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 VM 將檔案匯入到 CosmosDB、將資料從 Azure 資料表儲存體匯入至 CosmosDB，以及將資料從 Microsoft SQL Server 資料庫匯入至 CosmosDB。     |
|  如何使用/執行它？    |   若要使用命令列版本，請開啟命令提示字元，然後輸入 `dt` 。 若要使用 GUI 工具，請開啟命令提示字元，然後輸入 `dtui` 。    |
| 範例的連結      | [CosmosDB 匯入資料](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 用來與 Azure 雲端中儲存之檔案互動的圖形化使用者介面。 |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 DSVM 匯入和匯出資料。    |
|  如何使用/執行它？    | 搜尋 [開始] 功能表中的 [Azure 儲存體總管]。 |
| 範例的連結      | [Azure 儲存體總管](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 在 SQL Server 和資料檔案之間複製資料的 SQL Server 工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將 CSV 檔案匯入 SQL Server 資料表，並將 SQL Server 資料表匯出至檔案。      |
|  如何使用/執行它？    |   開啟命令提示字元，然後輸入 `bcp` 以取得協助。    |
| 範例的連結      | [bcp 公用程式](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM 上的相關工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 在 Linux 檔案系統中掛接 Azure Blob 儲存體容器的工具。      |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      | 讀取和寫入容器中的 blob。      |
|  如何使用並執行它？    |   在終端機執行 _blobfuse_。    |
| 範例的連結      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上的相關工具      | Azure CLI      |
