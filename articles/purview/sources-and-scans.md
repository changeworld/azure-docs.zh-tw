---
title: 支援的資料來源和檔案類型
description: 本文提供有關範疇中支援的資料來源和檔案類型的概念詳細資料。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 6432cc9affd34c0fa9b832aea91932a2b9e7540f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576667"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure 範疇中支援的資料來源和檔案類型

本文討論範疇中支援的資料來源、檔案類型和掃描概念。

## <a name="supported-data-sources"></a>支援的資料來源

Azure 範疇支援下列來源：

| 商店類型 | 支援的驗證類型 | 透過 UX/PowerShell 設定掃描 |
| ---------- | ------------------- | ------------------------------ |
| 內部部署 SQL Server                   | SQL 驗證                        | Ux                                |
| Azure Synapse Analytics (先前為 SQL DW)            | SQL 驗證、服務主體、MSI               | Ux                             |
| Azure SQL Database (DB)                   | SQL 驗證、服務主體、MSI               | Ux |
| Azure SQL Database 受控執行個體      | SQL 驗證、服務主體、MSI               | Ux    |
| Azure Blob 儲存體                       | 帳戶金鑰、服務主體、MSI | Ux            |
| Azure 資料總管                      | 服務主體                              | Ux            |
| Azure Data Lake Storage Gen1 (ADLS Gen1)  | 服務主體，MSI                              | Ux            |
| Azure Data Lake Storage Gen2 (ADLS Gen2)  | 帳戶金鑰、服務主體、MSI            | Ux            |
| Azure Cosmos DB                          | 帳戶金鑰                                    | Ux            |


> [!Note]
> Azure Data Lake Storage Gen2 現已全面上市。 我們建議您現在就開始使用。 如需詳細資訊，請參閱[產品頁面](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)。

## <a name="file-types-supported-for-scanning"></a>支援掃描的檔案類型

下列檔案類型支援掃描，適用于架構的解壓縮和分類（如果適用）：

- 延伸模組支援的結構化檔案格式： AVRO、ORC、PARQUET、CSV、JSON、PSV、SSV、TSV、TXT、XML
- 延伸模組支援的檔檔案格式： DOC、DOCM、.DOCX、DOT、ODP、ODS、ODT、PDF、.POT、PPS、PPSX、PPT、PPTM、.PPTX、XLC、XLS、.XLSB、XLSM、.XLSX、.XLT
- 範疇也支援自訂副檔名和自訂剖析器。

## <a name="sampling-within-a-file"></a>檔案內的取樣

在範疇術語中，
- L1 掃描：解壓縮基本資訊和中繼資料，例如檔案名、大小和完整名稱
- L2 掃描：解壓縮結構化檔案類型和資料庫資料表的架構
- L3 掃描：在適用的情況下將架構解壓縮，並以系統和自訂分類規則為取樣的檔案做為主體

針對所有結構化檔案格式，請以下列方式範疇掃描器範例檔案：

- 針對結構化檔案類型，它會在每個資料行中取樣128個數據列或 1 MB，以較低者為准
- 針對檔檔案格式，每個檔案都範例為 20 MB。
    - 如果檔檔大於 20 MB，則不受限於分類) 的深層掃描 (。 在此情況下，範疇只會捕捉基本的中繼資料，例如檔案名和完整名稱。

## <a name="resource-set-file-sampling"></a>資源集檔案取樣

如果與系統資源集原則或客戶定義的資源集原則相符，就會在範疇中偵測到一個資料夾或分割區檔案群組作為 *資源集* 。 如果偵測到資源集，則範疇會取樣其中包含的每個資料夾。 請 [在這裡](concept-resource-sets.md)深入瞭解資源集。

依檔案類型的資源集的檔案取樣：

- 100檔案中的 **分隔檔案 (CSV、PSV、SSV、TSV)** -1 會在被視為「資源集」的資料夾或分割區群組中取樣 (L3 掃描) 
- **Data Lake 檔案類型 (Parquet、Avro、Orc)** -1 （以 (18446744073709551615 為單位）) 在被視為 *資源集* 的資料夾或分割區群組內取樣 (L3 掃描) 
- 在100檔案中 **(JSON、XML、TXT) -1 的其他結構化檔案類型**，會在被視為「資源集」的資料夾或分割區群組中取樣 (L3 掃描) 
- **SQL 物件和 CosmosDB 實體** -每個檔案都是 L3 掃描。
- **檔檔案類型** -每個檔案都是 L3 掃描。 資源集模式不適用於這些檔案類型。

## <a name="classification"></a>分類

所有105系統分類規則都適用于結構化的檔案格式。 只有 MCE 分類規則適用于檔案類型 (不會掃描原生 RegEx 模式，bloom 以篩選器為基礎的偵測) 。 如需有關支援分類的詳細資訊，請參閱 [Azure 範疇中支援的分類](supported-classifications.md)。

## <a name="next-steps"></a>後續步驟

- [教學課程：執行入門套件並掃描資料](tutorial-scan-data.md)
- [ (預覽版) 管理 Azure 範疇中的資料來源 ](manage-data-sources.md)