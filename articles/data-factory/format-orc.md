---
title: Azure Data Factory 中的 ORC 格式
description: 本主題說明如何在 Azure Data Factory 中處理 ORC 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 3aa42d6060ecdd93dd97438a025c4f5e4f05ac52
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531724"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure Data Factory 中的 ORC 格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

當您想要 **剖析 ORC 檔案，或將資料寫入 ORC 格式**時，請遵循這篇文章。 

下列連接器支援 ORC 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、 [檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和 [SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 ORC 資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設為 **Orc**。 | 是      |
| location         | 檔案 (s) 的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型和支援的屬性 `location` 。 **請參閱連接器文章中的詳細資料-> 資料集屬性一節**。 | 是      |

以下是 Azure Blob 儲存體上的 ORC 資料集範例：

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            }
        }
    }
}
```

請注意下列幾點：

* 不支援複雜資料類型 (STRUCT、MAP、LIST、UNION)。
* 不支援資料行名稱中的空白字元。
* ORC 檔案有 3 種 [壓縮相關選項](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)︰NONE、ZLIB、SNAPPY。 Data Factory 支援以這些壓縮格式的任一項從 ORC 檔案讀取資料。 它會使用中繼資料裡的壓縮轉碼器來讀取資料。 不過，寫入 ORC 檔案時，Data Factory 會選擇 ZLIB，這是 ORC 的預設值。 目前沒有任何選項可覆寫這個行為。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 ORC 來源和接收所支援的屬性清單。

### <a name="orc-as-source"></a>ORC 作為來源

[複製活動*** \* 來源 \* *** ] 區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **OrcSource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在下都有自己支援的讀取設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

### <a name="orc-as-sink"></a>ORC 為接收

複製活動*** \* 接收 \* ***區段支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設為 **OrcSink**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下方的 **ORC 寫入設定** 表格。 |    否      |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在下都有自己支援的寫入設定 `storeSettings` 。 **請參閱連接器文章中的詳細資料-> 複製活動屬性一節**。 | 否       |

支援的 **ORC 寫入設定** `formatSettings` ：

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為 **OrcWriteSettings**。 | 是                                                   |
| maxRowsPerFile | 將資料寫入資料夾時，您可以選擇寫入多個檔案，並指定每個檔案的最大資料列數。  | 否 |
| fileNamePrefix | 適用于 `maxRowsPerFile` 設定時。<br> 將資料寫入多個檔案時，請指定檔案名前置詞，而導致此模式： `<fileNamePrefix>_00000.<fileExtension>` 。 如果未指定，則會自動產生檔案名前置詞。 當來源是以檔案為基礎的存放區或分割區 [選項的資料存放區](copy-activity-performance-features.md)時，此屬性不適用。  | 否 |

## <a name="using-self-hosted-integration-runtime"></a>使用自我裝載的 Integration Runtime

> [!IMPORTANT]
> 若為由自我裝載 Integration Runtime （ **例如，在**內部部署與雲端資料存放區之間）所授權的複製，您必須在 IR 機器上安裝 **64 位的 JRE 8 (JAVA Runtime Environment) 或 OpenJDK** 和 **Microsoft Visual C++ 2010** 可轉散發套件。 請參閱下列段落以取得詳細資料。

若為使用 ORC 檔案序列化/還原序列化在自我裝載 IR 上執行的複製，ADF 會先檢查 JRE 的登錄以找出 JAVA 執行時間， *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 如果找不到，則會先檢查系統變數 *`JAVA_HOME`* 以進行 OpenJDK。

- **若要使用 JRE**：64位 IR 需要64位 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **若要使用 OpenJDK**：自 IR 3.13 版起即支援。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。
- **安裝 Visual C++ 2010 可**轉散發套件： Visual C++ 2010 可轉散發套件未與自我裝載 IR 安裝一起安裝。 您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=14632)找到該程式。

> [!TIP]
> 如果您使用自我裝載的 Integration Runtime 將資料複製到 ORC 格式或從中複製資料，並出現錯誤訊息：「叫用 java 時發生錯誤，訊息： **OutOfMemoryError： java 堆積空間**」，您可以 `_JAVA_OPTIONS` 在裝載自我裝載 IR 的電腦上新增環境變數，以調整 JVM 的最小/最大堆積大小以提升這類複本，然後重新執行管線。

![在自我裝載 IR 上設定 JVM 堆積大小](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 根據預設，ADF 使用最小 64 MB 和最大1G。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
