---
title: 在 Azure Data Factory 中使用複製活動來保留中繼資料和 Acl
description: 瞭解如何在 Azure Data Factory 中使用複製活動，在複製期間保留中繼資料和 Acl。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260835"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用複製活動來保留中繼資料和 Acl

當您使用 Azure Data Factory 複製活動將資料從來源複製到接收時，在下列情況下，您也可以同時保留中繼資料和 Acl。

## <a name="preserve-metadata"></a>保留適用于 lake 遷移的中繼資料

當您將資料從某個 data lake 遷移至另一個，包括[Amazon S3](connector-amazon-simple-storage-service.md)、 [Azure Blob](connector-azure-blob-storage.md)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)時，您可以選擇保留檔案中繼資料和資料。

複製活動支援在資料複製期間保留下列屬性：

- **所有客戶指定的中繼資料** 
- 和下列**五個數據存放區內建系統屬性**： `contentType`、`contentLanguage` （Amazon S3 除外）、`contentEncoding`、`contentDisposition`、`cacheControl`。

當您從 Amazon S3/Azure Data Lake Storage Gen2/Azure Blob 依來源將檔案複製到具有二進位格式的 Azure Data Lake Storage Gen2/Azure Blob 時，您可以在 [活動撰寫] 的 [**複製活動** > **設定**] 索引標籤或資料複製工具中的 [**設定**] 頁面上找到 [**保留**] 選項。

![複製活動保留中繼資料](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

以下是複製活動 JSON 設定的範例（請參閱 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls"></a>將 Acl 從 Data Lake Storage Gen1 保留至 Gen2

當您從 Azure Data Lake Storage Gen1 升級至 Gen2 時，您可以選擇保留 POSIX 存取控制清單（Acl）以及資料檔案。 如需存取控制的詳細資訊，請參閱 Azure Data Lake Storage Gen1 中的[存取控制](../data-lake-store/data-lake-store-access-control.md)和[Azure Data Lake Storage Gen2 中的存取控制](../storage/blobs/data-lake-storage-access-control.md)。

複製活動支援在資料複製期間保留下列 Acl 類型。 您可以選取一或多個類型：

- **ACL**：複製並保留檔案和目錄上的 POSIX 存取控制清單。 它會將完整的現有 Acl 從來源複製到接收。 
- **擁有**者：複製並保留檔案和目錄的擁有使用者。 需要超級使用者存取接收器 Data Lake Storage Gen2。
- **群組**：複製並保留檔案和目錄的擁有群組。 對於接收 Data Lake Storage Gen2 或擁有使用者的超級使用者存取（如果擁有使用者也是目標群組的成員）是必要的。

如果您指定從資料夾複製，當 `recursive` 設定為 true 時，Data Factory 會複寫該資料夾及其下的檔案和目錄的 Acl。 如果您指定從單一檔案複製，則會複製該檔案的 Acl。

>[!NOTE]
>當您使用 ADF 將 Acl 從 Data Lake Storage Gen1 保留至 Gen2 時，將會覆寫 Gen2 對應資料夾/檔案上的現有 Acl。

>[!IMPORTANT]
>當您選擇保留 Acl 時，請確定您已授與足夠的許可權，可讓 Data Factory 對您的接收 Data Lake Storage Gen2 帳戶進行操作。 例如，使用帳戶金鑰驗證，或將儲存體 Blob 資料擁有者角色指派給服務主體或受控識別。

當您使用二進位格式或二進位複製選項來將來源設定為 Data Lake Storage Gen1，並以二進位格式或二進位複製選項來進行 Data Lake Storage Gen2 接收時，您可以在 [資料複製工具] 或 [**複製活動** > **設定**] 索引標籤上的 [**設定**] 頁面上找到 [**保留**] 選項，以進行活動撰寫。

![Data Lake Storage Gen1 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

以下是複製活動 JSON 設定的範例（請參閱 `preserve`）： 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>後續步驟

請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能](copy-activity-performance.md)
