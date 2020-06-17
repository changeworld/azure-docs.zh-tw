---
title: 使用 Azure Data Factory 從 HDFS 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從雲端或內部部署 HDFS 來源將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: f39fb50c5044cacb04b3b147a0160dd23c9eb2d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657088"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>使用 Azure Data Factory 從 HDFS 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-hdfs-connector.md)
> * [目前的版本](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何從 HDFS 伺服器複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 HDFS 連接器支援下列活動：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

具體而言，此 HDFS 連接器支援：

- 使用 **Windows** (Kerberos) 或**匿名**驗證複製檔案。
- 使用 **webhdfs** 通訊協定或**內建 DistCp** 支援複製檔案。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析/產生檔案。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> 請確定 Integration Runtime 可以存取 Hadoop 叢集的**所有** [名稱節點伺服器]:[名稱節點連接埠] 和 [資料節點伺服器]:[資料節點連接埠]。 預設 [名稱節點連接埠] 是 50070，而預設 [資料節點連接埠] 是 50075。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 HDFS 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 HDFS 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Hdfs**。 | 是 |
| url |到 HDFS 的 URL |是 |
| authenticationType | 允許的值包括：**匿名**或 **Windows**。 <br><br> 若要對 HDFS 連接器使用 **Kerberos 驗證**，請參閱[此章節](#use-kerberos-authentication-for-hdfs-connector)來據以設定您的內部部署環境。 |是 |
| userName |Windows 驗證的使用者名稱。 Kerberos 驗證請指定 `<username>@<domain>.com`。 |是 (適用於 Windows 驗證) |
| 密碼 |Windows 驗證的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 (適用於 Windows 驗證) |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 從[必要條件](#prerequisites)一節深入了解。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型資料集內的 `location` 設定下，HDFS 支援下列屬性：

| 屬性   | 描述                                                  | 必要 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 在資料集的 `location` 下，type 屬性必須設定為 **HdfsLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在活動來源設定中指定。 | 否       |
| fileName   | 所指定 folderPath 下方的檔案名稱。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在活動來源設定中指定。 | 否       |

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 HDFS 來源所支援的屬性清單。

### <a name="hdfs-as-source"></a>HDFS 作為來源

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

在格式型複製來源內 `storeSettings` 設定下，HDFS 支援下列屬性：

| 屬性                 | 描述                                                  | 必要                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 下的 type 屬性必須設定為 **HdfsReadSettings**。 | 是                                           |
| 找出要複製的檔案： |  |  |
| 選項 1：靜態路徑<br> | 請從資料集內的指定資料夾/檔案路徑複製。 如果您想要複製資料夾中的所有檔案，請另外將 `wildcardFileName` 指定為 `*`。 |  |
| 選項 2：萬用字元<br>- wildcardFolderPath | 含有萬用字元的資料夾路徑，可用來篩選來源資料夾。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| 選項 2：萬用字元<br>- wildcardFileName | 在特定 folderPath/wildcardFolderPath 下含有萬用字元的檔案名稱，用於篩選來源檔案。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 是 |
| 選項 3：檔案清單<br>- fileListPath | 表示要複製指定的檔案集。 請指向文字檔，內有您要複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。<br/>使用此選項時，請勿在資料集內指定檔案名稱。 [檔案清單範例](#file-list-examples)有更多範例可供參閱。 |否 |
| ***其他設定：*** |  | |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 <br>允許的值為 **true** (預設值) 和 **false**。<br>設定 `fileListPath` 時，此屬性不適用。 |否 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 <br>如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。<br/>設定 `fileListPath` 時，此屬性不適用。 | 否                                            |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否                                            |
| ***DistCp 設定：*** |  | |
| distcpSettings | 使用 HDFS DistCp 時的屬性群組。 | 否 |
| resourceManagerEndpoint | Yarn Resource Manager 端點 | 若使用 DistCp 則為「是」 |
| tempScriptPath | 資料夾路徑，用來儲存暫存 DistCp 命令指令碼。 指令碼檔案是由 Data Factory 產生，並會在複製作業完成後移除。 | 若使用 DistCp 則為「是」 |
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

本節描述含有萬用字元篩選之資料夾路徑和檔案名稱所產生的行為。

| folderPath | fileName             | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (空白，使用預設值) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (空白，使用預設值) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>檔案清單範例

本節說明在複製活動來源中使用檔案清單路徑時所產生的行為。

假設您的來源資料夾結構如下，且想要複製以粗體標示的檔案：

| 範例來源結構                                      | FileListToCopy.txt 中的內容                             | ADF 設定                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **在資料集內：**<br>- 資料夾路徑：`root/FolderA`<br><br>**在複製活動來源中：**<br>- 檔案清單路徑：`root/Metadata/FileListToCopy.txt` <br><br>檔案清單路徑指向同一個資料存放區中的文字檔，內有您想複製的檔案清單，每行一個檔案，以資料集內所設定路徑的相對路徑表示。 |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>使用 DistCp 從 HDFS 複製資料

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 是 Hadoop 原生命令列工具，可用來執行 Hadoop 叢集中的分散式複製。 執行 Distcp 命令時，它會先列出將複製的所有檔案，並將數個對應工作建立到 Hadoop 叢集中，而且每個對應工作會執行從來源到接收的二進位複製。

「複製活動」支援使用 DistCp，將檔案依現狀複製到 Azure Blob (包括[分段複製](copy-activity-performance.md)或 Azure Data Lake Store)；在此情況下，可充分發揮叢集的功用，而不是在「自我裝載整合執行階段」上執行。 尤其是，如果您的叢集相當強大，可提供更好的複製輸送量。 根據 Azure Data Factory 中的設定而定，複製活動會自動建構 distcp 命令，並提交至 Hadoop 叢集，然後監視複製狀態。

### <a name="prerequisites"></a>Prerequisites

若要使用 DistCp 從 HDFS 將檔案依原樣複製到 Azure Blob (包括分段複製) 或 Azure Data Lake Store，請確定 Hadoop 叢集符合下列需求：

1. MapReduce 和 Yarn 服務已啟用。
2. Yarn 是 2.5 或更高版本。
3. HDFS 伺服器與目標資料存放區 (Azure Blob 或 Azure Data Lake Store) 整合：

    - 自從 Hadoop 2.7 開始即原生支援 Azure Blob FileSystem。 您只需要在 Hadoop env config 中指定 jar 路徑。
    - Azure Data Lake Store FileSystem 是從 Hadoop 3.0.0-alpha1 開始封裝。 如果 Hadoop 叢集低於該版本，您需要從[這裡](https://hadoop.apache.org/releases.html)手動將 ADLS 相關 jar 封裝 (azure-datalake-store.jar) 匯入至叢集，並在 Hadoop env config 中指定 jar 路徑。

4. 準備 HDFS 中的暫存資料夾。 此暫存資料夾是用來儲存 DistCp 殼層指令碼，因此會佔用 KB 層級的空間。
5. 確定 HDFS 連結服務中提供的使用者帳戶擁有下列權限：a) 提交 Yarn 中的應用程式；b) 建立子資料夾、讀取/寫入上層暫存資料夾下的檔案。

### <a name="configurations"></a>組態

請參閱 [HDFS 作為來源](#hdfs-as-source)一節中的 DistCp 相關設定和範例。

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>對 HDFS 連接器使用 Kerberos 驗證

有兩種選項可以設定內部部署環境，以便在 HDFS 連接器中使用 Kerberos 驗證。 您可以選擇較適合您案例的選項。
* 選項 1：[加入 Kerberos 領域中的自我裝載整合執行階段機器](#kerberos-join-realm)
* 選項 2：[啟用 Windows 網域和 Kerberos 領域之間的相互信任](#kerberos-mutual-trust)

### <a name="option-1-join-self-hosted-integration-runtime-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>選項 1：加入 Kerberos 領域中的自我裝載整合執行階段機器

#### <a name="requirements"></a>需求

* 自我裝載整合執行階段機器必須加入 Kerberos 領域，且不可加入任何 Windows 網域。

#### <a name="how-to-configure"></a>如何設定

**在自我裝載整合執行階段機器上：**

1.  執行 **Ksetup** 公用程式來設定 Kerberos KDC 伺服器和領域。

    電腦必須設定為工作群組的成員，因為 Kerberos 領域與 Windows 網域不同。 若要進行此操作，您可以設定 Kerberos 領域並新增 KDC 伺服器，如下所示。 視需要使用您的領域來取代 *REALM.COM*。

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    在執行這 2 個命令後**重新啟動**電腦。

2.  使用 **Ksetup** 命令來確認設定。 輸出應該會像這樣：

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**在 Azure Data Factory 中：**

* 使用 **Windows 驗證**以及您用來連線到 HDFS 資料來源的 Kerberos 主體名稱和密碼，來設定 HDFS 連接器。 檢查組態詳細資料上的 [HDFS 連結服務屬性](#linked-service-properties)區段。

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>選項 2：啟用 Windows 網域和 Kerberos 領域之間的相互信任

#### <a name="requirements"></a>需求

*   自我裝載整合執行階段機器必須加入 Windows 網域。
*   您需要更新網域控制站設定的權限。

#### <a name="how-to-configure"></a>如何設定

> [!NOTE]
> 在下列教學課程中，視需要使用您的領域和網域控制站來取代 REALM.COM 和 AD.COM。

**在 KDC 伺服器上︰**

1. 編輯 **krb5.conf** 檔案中的 KDC 組態，讓 KDC 信任參考下列組態範本的 Windows 網域。 根據預設，設定會位於 **/etc/krb5.conf**。

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

   設定之後**重新啟動** KDC 服務。

2. 使用下列命令，在 KDC 伺服器中準備名為 **krbtgt/REALM.COM\@AD.COM** 的主體︰

           Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3. 在 **hadoop.security.auth_to_local** HDFS 服務組態檔中，新增 `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`。

**在網域控制站上：**

1.  執行下列 **Ksetup** 命令來新增領域項目：

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  建立 Windows 網域到 Kerberos 領域的信任關係。 [password] 是主體 **krbtgt/REALM.COM\@AD.COM** 的密碼。

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  選取 Kerberos 所使用的加密演算法。

    1. 移至 [伺服器管理員] > [群組原則管理] > [網域] > [群組原則物件] > [預設或作用中的網域原則]，然後進行編輯。

    2. 在 [群組原則管理編輯器] 快顯視窗中，移至 [電腦設定] > [原則] > [Windows 設定] > [安全性設定] > [本機原則] > [安全性選項]，並設定 [網路安全性：設定 Kerberos 允許的加密類型]。

    3. 選取您想要在連線至 KDC 時使用的加密演算法。 一般來說，您可以直接選取所有選項。

        ![設定 Kerberos 的加密類型](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. 使用 **Ksetup** 命令，指定要用於特定 REALM 的加密演算法。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  建立網域帳戶與 Kerberos 主體之間的對應，以便在 Windows 網域中使用 Kerberos 主體。

    1. 啟動 [系統管理工具] > [Active Directory 使用者和電腦]。

    2. 按一下 [檢視] > [進階功能] 來設定進階功能。

    3. 找到您要用以建立對應的帳戶，然後按一下滑鼠右鍵以檢視 [名稱對應] > 按一下 [Kerberos 名稱] 索引標籤。

    4. 加入來自領域的主體。

        ![對應安全性身分識別](media/connector-hdfs/map-security-identity.png)

**在自我裝載整合執行階段機器上：**

* 執行下列 **Ksetup** 命令來新增領域項目。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**在 Azure Data Factory 中：**

* 使用 **Windows 驗證**以及您用來連線到 HDFS 資料來源的網域帳戶或 Kerberos 主體，來設定 HDFS 連接器。 檢查組態詳細資料上的 [HDFS 連結服務屬性](#linked-service-properties)區段。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="legacy-models"></a>舊版模型

>[!NOTE]
>基於回溯相容性，仍照現狀支援下列模型。 建議您往後使用以上各節所述的新模型，ADF 製作使用者介面已轉換為產生新模型。

### <a name="legacy-dataset-model"></a>舊版資料集模型

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**FileShare** |是 |
| folderPath | 資料夾的路徑。 支援萬用字元篩選，允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際檔案名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例：rootfolder/subfolder/，如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |是 |
| fileName |  在指定 "folderPath" 之下檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際資料夾名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意，當您想要從大量檔案中篩選檔案時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，這意謂著不會對資料集套用任何檔案屬性篩選條件。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間**依原樣複製檔案** (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 將格式下的 **type** 屬性設定為這些值其中之一。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 格式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs-legacy.md#compression-support)。<br/>支援的類型為：**GZip**、**Deflate**、**BZip2** 及 **ZipDeflate**。<br/>支援的層級為：**Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。

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
| type | 複製活動來源的類型屬性必須設定為：**HdfsSource** |是 |
| 遞迴 | 表示是否從子資料夾，或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當 recursive 設定為 true，而接收器為檔案型存放區時，系統不會在接收器複製/建立空資料夾/子資料夾。<br/>允許的值為：**true** (預設值)、**false** | 否 |
| distcpSettings | 使用 HDFS DistCp 時的屬性群組。 | 否 |
| resourceManagerEndpoint | Yarn Resource Manager 端點 | 若使用 DistCp 則為「是」 |
| tempScriptPath | 資料夾路徑，用來儲存暫存 DistCp 命令指令碼。 指令碼檔案是由 Data Factory 產生，並會在複製作業完成後移除。 | 若使用 DistCp 則為「是」 |
| distcpOptions | 對於 DistCp 命令提供的其他選項。 | 否 |
| maxConcurrentConnections | 可同時連線到儲存體存放區的連線數目。 只有在想要限制資料存放區的同時連線數目時，才需指定此屬性。 | 否 |

**範例：使用 DistCp 時複製活動中的 HDFS 來源**

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
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
