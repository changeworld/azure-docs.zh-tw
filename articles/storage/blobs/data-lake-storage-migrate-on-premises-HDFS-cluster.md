---
title: 使用 Azure 資料框從上置 HDFS 存儲遷移到 Azure 存儲
description: 將資料從本地 HDFS 存儲遷移到 Azure 存儲
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301995"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>使用 Azure 資料框從上置 HDFS 存儲遷移到 Azure 存儲

通過使用資料盒設備，可以從 Hadoop 群集的本地 HDFS 存儲將資料移轉到 Azure 存儲（Blob 存儲或資料存儲第 2 代）。 您可以從 80 TB 資料盒或 770 TB 資料盒中選擇重。

本文可説明您完成以下任務：

> [!div class="checklist"]
> * 準備遷移資料。
> * 將資料複製到資料盒或資料盒重設備。
> * 將設備運回 Microsoft。
> * 對檔和目錄應用存取權限（僅限資料存儲第 2 代）

## <a name="prerequisites"></a>Prerequisites

您需要這些內容才能完成遷移。

* Azure 儲存體帳戶。

* 包含來源資料的本地 Hadoop 群集。

* [Azure 資料框設備](https://azure.microsoft.com/services/storage/databox/)。

  * [訂購您的資料框](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或[資料框重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 

  * 將[資料盒](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[資料盒連接到](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)本地網路。

如果你準備好了，我們開始吧。

## <a name="copy-your-data-to-a-data-box-device"></a>將資料複製到資料盒設備

如果資料適合單個資料盒設備，則將資料複製到資料盒設備。 

如果資料大小超過資料盒設備的容量，請使用[可選過程跨多個資料盒設備拆分資料](#appendix-split-data-across-multiple-data-box-devices)，然後執行此步驟。 

要將資料從本地 HDFS 存儲複製到資料盒設備，您需要設置一些內容，然後使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

按照以下步驟將資料通過 Blob/物件存儲的 REST API 複製到資料盒設備。 REST API 介面將使設備顯示為群集的 HDFS 存儲。

1. 在通過 REST 複製資料之前，請標識安全和連接基元以連接到資料盒或資料盒上的 REST 介面。 登錄到資料框的本地 Web UI，然後轉到 **"連接"和"複製**"頁。 針對設備的 Azure 存儲帳戶，在 **"訪問設置**"、"查找"下並選擇**REST**。

    !["連接和複製"頁面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在"訪問存儲帳戶和上載資料對話方塊中，複製**Blob 服務終結點**和**存儲帳戶金鑰**。 從 blob 服務終結點中，省略`https://`和尾隨斜杠。

    在這種情況下，終結點為： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 要使用的 URI 的主機部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有關示例，請參閱如何通過[HTTP 連接到 REST。](/azure/databox/data-box-deploy-copy-data-via-rest) 

     !["訪問存儲帳戶和上傳資料"對話方塊](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 在每個節點`/etc/hosts`上添加終結點和資料框或資料盒重節點 IP 位址。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果您使用的是 DNS 的其他機制，則應確保可以解析資料框終結點。

4. 將 shell`azjars`變數設置為`hadoop-azure`和`azure-storage`jar 檔的位置。 您可以在 Hadoop 安裝目錄下找到這些檔。

    要確定這些檔是否存在，請使用以下命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 將`<hadoop_install_dir>`預留位置替換為安裝 Hadoop 的目錄的路徑。 請務必使用完全限定的路徑。

    範例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 創建要用於資料複製的存儲容器。 還應指定目標目錄作為此命令的一部分。 此時，這可能是虛擬目標目錄。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 將`<blob_service_endpoint>`預留位置替換為 blob 服務終結點的名稱。

    * 將`<account_key>`預留位置替換為帳戶的訪問金鑰。

    * 將`<container-name>`預留位置替換為容器的名稱。

    * 將`<destination_directory>`預留位置替換為要將資料複製到的目錄的名稱。

6. 運行清單命令以確保已創建容器和目錄。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 將`<blob_service_endpoint>`預留位置替換為 blob 服務終結點的名稱。

   * 將`<account_key>`預留位置替換為帳戶的訪問金鑰。

   * 將`<container-name>`預留位置替換為容器的名稱。

7. 將資料從 Hadoop HDFS 複製到資料盒 Blob 存儲中，複製到之前創建的容器中。 如果未找到要複製到的目錄，則命令會自動創建它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 將`<blob_service_endpoint>`預留位置替換為 blob 服務終結點的名稱。

    * 將`<account_key>`預留位置替換為帳戶的訪問金鑰。

    * 將`<container-name>`預留位置替換為容器的名稱。

    * 將`<exlusion_filelist_file>`預留位置替換為包含檔排除清單的檔的名稱。

    * 將`<source_directory>`預留位置替換為包含要複製資料的目錄的名稱。

    * 將`<destination_directory>`預留位置替換為要將資料複製到的目錄的名稱。

    該`-libjars`選項用於使`hadoop-azure*.jar`和 從屬`azure-storage*.jar`檔可供`distcp`使用。 某些群集可能已發生這種情況。

    下面的示例演示如何使用命令`distcp`來複製資料。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    要提高複製速度，

    * 嘗試更改映射器的數量。 （上述示例使用`m`= 4 個映射器。

    * 嘗試並行運行`distcp`多個。

    * 請記住，大型檔的性能優於小檔。

## <a name="ship-the-data-box-to-microsoft"></a>將資料框運送到微軟

按照以下步驟準備資料框設備並將其運送到 Microsoft。

1. 首先，[準備在資料箱或資料盒中發貨。](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)

2. 設備準備完成後，下載 BOM 檔。 稍後將使用這些物料清單或清單檔來驗證上載到 Azure 的資料。

3. 關閉設備並卸下電纜。

4. 與 UPS 排定取貨時間。

    * 有關資料框設備，請參閱["運送資料框](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)"。

    * 有關資料盒重設備，請參閱[將資料框裝出重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. Microsoft 收到您的設備後，它將連接到資料中心網路，並將資料上載到您下設備訂單時指定的存儲帳戶。 根據 BOM 檔驗證所有資料是否上載到 Azure。 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>對檔和目錄應用存取權限（僅限資料存儲第 2 代）

您已經將資料放入 Azure 存儲帳戶中。 現在，您將對檔和目錄應用存取權限。

> [!NOTE]
> 僅當使用 Azure 資料湖存儲 Gen2 作為資料存儲時，才需要此步驟。 如果只使用沒有階層命名空間的 Blob 存儲帳戶作為資料存儲，則可以跳過此部分。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>為 Azure 資料湖存儲 Gen2 帳戶創建服務主體

要創建服務主體，請參閱[：使用門戶創建可以訪問資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 在執行該文章的[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)一節中的步驟時，請確實將 [儲存體 Blob 資料參與者]**** 角色指派給服務主體。

* 在"[獲取值"中的步驟進行文章的登錄時](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，將應用程式 ID 和用戶端機密值保存到文字檔中。 您很快就會用到這些資料。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>生成具有其許可權的複製檔的清單

從本地 Hadoop 群集運行此命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令生成具有其許可權的複製檔的清單。

> [!NOTE]
> 根據 HDFS 中的檔數，此命令可能需要很長時間才能運行。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>生成識別欄位表並將其映射到 Azure 活動目錄 （ADD） 標識

1. 下載`copy-acls.py`腳本。 請參閱[下載協助程式腳本並設置邊緣節點以運行](#download-helper-scripts)本文部分。

2. 運行此命令以生成唯一標識的清單。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此腳本生成一個名為`id_map.json`的檔，該檔包含需要映射到基於 ADD 的標識的標識。

3. 在文字編輯器中開啟 `id_map.json` 檔案。

4. 對於檔中顯示的每個 JSON 物件，使用適當的映射`target`標識更新 AAD 使用者主體名稱 （UPN） 或 ObjectId （OID） 的屬性。 完成後，保存該檔。 在下一步中，您將需要此檔。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>應用複製檔的許可權並應用標識映射

運行此命令以將許可權應用於複製到資料存儲庫 Gen2 帳戶的資料：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

* 將`<container-name>`預留位置替換為容器的名稱。

* 將`<application-id>`和`<client-secret>`預留位置替換為創建服務主體時收集的應用程式 ID 和用戶端金鑰。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附錄：跨多個資料框設備拆分資料

在將資料移動到資料盒設備之前，您需要下載一些協助程式腳本，確保將資料組織起來以適合資料盒設備，並排除任何不必要的檔。

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下載協助程式腳本並設置邊緣節點以運行它們

1. 從本地 Hadoop 群集的邊緣或頭節點運行此命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令克隆包含説明器腳本的 GitHub 存儲庫。

2. 確保本地電腦上安裝了[jq](https://stedolan.github.io/jq/)包。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安裝[請求](https://2.python-requests.org/en/master/)python 包。

   ```bash
   
   pip install requests
   ```

4. 設置對所需腳本執行許可權的許可權。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>確保將資料組織起來以適合資料盒設備

如果資料的大小超過單個資料盒設備的大小，則可以將檔拆分為組，以便存儲到多個資料盒設備上。

如果資料不超過單個資料盒設備的大小，則可以繼續下一節。

1. 使用較高的權限，請按照`generate-file-list`上一節中的指導運行下載的腳本。

   下面是命令參數的說明：

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 將生成的檔案清單複製到 HDFS，以便[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)作業可以訪問它們。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的檔

您需要從 DisCp 作業中排除某些目錄。 例如，排除包含使群集保持運行的狀態資訊的目錄。

在計畫啟動 DistCp 作業的本地 Hadoop 群集上，創建一個檔，指定要排除的目錄清單。

以下是範例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>後續步驟

瞭解資料存儲第 2 代如何與 HDInsight 群集配合使用。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
