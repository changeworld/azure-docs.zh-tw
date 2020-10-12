---
title: 使用 Azure 資料箱從內部內部部署 HDFS 存放區遷移至 Azure 儲存體
description: 使用資料箱裝置，將資料從內部部署 HDFS 存放區遷移至 Azure 儲存體 (blob 儲存體或 Data Lake Storage Gen2) 。
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: a50f85e76f16f1e5ba8823adb1ea1aa02157fcee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88032555"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>使用 Azure 資料箱從內部內部部署 HDFS 存放區遷移至 Azure 儲存體

您可以使用資料箱裝置，將資料從 Hadoop 叢集的內部部署 HDFS 存放區遷移至 Azure 儲存體 (blob 儲存體或 Data Lake Storage Gen2) 。 您可以選擇資料箱磁碟、80-TB 的資料箱或 770-TB Data Box Heavy。

本文可協助您完成下列工作：

> [!div class="checklist"]
> * 準備遷移您的資料。
> * 將您的資料複製到資料箱磁碟、資料箱或 Data Box Heavy 裝置。
> * 將裝置寄回給 Microsoft。
> * 將存取權限套用至 (Data Lake Storage Gen2 的檔案和目錄) 

## <a name="prerequisites"></a>必要條件

您需要這些專案才能完成遷移。

* Azure 儲存體帳戶。

* 包含來源資料的內部部署 Hadoop 叢集。

