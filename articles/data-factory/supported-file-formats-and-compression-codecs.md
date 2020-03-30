---
title: Azure 資料工廠中受支援的檔案格式
description: 本主題描述 Azure Data Factory 中以檔案為基礎的連接器所支援的檔案格式和壓縮碼。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 6855eea5939419c9a0a867de4e0621b4d4ae02b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439579"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Azure Data Factory 中支援的檔案格式和壓縮轉碼器

*本文適用于以下連接器[：Amazon S3、Azure](connector-amazon-simple-storage-service.md) [Blob、Azure](connector-azure-blob-storage.md)[資料存儲湖存儲第 1 代](connector-azure-data-lake-store.md)[、Azure 資料存儲第 2 代](connector-azure-data-lake-storage.md)[、Azure 檔存儲](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)[、FTP、Google](connector-ftp.md)[雲存儲](connector-google-cloud-storage.md)[、HDFS、HTTP](connector-hdfs.md)和[SFTP。](connector-sftp.md) [HTTP](connector-http.md)*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您可以使用 Copy[活動](copy-activity-overview.md)在兩個基於檔的資料存儲之間按大小報方式複製檔，在這種情況下，資料可以有效地複製，而無需任何序列化或反序列化。 

此外，您還可以分析或生成給定格式的檔。 例如，您可以執行以下操作：

* 從本地 SQL Server 資料庫複製資料，以 Parquet 格式寫入 Azure 資料湖存儲 Gen2。
* 從本地檔案系統複製文本 （CSV） 格式的檔，然後以 Avro 格式寫入 Azure Blob 存儲。
* 從本地檔案系統複製壓縮檔，動態解壓縮這些檔，並將提取的檔寫入 Azure 資料湖存儲 Gen2。
* 從 Azure Blob 存儲以 Gzip 壓縮文本 （CSV） 格式複製資料並將其寫入 Azure SQL 資料庫。
* 需要序列化/反序列化或壓縮/解壓縮的更多活動。

## <a name="next-steps"></a>後續步驟

請參閱其他複製活動文章：

- [複製活動概述](copy-activity-overview.md)
- [複製活動效能](copy-activity-performance.md)
