---
title: 資料擷取工具
titleSuffix: Azure Data Science Virtual Machine
description: 深入瞭解預先安裝在資料科學虛擬機器上的資料內嵌工具和公用程式。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 89cf81f8d0a66c29a345f56676fbb97601743710
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308975"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>資料科學虛擬機器資料擷取工具

作為資料科學或 AI 專案中的第一個技術步驟，您必須識別要使用的資料集，並將它們帶入分析環境。 資料科學虛擬機器 (DSVM) 提供工具和程式庫，將來自不同來源的資料帶入 DSVM 本機的分析資料儲存區，或放入雲端或內部部署的資料平臺。

以下是 DSVM 中可用的一些資料移動工具。

## <a name="adlcopy"></a>AdlCopy

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 將資料從 Azure Blob 儲存體複製到 Azure Data Lake 存放區的工具。 它也可以在兩個 Azure Data Lake Store 帳戶之間複製資料。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 Azure Blob 儲存體將多個 blob 匯入 Azure Data Lake 存放區。      |
|  如何使用/執行它？    |   開啟命令提示字元並輸入 `adlcopy` 以取得說明。    |
| 範例的連結      | [使用 AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| DSVM 上的相關工具      | AzCopy、Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | Azure 的管理工具。 它也包含從 azure 資料平臺（例如 Azure Blob 儲存體和 Azure Data Lake 存放區）移動資料的命令動詞。     |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 在 Azure 儲存體和 Azure Data Lake 存放區中匯入和匯出資料。      |
|  如何使用/執行它？    |   開啟命令提示字元並輸入 `az` 以取得說明。    |
| 範例的連結      | [使用 Azure CLI](/cli/azure)     |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 一種工具，可將資料複製到本機檔案、Azure Blob 儲存體、檔案和資料表。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將檔案複製到 Azure Blob 儲存體，並在帳戶之間複製 blob。      |
|  如何使用/執行它？    |   開啟命令提示字元並輸入 `azcopy` 以取得說明。    |
| 範例的連結      | [AzCopy on Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| DSVM 上的相關工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 資料移轉工具

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 此工具可將資料從各種來源匯入到 Azure Cosmos DB，也就是雲端中的 NoSQL 資料庫。 這些來源包括 JSON 檔案、CSV 檔案、SQL、MongoDB、Azure 資料表儲存體、Amazon DynamoDB 和 Azure Cosmos DB SQL API 集合。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 VM 將檔案匯入至 CosmosDB、將資料從 Azure 資料表儲存體匯入至 CosmosDB，以及從 Microsoft SQL Server 資料庫將資料匯入到 CosmosDB。     |
|  如何使用/執行它？    |   若要使用命令列版本，請開啟命令提示字元，然後輸入 `dt` 。 若要使用 GUI 工具，請開啟命令提示字元，然後輸入 `dtui` 。    |
| 範例的連結      | [CosmosDB 匯入資料](../../cosmos-db/import-data.md)      |
| DSVM 上的相關工具      | AzCopy、AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 圖形化消費者介面，可與儲存在 Azure 雲端中的檔案進行互動。 |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 從 DSVM 匯入和匯出資料。    |
|  如何使用/執行它？    | 在 [開始] 功能表中搜尋「Azure 儲存體總管」。 |
| 範例的連結      | [Azure 儲存體總管](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 在 SQL Server 和資料檔案之間複製資料的 SQL Server 工具。      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 將 CSV 檔案匯入 SQL Server 資料表中，並將 SQL Server 資料表匯出到檔案。      |
|  如何使用/執行它？    |   開啟命令提示字元並輸入 `bcp` 以取得說明。    |
| 範例的連結      | [bcp 公用程式](/sql/tools/bcp-utility)      |
| DSVM 上的相關工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| 類別 | 值 |
| ------------- | ------------- |
| 這是什麼？   | 在 Linux 檔案系統中掛接 Azure Blob 儲存體容器的工具。      |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      | 讀取和寫入容器中的 blob。      |
|  如何使用和執行？    |   在終端機執行 _blobfuse_ 。    |
| 範例的連結      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上的相關工具      | Azure CLI      |