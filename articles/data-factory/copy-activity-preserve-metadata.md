---
title: 使用 Azure Data Factory 中的複製活動保留中繼資料和 Acl
description: 瞭解如何使用 Azure Data Factory 中的「複製活動」，在複製期間保留中繼資料和 Acl。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: a96b04df56dc7d5ea26463073d673275b8a4a8c4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015072"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的複製活動保留中繼資料和 Acl

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您使用 Azure Data Factory 複製活動將資料從來源複製到接收時，在下列案例中，您也可以同時保留中繼資料和 Acl。

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> 保留適用于 lake 遷移的中繼資料

當您將資料從一個 data lake 遷移到另一個，包括 [Amazon S3](connector-amazon-simple-storage-service.md)、 [Azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)和 [Azure 檔案儲存體](connector-azure-file-storage.md)時，您可以選擇保留檔案中繼資料以及資料。

複製活動支援在資料複製期間保留下列屬性：

- **所有客戶指定的中繼資料** 
- 以及下列 **五個數據存放區內建的系統屬性**： `contentType` 、 `contentLanguage` (除了 Amazon S3) 、 `contentEncoding` 、 `contentDisposition` 、之外 `cacheControl` 。

**處理中繼資料的差異：** Amazon S3 和 Azure 儲存體允許客戶所指定中繼資料的索引鍵中有不同組字元。 當您選擇使用複製活動保留中繼資料時，ADF 會自動以 ' _ ' 取代不正確字元。

當您從 Amazon S3/Azure Data Lake Storage Gen2/azure blob/azure 檔案儲存體依原樣將檔案複製到具有二進位格式的 Azure Data Lake Storage Gen2/azure blob/azure 檔案儲存體時，您可以在活動撰寫的 [**複製活動** 設定] 索引標籤 **Preserve**  >  **Settings** 或資料複製工具中的 [**設定**] 頁面上找到 [保留] 選項。

![複製活動保留中繼資料](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

以下是複製活動 JSON 設定的範例 (請參閱 `preserve`) ： 

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

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> 將 Acl 從 Data Lake Storage Gen1/Gen2 保留至 Gen2

當您從 Azure Data Lake Storage Gen1 升級以 Gen2 或複製 ADLS Gen2 之間的資料時，您可以選擇保留 POSIX 存取控制清單 (Acl) 和資料檔案。 如需存取控制的詳細資訊，請參閱 Azure Data Lake Storage Gen2 中的 Azure Data Lake Storage Gen1 和[存取控制](../storage/blobs/data-lake-storage-access-control.md)中的[存取控制](../data-lake-store/data-lake-store-access-control.md)。

複製活動支援在資料複製期間保留下列類型的 Acl。 您可以選取一或多個類型：

- **ACL**：複製及保留檔案和目錄上的 POSIX 存取控制清單。 它會將完整的現有 Acl 從來源複製到接收。 
- **擁有** 者：複製並保留檔案和目錄的擁有使用者。 需要超級使用者的接收 Data Lake Storage Gen2 存取權。
- **群組**：複製並保留檔案和目錄的擁有群組。 如果擁有使用者也是目標群組) 的成員，則超級使用者對接收 Data Lake Storage Gen2 或擁有使用者 (的存取權。

如果您指定從資料夾複製，則如果設為 true，Data Factory 會複寫該指定資料夾的 Acl 和其下的檔案和目錄 `recursive` 。 如果您指定要從單一檔案複製，則會複製該檔案上的 Acl。

>[!NOTE]
>當您使用 ADF 將 Acl 從 Data Lake Storage Gen1/Gen2 保留至 Gen2 時，接收上的現有 Acl Gen2 對應的資料夾/檔案將會遭到覆寫。

>[!IMPORTANT]
>當您選擇保留 Acl 時，請務必授與足夠的許可權給 Data Factory，以對您的接收 Data Lake Storage Gen2 帳戶執行操作。 例如，使用帳戶金鑰驗證，或將儲存體 Blob 資料擁有者角色指派給服務主體或受控識別。

當您將來源設定為具有二進位格式或二進位複製選項的 Data Lake Storage Gen1/Gen2，以及以二進位格式或二進位複製選項 Data Lake Storage Gen2 的接收時，您可以在 [設定] 頁面的 [設定] 頁面上，找到 [**設定**] 資料複製頁面上的 [**保留**] 選項，也就是活動撰寫的 [**複製活動**  >  **設定**]

![Data Lake Storage Gen1/Gen2 to Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

以下是複製活動 JSON 設定的範例 (請參閱 `preserve`) ： 

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
