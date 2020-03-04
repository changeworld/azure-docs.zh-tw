---
title: 取得 Azure Data Factory 中的中繼資料活動
description: 瞭解如何在 Data Factory 管線中使用取得中繼資料活動。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: jingwang
ms.openlocfilehash: a0c07aaf27825254f776a03b9b9ca2cbeddca02d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250265"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>取得 Azure Data Factory 中的中繼資料活動

您可以使用 [取得中繼資料] 活動來抓取 Azure Data Factory 中任何資料的中繼資料。 您可以在下列案例中使用此活動：

- 驗證任何資料的中繼資料。
- 當資料已就緒/可供使用時，觸發管線。

下列功能可用於控制流程：

- 您可以使用條件運算式中的 [取得中繼資料] 活動的輸出來執行驗證。
- 您可以透過 Do 直到迴圈，以在滿足條件時觸發管線。

## <a name="capabilities"></a>功能

取得中繼資料活動會採用資料集做為輸入，並傳回中繼資料資訊做為輸出。 目前支援下列連接器和對應的可檢索中繼資料。 傳回的中繼資料大小上限為 2 MB。

>[!NOTE]
>如果您在自我裝載整合執行時間上執行 [取得中繼資料] 活動，3.6 或更新版本會支援最新的功能。

### <a name="supported-connectors"></a>支援的連接器

**檔案儲存體**

| 連接器/中繼資料 | itemName<br>(檔案/資料夾) | itemType<br>(檔案/資料夾) | size<br>(檔案) | created<br>(檔案/資料夾) | lastModified<br>(檔案/資料夾) |childItems<br>(資料夾) |contentMD5<br>(檔案) | 結構<br/>(檔案) | columnCount<br>(檔案) | exists<br>(檔案/資料夾) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob 儲存體](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) \(部分機器翻譯\) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure 檔案](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [檔案系統](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- 針對 Amazon S3 和 Google Cloud Storage，`lastModified` 會套用到 bucket 和金鑰，但不會套用至虛擬資料夾，而 `exists` 則適用于值區和金鑰，但不會套用至前置詞或虛擬資料夾。
- 針對 Azure Blob 儲存體，`lastModified` 會套用至容器和 Blob，而不是虛擬資料夾。
- `lastModified` 篩選目前適用于篩選子專案，而不是指定的資料夾/檔案本身。
- 取得中繼資料活動不支援資料夾/檔案上的萬用字元篩選。

**關係資料庫**

| 連接器/中繼資料 | 結構 | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Database 受控執行個體](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>中繼資料選項

您可以在 [取得中繼資料作用欄位] 清單中指定下列元資料類型，以取得對應的資訊：

| 中繼資料類型 | 描述 |
|:--- |:--- |
| itemName | 檔案或資料夾的名稱。 |
| itemType | 檔案或資料夾的類型。 傳回的值為 `File` 或 `Folder`。 |
| size | 檔案的大小（以位元組為單位）。 僅適用于檔案。 |
| created | 檔案或資料夾的建立日期時間。 |
| lastModified | 檔案或資料夾的上次修改日期時間。 |
| childItems | 指定資料夾中的子資料夾和檔案清單。 僅適用于資料夾。 傳回的值是每個子專案的名稱和類型清單。 |
| contentMD5 | 檔案的 MD5。 僅適用于檔案。 |
| 結構 | 檔案或關係資料庫資料表的資料結構。 傳回的值是資料行名稱和資料行類型的清單。 |
| columnCount | 檔案或關聯式資料表中的資料行數目。 |
| exists| 檔案、資料夾或資料表是否存在。 請注意，如果在 [取得元資料欄位] 清單中指定了 `exists`，即使檔案、資料夾或資料表不存在，活動也不會失敗。 相反地，會在輸出中傳回 `exists: false`。 |

>[!TIP]
>當您想要驗證檔案、資料夾或資料表是否存在時，請在 [取得中繼資料作用欄位] 清單中指定 `exists`。 然後，您可以在活動輸出中檢查 `exists: true/false` 結果。 如果未在欄位清單中指定 `exists`，如果找不到物件，則 [取得中繼資料] 活動將會失敗。

>[!NOTE]
>當您從檔案存放區取得中繼資料，並設定 `modifiedDatetimeStart` 或 `modifiedDatetimeEnd`時，輸出中的 `childItems` 只會包含指定之路徑中的檔案，而這些檔案在指定的範圍內具有上次修改時間。 中的不會包含子資料夾中的專案。

## <a name="syntax"></a>語法

**GetMetadata 活動**

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

目前，取得中繼資料活動可能會傳回下列類型的中繼資料資訊：

屬性 | 描述 | 必要項
-------- | ----------- | --------
欄位清單 | 所需的中繼資料資訊類型。 如需所支援中繼資料的詳細資訊，請參閱本文的[中繼資料選項](#metadata-options)一節。 | 是 
資料集 | 要由「取得中繼資料」活動抓取其中繼資料的參考資料集。 如需所支援連接器的詳細資訊，請參閱[功能](#capabilities)一節。 如需資料集語法的詳細資訊，請參閱特定的連接器主題。 | 是
formatSettings | 適用于使用格式類型資料集時。 | 否
storeSettings | 適用于使用格式類型資料集時。 | 否

## <a name="sample-output"></a>範例輸出

取得中繼資料結果會顯示在活動輸出中。 以下是兩個範例，其中顯示大量的中繼資料選項。 若要在後續活動中使用結果，請使用此模式： `@{activity('MyGetMetadataActivity').output.itemName}`。

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
深入瞭解 Data Factory 支援的其他控制流程活動：

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [ForEach 活動](control-flow-for-each-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
