---
title: 使用 Azure Data Factory 從 HDFS 複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料從雲端或內部部署 HDFS 來源複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 96603de7014419b142cc35714b891f9e4b15ec99
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405078"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 HDFS 伺服器複製資料

> [!div class="op_single_selector" title1="選取您要使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-hdfs-connector.md)
> * [目前的版本](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何將資料從 Hadoop 分散式檔案系統 (HDFS) server 複製。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援 HDFS 連接器：

- 使用支援的[來源和接收矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [刪除活動](delete-activity.md)

具體而言，HDFS 連接器支援：

- 使用 *Windows* (Kerberos) 或 *匿名* 驗證來複製檔案。
- 使用 *webhdfs* 通訊協定或 *內建 DistCp* 支援來複製檔案。
- 依原樣複製檔案，或使用 [支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> 請確定整合執行時間可以存取 Hadoop 叢集的 *所有* [名稱節點伺服器]： [名稱節點埠] 和 [資料節點伺服器]： [資料節點埠]。 預設 [名稱節點埠] 是50070，預設 [資料節點埠] 是50075。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 HDFS 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 HDFS 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | *Type*屬性必須設定為*Hdfs*。 | 是 |
| url |HDFS 的 URL |是 |
| authenticationType | 允許的值為 [ *匿名* ] 或 [ *Windows*]。 <br><br> 若要設定您的內部部署環境，請參閱「 [針對 HDFS 連接器使用 Kerberos 驗證](#use-kerberos-authentication-for-the-hdfs-connector) 」一節。 |是 |
| userName |Windows 驗證的使用者名稱。 針對 Kerberos 驗證，請指定** \<username> @ \<domain> .com**。 |是 Windows 驗證) 的 ( |
| 密碼 |Windows 驗證的密碼。 將此欄位標示為 SecureString，以安全地將它儲存在您的 data factory 中，或 [參考儲存在 Azure key vault 中的秘密](store-credentials-in-key-vault.md)。 |是 (適用於 Windows 驗證) |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 若要深入瞭解，請參閱「 [必要條件](#prerequisites) 」一節。 如果未指定整合執行時間，服務會使用預設 Azure Integration Runtime。 |否 |

**範例：使用匿名驗證**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：使用 Windows 驗證**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集之區段和屬性的完整清單，請參閱 [Azure Data Factory 中的資料集](concepts-datasets-linked-services.md)。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

以下是針對 `location` 以格式為基礎的資料集設定中的 HDFS 支援的屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 資料集內的 *type* 屬性 `location` 必須設定為 *HdfsLocation*。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定路徑。 | 否       |
| fileName   | 指定之 folderPath 下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過這項設定，並在 [活動來源設定] 中指定檔案名。 | 否       |

**範例︰**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)。 本節提供 HDFS 來源所支援的屬性清單。

### <a name="hdfs-as-source"></a>HDFS 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

以下是針對 `storeSettings` 以格式為基礎的複製來源設定下的 HDFS 支援的屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 下的 *類型* 屬性 `storeSettings` 必須設為 **HdfsReadSettings**。 | 是                                           |
| ***找出要複製的檔案*** |  |  |
| 選項 1：靜態路徑<br> | 從資料集所指定的資料夾或檔案路徑複製。 若您想要複製資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項 2：萬用字元<br>- wildcardFolderPath | 含有萬用字元的資料夾路徑，可用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。 `^`如果您的實際資料夾名稱裡面有萬用字元或這個 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱 [資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 2：萬用字元<br>- wildcardFileName | 在指定的 folderPath/wildcardFolderPath 下，用來篩選來源檔案的檔案名。 <br>允許的萬用字元為： `*` (符合零或多個字元) 和 `?` (符合零或單一字元) ; `^` 如果您的實際資料夾名稱包含萬用字元或此 escape 字元，請使用來進行 escape。  如需更多範例，請參閱 [資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示複製指定的檔案集。 指向文字檔，其中包含您想要複製的檔案清單 (每行一個檔案，以及資料集) 中設定之路徑的相對路徑。<br/>當您使用此選項時，請勿在資料集中指定檔案名。 如需更多範例，請參閱檔案 [清單範例](#file-list-examples)。 |否 |
| ***其他設定*** |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當 `recursive` 設定為 *true* 且接收是檔案型存放區時，就不會在接收時複製或建立空的資料夾或子資料夾。 <br>允許的值為 *true* (預設值) 和 *false*。<br>設定 `fileListPath` 時，不適用此屬性。 |否 |
| deleteFilesAfterCompletion | 指出是否要在成功移至目的地存放區之後，從來源存放區刪除二進位檔案。 檔案刪除是針對每個檔案，因此當複製活動失敗時，您會看到部分檔案已複製到目的地並從來源刪除，其他檔案仍在來源存放區上。 <br/>這個屬性只在二進位檔案複製案例中有效。 預設值： false。 |否 |
| modifiedDatetimeStart    | 檔案會根據 *上次修改*的屬性進行篩選。 <br>如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。<br/>設定 `fileListPath` 時，不適用此屬性。 | 否                                            |
| modifiedDatetimeEnd      | 同上。  
| enablePartitionDiscovery | 針對已分割的檔案，指定是否從檔案路徑剖析分割區，並將它們新增為其他來源資料行。<br/>允許的值為 **false** (預設) 和 **true**。 | 否                                            |
| partitionRootPath | 當資料分割探索已啟用時，請指定絕對根路徑，以便將分割的資料夾讀取為數據行。<br/><br/>如果未指定，則預設為<br/>-當您在資料集或來源上的檔案清單中使用檔案路徑時，資料分割根路徑是在資料集中設定的路徑。<br/>-當您使用萬用字元資料夾篩選時，資料分割根路徑是第一個萬用字元之前的子路徑。<br/><br/>例如，假設您將資料集中的路徑設定為 "root/folder/year = 2020/month = 08/day = 27"：<br/>-如果您將資料分割根路徑指定為 "root/folder/year = 2020"，則除了檔案內的資料行之外，複製活動會分別產生兩個數據行， `month` 以及 `day` 值為 "08" 和 "27" 的資料行。<br/>-如果未指定資料分割根路徑，將不會產生額外的資料行。 | 否                                            |
| maxConcurrentConnections | 可以同時連線到儲存體存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才需要指定值。 | 否                                            |
| ***DistCp 設定*** |  | |
| distcpSettings | 當您使用 HDFS DistCp 時，所要使用的屬性群組。 | 否 |
| resourceManagerEndpoint | YARN (還有另一個資源 Negotiator) 端點 | 是，如果使用 DistCp |
| tempScriptPath | 用來儲存 temp DistCp 命令腳本的資料夾路徑。 腳本檔是由 Data Factory 產生，並會在複製作業完成後移除。 | 是，如果使用 DistCp |
| distcpOptions | 對於 DistCp 命令提供的其他選項。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>資料夾和檔案篩選範例

如果您使用萬用字元篩選器搭配資料夾路徑和檔案名，本節會描述所產生的行為。

| folderPath | fileName             | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (空白，使用預設值) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (空白，使用預設值) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明使用複製活動來源中的檔案清單路徑所產生的行為。 它會假設您有下列源資料夾結構，而且想要複製粗體字型的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | Azure Data Factory 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;中繼資料<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集中：**<br>- 資料夾路徑：`root/FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`root/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向相同資料存放區中的文字檔，其中包含您想要複製的檔案清單 (每行一個檔案，以及資料集) 中設定之路徑的相對路徑。 |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>使用 DistCp 從 HDFS 複製資料

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 是 hadoop 原生命令列工具，可在 hadoop 叢集中執行分散式複製。 當您在 DistCp 中執行命令時，它會先列出所有要複製的檔案，然後在 Hadoop 叢集中建立數個對應工作。 每個對應工作會執行從來源到接收的二進位複製。

複製活動支援使用 DistCp 將檔案複製到 Azure Blob 儲存體 (包括 [分段複製](copy-activity-performance.md)) 或 azure data lake store。 在此情況下，DistCp 可以利用叢集的電源，而不是在自我裝載整合執行時間上執行。 使用 DistCp 可提供較佳的複製輸送量，特別是當您的叢集非常強大時。 複製活動會根據 data factory 中的設定，自動建立 DistCp 命令、將它提交至您的 Hadoop 叢集，以及監視複製狀態。

### <a name="prerequisites"></a>Prerequisites

若要使用 DistCp 將檔案從 HDFS 複製到 Azure Blob 儲存體 (包括分段複製) 或 Azure data lake store），請確定您的 Hadoop 叢集符合下列需求：

* MapReduce 和 YARN 服務已啟用。  
* YARN 版本是2.5 或更新版本。  
* HDFS 伺服器會與您的目標資料存放區整合： **Azure Blob 儲存體** 或 **Azure Data Lake 存放區 (ADLS Gen1) **： 

    - 自從 Hadoop 2.7 開始即原生支援 Azure Blob FileSystem。 您只需要在 Hadoop 環境設定中指定 JAR 路徑。
    - Azure Data Lake Store FileSystem 是從 Hadoop 3.0.0-alpha1 開始封裝。 如果您的 Hadoop 叢集版本早于該版本，您需要從 [這裡](https://hadoop.apache.org/releases.html)手動將 Azure Data Lake 存放區相關的 JAR 套件 (azure-datalake-store) 到叢集中，並在 Hadoop 環境設定中指定 JAR 檔案路徑。

* 準備 HDFS 中的暫存資料夾。 這個暫存資料夾是用來儲存 DistCp shell 腳本，因此它會佔用 KB 層級的空間。
* 請確定 HDFS 連結服務中提供的使用者帳戶具有下列許可權：
   * 在 YARN 中提交應用程式。
   * 在暫存資料夾下建立子資料夾和讀取/寫入檔案。

### <a name="configurations"></a>組態

針對 DistCp 相關的設定和範例，請移至 [HDFS 作為來源](#hdfs-as-source) 一節。

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>針對 HDFS 連接器使用 Kerberos 驗證

有兩個選項可設定內部部署環境，以針對 HDFS 連接器使用 Kerberos 驗證。 您可以選擇更適合您的情況。
* 選項1： [加入 Kerberos 領域中的自我裝載整合執行時間電腦](#kerberos-join-realm)
* 選項 2：[啟用 Windows 網域和 Kerberos 領域之間的相互信任](#kerberos-mutual-trust)

針對任一個選項，請務必開啟 Hadoop 叢集的 webhdfs：

1. 建立 webhdfs 的 HTTP 主體和 keytab。

    > [!IMPORTANT]
    > HTTP Kerberos 主體的開頭必須是 "**HTTP/**"，根據 KERBEROS HTTP SPNEGO 規格。

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. HDFS 設定選項：在中新增下列三個屬性 `hdfs-site.xml` 。
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>選項1：加入 Kerberos 領域中的自我裝載整合執行時間電腦

#### <a name="requirements"></a>需求

* 自我裝載整合執行時間電腦必須加入 Kerberos 領域，且無法加入任何 Windows 網域。

#### <a name="how-to-configure"></a>如何設定

**在 KDC 伺服器上：**

建立 Azure Data Factory 要使用的主體，並指定密碼。

> [!IMPORTANT]
> 使用者名稱不應包含主機名稱。

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**在自我裝載整合執行時間電腦上：**

1.  執行 Ksetup 公用程式來設定 Kerberos 金鑰發佈中心 (KDC) 伺服器和領域。

    電腦必須設定為工作組成員，因為 Kerberos 領域與 Windows 網域不同。 您可以藉由執行下列命令來設定 Kerberos 領域並新增 KDC 伺服器，以達到此設定。 將 *REALM.COM* 取代為您自己的領域名稱。

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    執行這些命令之後，請重新開機電腦。

2.  使用命令驗證設定 `Ksetup` 。 輸出應該會像這樣：

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**在您的 data factory 中：**

* 使用 Windows 驗證搭配您的 Kerberos 主體名稱和密碼，來設定 HDFS 連接器，以連接到 HDFS 資料來源。 如需設定詳細資料，請核取 [ [HDFS 連結服務屬性](#linked-service-properties) ] 區段。

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>選項 2：啟用 Windows 網域和 Kerberos 領域之間的相互信任

#### <a name="requirements"></a>需求

*   自我裝載整合執行時間電腦必須加入 Windows 網域。
*   您需要更新網域控制站設定的權限。

#### <a name="how-to-configure"></a>如何設定

> [!NOTE]
> 將下列教學課程中的 REALM.COM 和 AD.COM 取代為您自己的領域名稱和網域控制站。

**在 KDC 伺服器上：**

1. 編輯 *krb5.keytab* 檔案中的 kdc 設定，讓 kdc 藉由參考下列設定範本來信任 Windows 網域。 根據預設，設定會位於 */etc/krb5.conf*。

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   設定檔案之後，請重新開機 KDC 服務。

2. 使用下列命令，在 KDC 伺服器中準備名為 *krbtgt/REALM .com \@ AD.COM* 的主體：

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. 在 *hadoop.security.auth_to_local* HDFS 服務設定檔案中，新增 `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`。

**在網域控制站上：**

1.  執行下列 `Ksetup` 命令來新增領域專案：

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  建立 Windows 網域對 Kerberos 領域的信任關係。 [password] 是主體 *krbtgt/REALM.COM\@AD.COM* 的密碼。

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  選取 Kerberos 中使用的加密演算法。

    a. 選取**伺服器管理員**  >  **群組原則管理**  >  **網域**  >  **群組原則物件**  >  **預設或作用中網域原則**]，然後選取 [**編輯**]。

    b. 在 [**群組原則管理編輯器**] 窗格中**Computer Configuration**，選取 [  >  **Policies**  >  **Windows 設定**  >  **安全性設定**  >  **本機原則**安全性選項] 的 [電腦設定原則]  >  ** **，然後設定 [**網路安全性：設定 Kerberos 允許的加密類型**]。

    c. 選取您要在連接到 KDC 伺服器時使用的加密演算法。 您可以選取所有選項。

    ![[網路安全性：設定 Kerberos 允許的加密類型] 窗格的螢幕擷取畫面](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. 使用 `Ksetup` 命令來指定要在指定領域上使用的加密演算法。

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  建立網域帳戶和 Kerberos 主體之間的對應，讓您可以在 Windows 網域中使用 Kerberos 主體。

    a. 選取 [系統**管理工具**]  >  **Active Directory 消費者和電腦**。

    b. 選取 [檢視] > [進階功能]，以設定進階功能。

    c. 在 [ **Advanced Features** ] 窗格中，以滑鼠右鍵按一下您要建立對應的帳戶，然後在 [ **名稱** 對應] 窗格中，選取 [ **Kerberos 名稱** ] 索引標籤。

    d. 加入來自領域的主體。

       ![[安全性識別對應] 窗格](media/connector-hdfs/map-security-identity.png)

**在自我裝載整合執行時間電腦上：**

* 執行下列 `Ksetup` 命令來新增領域專案。

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**在您的 data factory 中：**

* 使用 Windows 驗證搭配您的網域帳戶或 Kerberos 主體連接到 HDFS 資料來源，來設定 HDFS 連接器。 如需設定的詳細資訊，請參閱「 [HDFS 連結服務屬性](#linked-service-properties) 」一節。

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需查閱活動屬性的詳細資訊，請參閱 [Azure Data Factory 中的查閱活動](control-flow-lookup-activity.md)。

## <a name="delete-activity-properties"></a>刪除活動屬性

如需刪除活動屬性的相關資訊，請參閱 [Azure Data Factory 中的刪除活動](delete-activity.md)。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>為了回溯相容性，仍支援下列模型。 建議您使用先前討論的新模型，因為 Azure Data Factory 撰寫 UI 已切換為產生新的模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 *類型* 屬性必須設定為檔案 *共用* |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選。 允許的萬用字元 `*` (符合零或多個字元) 和 `?` (符合零或單一字元) ; `^` 如果您的實際檔案名包含萬用字元或此 escape 字元，請使用來進行 escape。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |是 |
| fileName |  指定之 "folderPath" 下檔案的名稱或萬用字元篩選。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>若為篩選，允許的萬用字元 `*` (符合零或多個字元) ，而且 `?` (符合零或單一字元) 。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>`^`如果您的實際資料夾名稱裡面有萬用字元或這個 escape 字元，請使用來進行 escape。 |否 |
| modifiedDatetimeStart | 檔案會根據 *上次修改*的屬性進行篩選。 如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 此時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要將檔案篩選套用至大量的檔案時，資料移動的整體效能將受到影響。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據 *上次修改*的屬性進行篩選。 如果檔案的上次修改時間在的範圍內，則會選取這些檔案 `modifiedDatetimeStart` `modifiedDatetimeEnd` 。 此時間會以 *2018-12-01T05：00： 00Z*的格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要將檔案篩選套用至大量的檔案時，資料移動的整體效能將受到影響。 <br/><br/> 屬性可以是 Null，這表示不會將任何檔案屬性篩選套用至資料集。  當 `modifiedDatetimeStart` 有 datetime 值但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。  當 `modifiedDatetimeEnd` 有 datetime 值但 `modifiedDatetimeStart` 為 Null 時，表示已選取其上次修改屬性小於 datetime 值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析特定格式的檔案，以下是支援的檔案格式類型：*TextFormat*、*JsonFormat*、*AvroFormat*、*OrcFormat*、*ParquetFormat*。 將格式下的 *type* 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[ORC 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為： *Gzip*、 *Deflate*、 *Bzip2*和 *ZipDeflate*。<br/>支援的層級為：*Optimal* 和 *Fastest*。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製具有指定名稱的單一檔案，請使用資料夾元件和**檔案名**指定**folderPath** 。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。

**範例︰**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>舊版複製活動來源模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 *type* 屬性必須設為 *>hdfssource*。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 *true* 且接收是檔案型存放區時，將不會在接收時複製或建立空的資料夾或子資料夾。<br/>允許的值為 *true* (預設值) 和 *false*。 | 否 |
| distcpSettings | 當您使用 HDFS DistCp 時的屬性群組。 | 否 |
| resourceManagerEndpoint | YARN Resource Manager 端點 | 是，如果使用 DistCp |
| tempScriptPath | 用來儲存 temp DistCp 命令腳本的資料夾路徑。 腳本檔是由 Data Factory 產生，並會在複製作業完成後移除。 | 是，如果使用 DistCp |
| distcpOptions | DistCp 命令會提供其他選項。 | 否 |
| maxConcurrentConnections | 可以同時連線到儲存體存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才需要指定值。 | 否 |

**範例：使用 DistCp 複製活動中的 HDFS 來源**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
