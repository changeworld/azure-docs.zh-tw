---
title: 資料擷取工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解資料科學虛擬機器上預先安裝的資料引入工具和實用程式。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270052"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>資料科學虛擬機器資料擷取工具

作為資料科學或 AI 專案中的第一個技術步驟之一，您必須標識要使用的資料集並將其引入分析環境。 資料科學虛擬機器 （DSVM） 提供工具和庫，用於將來自不同來源的資料引入 DSVM 上的本地分析資料存儲，或將資料平臺引入雲或本地資料平臺。

以下是 DSVM 中提供的一些資料移動工具。

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將資料從 Azure Blob 存儲複製到 Azure 資料湖存儲的工具。 它也可以在兩個 Azure Data Lake Store 帳戶之間複製資料。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 Azure Blob 存儲導入多個 Blob 到 Azure 資料湖存儲。      |
|  如何使用/執行它？    |   打開命令提示符並鍵入`adlcopy`以獲取有關説明。    |
| 範例的連結      | [使用 AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 的相關工具      | 阿茲比， Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Azure 的管理工具。 它還包含命令謂詞，用於從 Azure 資料平臺（如 Azure Blob 存儲和 Azure 資料湖存儲）移動資料。     |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 將資料導入並從 Azure 存儲和 Azure 資料湖存儲處導入和匯出。      |
|  如何使用/執行它？    |   打開命令提示符並鍵入`az`以獲取有關説明。    |
| 範例的連結      | [使用 Azure CLI](https://docs.microsoft.com/cli/azure)     |
| DSVM 的相關工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一種工具，用於將資料複製到本地檔、Azure Blob 存儲、檔和表。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將檔案複製到 Azure Blob 存儲並在帳戶之間複製 Blob。      |
|  如何使用/執行它？    |   打開命令提示符並鍵入`azcopy`以獲取有關説明。    |
| 範例的連結      | [AzCopy on Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| DSVM 的相關工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 資料移轉工具

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 將資料從各種源導入 Azure Cosmos DB 的工具，這是雲中的 NoSQL 資料庫。 這些源包括 JSON 檔、CSV 檔、SQL、MongoDB、Azure 表存儲、Amazon DynamoDB 和 Azure Cosmos DB SQL API 集合。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將檔從 VM 導入 CosmosDB，將資料從 Azure 表存儲導入 CosmosDB，並將資料從 Microsoft SQL Server 資料庫導入 CosmosDB。     |
|  如何使用/執行它？    |   要使用命令列版本，打開命令提示符並鍵入`dt`。 要使用 GUI 工具，應打開命令提示符`dtui`並鍵入 。    |
| 範例的連結      | [宇宙DB導入資料](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| DSVM 的相關工具      | AzCopy、AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 用於與 Azure 雲中存儲的檔交互的圖形化使用者介面。 |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 DSVM 導入和匯出資料。    |
|  如何使用/執行它？    | 在"開始"功能表中搜索"Azure 存儲資源管理器"。 |
| 範例的連結      | [Azure 存儲資源管理器](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 在 SQL Server 和資料檔案之間複製資料的 SQL Server 工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將 CSV 檔導入 SQL Server 表並將 SQL Server 表匯出到檔。      |
|  如何使用/執行它？    |   打開命令提示符並鍵入`bcp`以獲取有關説明。    |
| 範例的連結      | [bcp 實用程式](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| DSVM 的相關工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一種工具，用於在 Linux 檔案系統中裝載 Azure Blob 存儲容器。      |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      | 讀取和寫入容器中的 Blob。      |
|  如何使用和運行它？    |   在終端機執行 _blobfuse_。    |
| 範例的連結      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 的相關工具      | Azure CLI      |