* [Azure 資料箱裝置](https://azure.microsoft.com/services/storage/databox/)。

  * [訂購資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) 或 [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 

  * 將您的 [資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 或 [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) 連接至內部部署網路。

如果您已經準備好了，讓我們開始吧。

## <a name="copy-your-data-to-a-data-box-device"></a>將您的資料複製到資料箱裝置

如果您的資料符合單一資料箱裝置，您會將資料複製到資料箱裝置。 

如果您的資料大小超過資料箱裝置的容量，請使用選擇性程式 [將資料分割到多個資料箱裝置](#appendix-split-data-across-multiple-data-box-devices) ，然後執行此步驟。 

若要將資料從內部部署 HDFS 存放區複製到資料箱裝置，您需要設定幾個專案，然後使用 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 工具。

請遵循下列步驟，透過 Blob/物件儲存體的 REST Api 將資料複製到您的資料箱裝置。 REST API 介面會讓裝置顯示為叢集的 HDFS 存放區。

1. 透過 REST 複製資料之前，請先找出安全性和連線基本型別，以連接到資料箱上的 REST 介面或 Data Box Heavy。 登入資料箱的本機 web UI，然後移至 **[連線並複製]** 頁面。 針對您裝置的 Azure 儲存體帳戶，在 [ **存取設定**] 下，找出並選取 [ **REST**]。

    ![[連線並複製] 頁面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在 [存取儲存體帳戶] 和 [上傳資料] 對話方塊中，複製 **Blob 服務端點** 和 **儲存體帳戶金鑰**。 從 blob 服務端點，省略 `https://` 和結尾的斜線。

    在此情況下，端點為： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` 。 您將使用的 URI 主機部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` 。 如需範例，請參閱如何透過 [Http 連接到 REST](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     ![[存取儲存體帳戶並上傳資料] 對話方塊](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 在每個節點上，將端點和資料箱或 Data Box Heavy 節點 IP 位址新增至 `/etc/hosts` 。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果您使用 DNS 的其他機制，您應該確定可以解析資料箱端點。

4. 將 shell 變數設定 `azjars` 為和 jar 檔案的 `hadoop-azure` 位置 `azure-storage` 。 您可以在 Hadoop 安裝目錄下找到這些檔案。

    若要判斷這些檔案是否存在，請使用下列命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` 。 將 `<hadoop_install_dir>` 預留位置取代為您已安裝 Hadoop 之目錄的路徑。 請務必使用完整路徑。

    範例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 建立您想要用於資料複製的儲存體容器。 您也應該將目的地目錄指定為此命令的一部分。 這可能是虛擬目的地目錄。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 將 `<blob_service_endpoint>` 預留位置取代為您 blob 服務端點的名稱。

    * 將預留位置取代為 `<account_key>` 您帳戶的存取金鑰。

    * `<container-name>`以您的容器名稱取代預留位置。

    * 將預留位置取代為 `<destination_directory>` 您想要將資料複製到其中的目錄名稱。

6. 執行清單命令，以確保您的容器和目錄已建立。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 將 `<blob_service_endpoint>` 預留位置取代為您 blob 服務端點的名稱。

   * 將預留位置取代為 `<account_key>` 您帳戶的存取金鑰。

   * `<container-name>`以您的容器名稱取代預留位置。

7. 將資料從 Hadoop HDFS 複製到資料箱 Blob 儲存體，然後複製到您稍早建立的容器。 如果找不到您要複製的目錄，此命令會自動建立它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 將 `<blob_service_endpoint>` 預留位置取代為您 blob 服務端點的名稱。

    * 將預留位置取代為 `<account_key>` 您帳戶的存取金鑰。

    * `<container-name>`以您的容器名稱取代預留位置。

    * 將預留位置取代為 `<exlusion_filelist_file>` 包含您的檔案排除清單的檔案名。

    * 將預留位置取代為 `<source_directory>` 包含您想要複製之資料的目錄名稱。

    * 將預留位置取代為 `<destination_directory>` 您想要將資料複製到其中的目錄名稱。

    `-libjars`選項可用來讓 `hadoop-azure*.jar` 和相依檔案 `azure-storage*.jar` 可供使用 `distcp` 。 某些叢集可能已經發生這種情況。

    下列範例顯示如何 `distcp` 使用命令來複製資料。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    若要改善複製速度：

    * 請嘗試變更對應程式數目數目。  (上述範例使用 `m` = 4 對應程式數目。 ) 

    * 請嘗試 `distcp` 以平行方式執行多個。

    * 請記住，大型檔案的執行效能優於小型檔案。

## <a name="ship-the-data-box-to-microsoft"></a>將資料箱寄送給 Microsoft

遵循下列步驟來準備資料箱裝置並寄送至 Microsoft。

1. 首先，  [寄送準備您的資料箱或 Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)。

2. 裝置準備完成後，請下載 BOM 檔案。 您稍後將會使用這些 BOM 或資訊清單檔案來確認已上傳至 Azure 的資料。

3. 關閉裝置並移除纜線。

4. 與 UPS 排定取貨時間。

    * 針對資料箱裝置，請參閱 [寄送資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

    * 針對 Data Box Heavy 裝置，請參閱 [寄送您的 Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. 當 Microsoft 收到您的裝置之後，它會連線到資料中心網路，並將資料上傳至您放置裝置訂單時所指定的儲存體帳戶。 針對您所有資料上傳至 Azure 的 BOM 檔案進行驗證。 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>將存取權限套用至 (Data Lake Storage Gen2 的檔案和目錄) 

您的 Azure 儲存體帳戶中已經有資料。 現在您會將存取權限套用至檔案和目錄。

> [!NOTE]
> 只有當您使用 Azure Data Lake Storage Gen2 作為資料存放區時，才需要執行此步驟。 如果您只使用沒有階層命名空間的 blob 儲存體帳戶作為資料存放區，則可以略過本節。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>為您的 Azure Data Lake Storage Gen2 帳戶建立服務主體

若要建立服務主體，請參閱 [如何：使用入口網站來建立可存取資源 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 在執行該文章的[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)一節中的步驟時，請確實將 [儲存體 Blob 資料參與者]**** 角色指派給服務主體。

* 當您執行文章的 [ [取得值以進行登入](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) ] 區段中的步驟時，請將應用程式識別碼和用戶端秘密值儲存到文字檔中。 您很快就會用到這些資料。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>使用許可權產生複製的檔案清單

從內部部署 Hadoop 叢集，執行此命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令會產生複製的檔案及其許可權的清單。

> [!NOTE]
> 視 HDFS 中的檔案數目而定，此命令可能需要很長的時間才能執行。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>產生身分識別清單，並將其對應至 Azure Active Directory (新增) 身分識別

1. 下載 `copy-acls.py` 腳本。 請參閱本文的 [下載協助程式腳本，並設定您的邊緣節點來執行這些腳本](#download-helper-scripts) 一節。

2. 執行此命令以產生唯一身分識別的清單。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此腳本會產生名為 `id_map.json` 的檔案，其中包含您需要對應至新增型身分識別的身分識別。

3. 在文字編輯器中開啟 `id_map.json` 檔案。

4. 針對出現在檔案中的每個 JSON 物件， `target` 使用適當的對應身分識別，將 AAD 使用者主體名稱的屬性（ (UPN) 或 ObjectId）更新為 (OID) 。 完成之後，請儲存檔案。 在下一個步驟中，您將需要此檔案。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>將許可權套用至複製的檔案並套用身分識別對應

執行此命令，將許可權套用至您複製到 Data Lake Storage Gen2 帳戶中的資料：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

* `<container-name>`以您的容器名稱取代預留位置。

* 以您 `<application-id>` 在 `<client-secret>` 建立服務主體時所收集的應用程式識別碼和用戶端密碼取代和預留位置。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附錄：跨多個資料箱裝置分割資料

將資料移到資料箱裝置之前，您必須先下載一些協助程式腳本，確定您的資料已組織成符合資料箱裝置，並排除任何不必要的檔案。

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下載協助程式腳本，並設定您的邊緣節點以執行它們

1. 從您的內部部署 Hadoop 叢集的邊緣或前端節點，執行此命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令會複製包含 helper 腳本的 GitHub 存放庫。

2. 請確定您的本機電腦上已安裝 [jq](https://stedolan.github.io/jq/) 套件。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安裝 [要求](https://2.python-requests.org/en/master/) python 套件。

   ```bash
   
   pip install requests
   ```

4. 設定必要腳本的 execute 許可權。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>確定您的資料已組織成符合資料箱裝置

如果您的資料大小超過單一資料箱裝置的大小，您可以將檔案分割成多個群組，以便儲存至多個資料箱裝置。

如果您的資料未超過單一資料箱裝置的大小，您可以繼續進行下一節。

1. 使用更高的許可權，依照 `generate-file-list` 上一節中的指導方針執行您下載的腳本。

   以下是命令參數的描述：

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

2. 將產生的檔案清單複製到 HDFS，使其可供 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 作業存取。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的檔案

您必須從 DisCp 作業中排除某些目錄。 例如，排除包含狀態資訊的目錄，讓叢集保持執行狀態。

在您打算起始 DistCp 作業的內部部署 Hadoop 叢集上，建立指定您要排除的目錄清單的檔案。

以下是範例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>後續步驟

瞭解 Data Lake Storage Gen2 如何與 HDInsight 叢集搭配運作。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
