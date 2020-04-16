---
title: 取得 Azure 資料工廠中的中繼資料活動
description: 瞭解如何在數據工廠管道中使用「獲取元數據」活動。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jingwang
ms.openlocfilehash: 344ad8e106c119c1de59570d1ec4e3df5e1cc8af
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417113"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>取得 Azure 資料工廠中的中繼資料活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

可以使用「獲取中繼資料」活動檢索 Azure 資料工廠中任何數據的元資料。 您可以在以下機制中使用此活動:

- 驗證任何數據的元數據。
- 當數據準備就緒/可用時觸發管道。

下列功能可用於控制流程：

- 您可以使用條件表示式中的「獲取元資料」活動的輸出來執行驗證。
- 當條件通過執行直到迴圈滿足時,可以觸發管道。

## <a name="capabilities"></a>功能

獲取中繼資料活動將資料集作為輸入,並將中繼資料資訊作為輸出返回。 目前,支援以下連接器和相應的可檢索元數據。 返回的元數據的最大大小為 2 MB。

>[!NOTE]
>如果在自託管的集成運行時運行獲取元數據活動,則版本 3.6 或更高版本支援最新功能。

### <a name="supported-connectors"></a>支援的連接器

**檔案儲存**

| 連接器/中繼資料 | itemName<br>(檔案/資料夾) | itemType<br>(檔案/資料夾) | size<br>(檔案) | created<br>(檔案/資料夾) | lastModified<br>(檔案/資料夾) |childItems<br>(資料夾) |contentMD5<br>(檔案) | structure<br/>(檔案) | columnCount<br>(檔案) | exists<br>(檔案/資料夾) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob 儲存](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure 檔案](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [檔案系統](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- 對資料夾使用「獲取元資料」活動時,請確保對給定資料夾具有 LIST/EXECUTE 許可權。
- 對於 Amazon S3 和`lastModified`Google 雲端儲存,適用於儲存桶和密鑰,`exists`但不適用於虛擬資料夾 ,並應用於儲存桶和金鑰,但不適用於前置資料夾。
- 對於 Azure`lastModified`Blob 儲存,應用於容器和 Blob,但不適用於虛擬資料夾。
- `lastModified`篩選器當前適用於篩選子項,但不適用於指定的資料夾/檔本身。
- 對於獲取中繼活動,不支援資料夾/檔的通配符篩選器。

**關聯資料庫**

| 連接器/中繼資料 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 資料庫託管實例](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>中繼資料選項

您可以在「取得中繼資料」活動欄位清單中指定以下中繼資料類型,以檢查資料類型, 以檢查的資訊:

| 中繼資料類型 | 描述 |
|:--- |:--- |
| itemName | 檔案或資料夾的名稱。 |
| itemType | 檔案或資料夾的類型。 傳回的值`File``Folder`為或 。 |
| size | 檔的大小(以位元組為單位)。 僅適用於檔。 |
| created | 檔案或資料夾的建立日期時間。 |
| lastModified | 檔案或資料夾的上次修改日期時間。 |
| childItems | 指定資料夾的子資料夾與檔案清單。 僅適用於資料夾。 返回的值是每個子項的名稱和類型的清單。 |
| contentMD5 | 檔案的 MD5。 僅適用於檔。 |
| structure | 檔或關係資料庫表的數據結構。 傳回的值是列名稱和列類型的清單。 |
| columnCount | 檔案或關聯表中的欄數。 |
| exists| 是否存在檔、資料夾或表。 請注意,如果在`exists`「獲取元數據」欄位列表中指定,即使檔、資料夾或表不存在,活動也不會失敗。 `exists: false`而是在輸出中返回。 |

>[!TIP]
>如果要驗證檔、資料夾或表是否存在,請在「獲取元數據」活動欄位`exists`清單中指定。 然後,`exists: true/false`您可以在活動輸出中檢查結果。 如果未`exists`在欄位清單中指定,則如果未找到物件,「獲取元資料」活動將失敗。

>[!NOTE]
>從檔案儲存取得中繼資料並`modifiedDatetimeStart`配置`modifiedDatetimeEnd`或`childItems`時, 輸出將僅包括給定路徑中上次修改時間在指定範圍內的檔。 中不包括子資料夾中的專案。

## <a name="syntax"></a>語法

**取得中繼資料活動**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**資料集**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>類型屬性

目前,「獲取中繼資料」活動可以傳回以下類型的中繼資料資訊:

屬性 | 描述 | 必要
-------- | ----------- | --------
欄位清單 | 所需的元數據資訊的類型。 有關受支援的元數據的詳細資訊,請參閱本文的[元數據選項](#metadata-options)部分。 | 是 
資料集 | 要由獲取元數據活動檢索其元數據的引用數據集。 有關支援的連接器的資訊,請參閱[功能](#capabilities)部分。 有關數據集語法詳細資訊,請參閱特定的連接器主題。 | 是
格式設定 | 使用格式類型數據集時應用。 | 否
商店設置 | 使用格式類型數據集時應用。 | 否

## <a name="sample-output"></a>範例輸出

獲取元數據結果顯示在活動輸出中。 下面是兩個示例,其中顯示了廣泛的元數據選項。 您可以使用此模式, 請使用此模式: `@{activity('MyGetMetadataActivity').output.itemName}`。

### <a name="get-a-files-metadata"></a>取得檔案的中繼資料

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>取得資料夾的中繼資料

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟
瞭解資料工廠支援的其他控制串流活動:

- [執行導管活動](control-flow-execute-pipeline-activity.md)
- [ForEach 活動](control-flow-for-each-activity.md)
- [尋找活動](control-flow-lookup-activity.md)
- [網路活動](control-flow-web-activity.md)
