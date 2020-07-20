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
ms.date: 05/06/2020
ms.author: jingwang
ms.openlocfilehash: a1527195296237eb8c9c309f8ac4a5911136cf77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891759"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用複製活動來保留中繼資料和 Acl

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您使用 Azure Data Factory 複製活動將資料從來源複製到接收時，在下列情況下，您也可以同時保留中繼資料和 Acl。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>保留適用于 lake 遷移的中繼資料

當您將資料從某個 data lake 遷移至另一個，包括[Amazon S3](connector-amazon-simple-storage-service.md)、 [Azure Blob](connector-azure-blob-storage.md)和[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)時，您可以選擇保留檔案中繼資料和資料。

複製活動支援在資料複製期間保留下列屬性：

- **所有客戶指定的中繼資料** 
- 和下列**五個數據存放區內建系統屬性**： `contentType` 、 `contentLanguage` （Amazon S3 除外）、、 `contentEncoding` `contentDisposition` 、 `cacheControl` 。

**處理中繼資料的差異：** Amazon S3 和 Azure 儲存體允許客戶所指定中繼資料的索引鍵中有不同的字元集。 當您選擇使用複製活動來保留中繼資料時，ADF 會自動以 ' _ ' 取代不正確字元。

當您從 Amazon S3/Azure Data Lake Storage Gen2/Azure blob 依來源將檔案複製到具有二進位格式的 Azure Data Lake Storage Gen2/azure blob 時，您可以**Preserve**在 [活動撰寫] 的 [**複製活動**  >  **設定**] 索引標籤或資料複製工具中的 [**設定**] 頁面上找到 [保留] 選項。

![複製活動保留中繼資料](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

以下是複製活動 JSON 設定的範例（請參閱 `preserve` ）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>將 Acl 從 Data Lake Storage Gen1/Gen2 保留至 Gen2

當您從 Azure Data Lake Storage Gen1 升級為 Gen2 或在 ADLS Gen2 之間複製資料時，您可以選擇保留 POSIX 存取控制清單（Acl）以及資料檔案。 如需存取控制的詳細資訊，請參閱 Azure Data Lake Storage Gen1 中的[存取控制](../data-lake-store/data-lake-store-access-control.md)和[Azure Data Lake Storage Gen2 中的存取控制](../storage/blobs/data-lake-storage-access-control.md)。

複製活動支援在資料複製期間保留下列 Acl 類型。 您可以選取一或多個類型：

- **ACL**：複製並保留檔案和目錄上的 POSIX 存取控制清單。 它會將完整的現有 Acl 從來源複製到接收。 
- **擁有**者：複製並保留檔案和目錄的擁有使用者。 需要超級使用者存取接收器 Data Lake Storage Gen2。
- **群組**：複製並保留檔案和目錄的擁有群組。 對於接收 Data Lake Storage Gen2 或擁有使用者的超級使用者存取（如果擁有使用者也是目標群組的成員）是必要的。

如果您指定從資料夾複製，當設定為 true 時，Data Factory 會複寫指定資料夾和其下的檔案和目錄的 Acl `recursive` 。 如果您指定從單一檔案複製，則會複製該檔案的 Acl。

>[!NOTE]
>當您使用 ADF 將 Acl 從 Data Lake Storage Gen1/Gen2 保留至 Gen2 時，將會覆寫接收 Gen2 對應資料夾/檔案的現有 Acl。

>[!IMPORTANT]
>當您選擇保留 Acl 時，請確定您已授與足夠的許可權，可讓 Data Factory 對您的接收 Data Lake Storage Gen2 帳戶進行操作。 例如，使用帳戶金鑰驗證，或將儲存體 Blob 資料擁有者角色指派給服務主體或受控識別。

當您使用二進位格式或二進位複製選項來將 source 設定為 Data Lake Storage Gen1/Gen2，並以二進位格式或二進位複製選項來進行 Data Lake Storage Gen2 接收時，您可以在 [資料複製工具] 或 [**複製活動**設定] 索引標籤上的 [**設定**] 頁面上找到 [**保留**] 選項，  >  **Settings**以進行活動撰寫。

![Data Lake Storage Gen1/Gen2 至 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

以下是複製活動 JSON 設定的範例（請參閱 `preserve` ）： 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
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
