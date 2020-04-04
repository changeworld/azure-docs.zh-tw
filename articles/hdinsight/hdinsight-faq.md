---
title: Azure HDInsight 常見問題
description: 關於 HDInsight 的常見問題
keywords: 常見問題解答
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652224"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight:常見問題

本文提供了有關如何運行[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)的一些最常見問題的解答。

## <a name="creating-or-deleting-hdinsight-clusters"></a>建立或刪除 HDInsight 叢集

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>如何預配 HDInsight 群集?

要檢視可用的 HDInsight 叢集的類型和預配方法,請參閱使用 Apache [Hadoop、Apache Spark、Apache Kafka 等在 HDInsight 中設定群組 。](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>如何刪除現有的 HDInsight 群集?

要瞭解有關在群集不再使用時刪除群集的更多內容,請參閱[刪除 HDInsight 叢集](hdinsight-delete-cluster.md)。

在創建和刪除操作之間至少留出 30 到 60 分鐘。 否則,操作可能會失敗,並出現以下錯誤訊息:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>如何為工作負載選擇正確數量的內核或節點?

適當數量的內核和其他配置選項取決於各種因素。

有關詳細資訊,請參閱[HDInsight 叢集的容量規劃](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)。

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>當叢集佈建因為容量問題而失敗時該怎麼辦？

本節提供了常見的容量問題錯誤和緩解技術。

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>錯誤:部署將超過「800」的配額

針對每個資源群組，Azure 的部署配額限制為 800 個。 每個資源組、訂閱、帳戶或其他作用域應用不同的配額。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 如果嘗試部署內核數超過允許數量的虛擬機,則會收到一條錯誤消息,指出已超出配額。

要解決此問題,請使用 Azure 門戶、CLI 或 PowerShell 刪除不再需要的部署。

如需詳細資訊，請參閱[解決資源配額的錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>錯誤:最大節點超過此區域中的可用核心

您的訂用帳戶可能設定為要限制區域的核心數目。 如果嘗試部署具有比允許數量更多的內核的資源,則會收到一條錯誤消息,指出超出配額。

若要要求增加配額，請遵循下列步驟：

1. 到[Azure 門戶](https://portal.azure.com),然後選擇 **「幫助 + 支援**」。。
   
1. 選取 [新增支援要求]****。
   
1. 在 **'新增支援請求**"頁的 **'基礎知識'** 選項卡上,提供以下資訊:
   
   - **問題型態:** 選擇**服務和訂閱限制(配額)。**
   - **訂閱:** 選擇要修改的訂閱。
   - **配額型態:** 選擇**HDInsight**。

如需詳細資訊，請參閱[建立支援票證來增加核心](hdinsight-capacity-planning.md#quotas)。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 群集中的各種類型的節點是什麼?

Azure HDInsight 群集具有不同類型的虛擬機或節點。 每個節點類型在系統操作中起著一定的作用。

有關詳細資訊,請參閱[Azure HDInsight 群組集中的資源類型](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)。

## <a name="individual-components"></a>個別元件

### <a name="can-i-install-additional-components-on-my-cluster"></a>我可以在我的群集上安裝其他元件嗎?

是。 要安裝其他元件或自訂群組設定,請使用:

- 創建期間或之後的腳本。 文本通過[腳本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)調用 ,這是一個配置選項,可以從 Azure 門戶、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 中使用。 此配置選項可以從 Azure 門戶、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 中使用。

- [HDInsight 應用平臺](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)可安裝生態系統應用程式。

有關支援的元件清單,請參閱[HDInsight 提供的 Apache Hadoop 元件和版本是什麼?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>是否可以升級群集上預安裝的各個元件?

如果升級群集上預安裝的內置元件或應用程式,Microsoft 將不支援生成的配置。 這些系統配置尚未經過 Microsoft 測試。 嘗試使用已預安裝元件升級版本的 HDInsight 叢集的不同版本。

例如,不支援將 Hive 升級為單個元件。 HDInsight 是一個託管服務,許多服務與 Ambari 伺服器集成並進行測試。 自行升級 Hive 會導致其他元件的索引二進位檔發生變化,並會導致群集上的元件集成問題。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark 和 Kafka 可以在同一 HDInsight 群集上運行嗎?

不,不可能在同一個HDInsight群集上運行阿帕奇卡夫卡和阿帕奇火花。 為 Kafka 和 Spark 創建單獨的群集,以避免資源爭用問題。

### <a name="how-do-i-change-timezone-in-ambari"></a>如何更改安巴里的時區?

1. 在打開 Ambari `https://CLUSTERNAME.azurehdinsight.net`Web UI,其中 CLUSTERNAME 是群集的名稱。
2. 在右上角,選擇管理員 |設置。 

   ![安巴里設定](media/hdinsight-faq/ambari-settings.png)

3. 在「用戶設置」視窗中,從時區下拉清單中選擇新時區,然後單擊"保存"。

   ![安巴里使用者設定](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>如何從現有元存儲遷移到 Azure SQL 伺服器? 

要從 SQL 伺服器移轉到 Azure SQL 伺服器,請參考[教學: 使用 DMS 將 SQL 伺服器移至 Azure SQL 資料庫中的單個資料庫或池資料庫](../dms/tutorial-sql-server-to-azure-sql.md)。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>刪除群集時是否刪除了 Hive 中儲存?

這取決於群集配置為使用的元存儲類型。

對於預設元存儲:預設元存儲是群集生命週期的一部分。 當您刪除叢集時，相應的中繼存放區和中繼資料會一併刪除。

對於自定義元存儲:元存儲的生命週期不與群集的生命周期相關聯。 因此,您可以創建和刪除群集,而不會丟失元資料。 即使在刪除並重新創建 HDInsight 群集後,Hive 架構等中數據也會保留。

如需詳細資訊，請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](hdinsight-use-external-metadata-stores.md)。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>遷移 Hive 中儲存是否也會遷移 Ranger 資料庫的預設政策?

否,策略定義位於 Ranger 資料庫中,因此遷移遊俠資料庫將遷移其策略。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>是否可以將 Hive 中儲存從企業安全包 (ESP) 群集遷移到非 ESP 群集,反之亦然?

可以,您可以將 Hive 元存儲從 ESP 遷移到非 ESP 叢集。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>如何估計 Hive 儲存資料庫的大小?

Hive 儲存儲存 Hive 伺服器使用的數據來源的中繼資料。大小要求部分取決於 Hive 數據源的數量和複雜性,無法預先估計。 如[Hive 元存儲指南](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)中所述,可以從 S2 層開始,該層提供 50 個 DTU 和 250 GB 的存儲空間,如果看到瓶頸,則可以擴展資料庫。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>您是否支援 Azure SQL 資料庫以外的任何其他資料庫作為外部元儲存?

否,Microsoft 僅支援 Azure SQL 資料庫作為外部自定義元存儲。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>我可以跨多個群集共用元存儲嗎?

可以,您可以在多個群集之間共用自定義元存儲,只要它們使用相同的 HDInsight 版本。

## <a name="connectivity-and-virtual-networks"></a>連線並虛擬網路  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>阻止埠 22 和 23 在我的網路上有什麼影響?

如果阻止埠 22 和埠 23,則 SSH 將無法訪問群集。 HDInsight 服務不使用這些埠。

如需詳細資訊，請參閱下列文件：

- [控制網路流量](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [使用專用終結點在虛擬網路中保護對 HDInsight 叢集的傳入流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>是否可以將其他虛擬機器部署在同一子網路中,與 HDInsight 群集相同?

可以,您可以將其他虛擬機器部署在同一子網路中,與 HDInsight 群集相同。 可以進行以下設定:

- 邊緣節點:您可以將另一個邊緣節點添加到群集中,如[HDInsight 中 Apache Hadoop 群集上的空邊節點。](hdinsight-apps-use-edge-node.md)

- 獨立節點:您可以將獨立虛擬機添加到同一子網,並使用專用終結點`https://<CLUSTERNAME>-int.azurehdinsight.net`從該虛擬機訪問群集。 有關詳細資訊,請參閱[控制網路流量](hdinsight-plan-virtual-network-deployment.md#networktraffic)。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>是否應將資料存儲在邊緣節點的本地磁碟上?

不可以,將數據存儲在本地磁碟上不是個好主意。 如果節點發生故障,存儲在本地的所有數據都將丟失。 我們建議將資料存儲在 Azure 資料湖儲存 Gen2 或 Azure Blob 儲存中,或者透過安裝用於儲存資料的 Azure 檔案共用。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>我可以將現有的 HDInsight 群集添加到另一個虛擬網路嗎?

不,你不能。 應在預配時指定虛擬網路。 如果在預配期間未指定虛擬網路,則部署將創建可從外部無法訪問的內部網路。 有關詳細資訊,請參閱將[HDInsight 新增到現有虛擬網路](hdinsight-plan-virtual-network-deployment.md#existingvnet)。

## <a name="security-and-certificates"></a>安全性與憑證

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Azure HDInsight 群集上的惡意軟體保護建議是什麼?

有關惡意軟體保護的資訊,請參閱[適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意軟體](../security/fundamentals/antimalware.md)。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>如何為 HDInsight ESP 群集建立金鑰選項卡?

為您的網域使用者名創建Kerberos鍵選項卡。 以後,您可以使用此鍵選項卡對遠端域加入的群集進行身份驗證,而無需輸入密碼。 網域名為大寫:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>我能否使用現有的 Azure 活動目錄租戶創建具有 ESP 的 HDInsight 群集?

必須先啟用 Azure 活動目錄域服務 (Azure AD DS),然後才能使用 ESP 創建 HDInsight 群集。 開源 Hadoop 依賴於 Kerberos 進行身份驗證(與 OAuth 相反)。

要將 VM 加入域,必須具有域控制器。 Azure AD DS 是託管域控制器,被視為 Azure 活動目錄的擴展,它提供了以託管方式建構安全 Hadoop 群集的所有 Kerberos 要求。 HDInsight 作為託管服務與 Azure AD DS 整合,可提供端到端安全性。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>是否可以在 AAD-DS 安全 LDAP 設定中使用自簽名證書並預配 ESP 群集?

建議使用證書頒發機構頒發的證書,但 ESP 上也支援使用自簽名證書。 如需詳細資訊，請參閱

- [啟用 Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [教學:為 Azure 活動目錄網域服務託管域設定安全 LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>如何拉取 Ranger 中顯示的登入活動?

對於審核要求,Microsoft 建議啟用 Azure 監視器日誌,如[使用 Azure 監視器日誌來監視 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>我可以在我的群集上禁用 Clamscan 嗎?

Clamscan 是在 HDInsight 群集上運行的防病毒軟體,Azure 安全 (azsecd) 用於保護群集免受病毒攻擊。 Microsoft 強烈建議使用者不要對預設的 Clamscan 配置進行任何更改。

此過程不會干擾或佔用其他進程的任何迴圈。 它總是屈服於其他過程。 僅當系統空閒時,才應看到來自 Clamscan 的 CPU 峰值。  

在必須控制計畫的情況下,可以使用以下步驟:

1. 使用以下指令關閉自動執行:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. 新增將以下指令作為根執行的 Cron 作業:
   
   `/usr/local/bin/azsecd manual -s clamav`

有關如何設定和執行 cron 工作的詳細資訊,請參閱[如何設定 Cron 作業](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>為什麼在 Spark ESP 群集上可以使用 LLAP?
在 ESP Spark 群集上,由於安全原因(即 Apache Ranger),而不是性能,啟用 LLAP。 應使用較大的節點 VM 來適應 LLAP 的資源使用方式(例如最小 D13V2)。 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>創建 ESP 群集後,如何添加其他 AAD 組?
實現此目的的方法有兩種:1- 您可以在群集創建時重新創建群集並添加其他組。 如果您在 AAD-DS 中使用作用域同步,請確保組 B 包含在作用域同步中。
2- 將群組添加為用於創建 ESP 群集的前一組的嵌套子組。 例如,如果已創建具有組的`A`ESP 群集,則可以稍後將組添加為`B`嵌套子`A`組, 大約一小時後,該組將自動同步並在群集中可用。 

## <a name="storage"></a>存放裝置

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>是否可以將 Azure 資料儲存庫第 2 代作為其他儲存帳戶添加到現有 HDInsight 叢集?

否,目前無法將 Azure 資料儲存湖儲存 Gen2 儲存帳戶添加到以 Blob 儲存為主要存儲的群集。 關於詳細資訊,請參閱[比較儲存選項](hdinsight-hadoop-compare-storage-options.md)。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>如何查找數據湖存儲帳戶的當前連結的服務主體?

您可以在 Azure 門戶中的群集屬性下在 **「資料存儲庫第 1 代」訪問**中找到設定。 有關詳細資訊,請參閱[驗證叢集設定](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>如何計算 HDInsight 群集的儲存帳戶和 Blob 容器的使用方式?

執行下列其中一個動作：

- [使用 PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- 尋找 */user/hive 的大小。* 使用以下指令列在 HDInsight 叢集上回收/資料夾:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>如何為 Blob 存儲帳戶設置審核?

要審核 Blob 儲存帳戶,請使用 Azure[門戶中的監視存儲帳戶](../storage/common/storage-monitor-storage-account.md)中的過程配置監視。 HDFS 審核日誌僅提供本地 HDFS 檔案系統的審核資訊(hdfsTTPs://mycluster)。  它不包括在遠端存儲上執行的操作。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>如何在 Blob 容器和 HDInsight 頭節點之間傳輸檔?

在頭節點上執行類似於以下 shell 文稿的文稿:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> *檔名.txt*將具有 blob 容器中檔案的絕對路徑。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>是否有用於存儲的 Ranger 外掛程式?

目前,沒有用於 Blob 儲存和 Azure 資料儲存第 1 代或第 2 代的 Ranger 外掛程式。 對於 ESP 群集,應使用 Azure 資料湖儲存,因為您至少可以使用 HDFS 工具在檔案系統等級手動設置細粒度許可權。 此外,在使用 Azure 資料湖儲存時,ESP 群集將在群集級別使用 Azure 活動目錄執行某些文件系統存取控制。 

可以使用 Azure 儲存資源管理員將數據存取策略分配給使用者的安全組。 如需詳細資訊，請參閱

- [如何設置 Azure AD 使用者使用 Hive 或其他服務在資料儲存第 2 代中查詢數據的許可權?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [搭配 Azure Data Lake Storage Gen2 使用 Azure 儲存體總管設定檔案和目錄等級使用權限](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>是否可以在不增加輔助節點的磁碟大小的情況下增加群集上的 HDFS 儲存?

不可以,不能增加任何輔助節點的磁碟大小,因此增加磁碟大小的唯一方法是刪除群集,然後使用較大的輔助 VM 重新創建群集。 不要使用 HDFS 儲存任何 HDInsight 數據,因為如果刪除群集,數據將被刪除。 而是將數據存儲在 Azure 中。 向上擴展群集還可以向 HDInsight 群集添加其他容量。

## <a name="edge-nodes"></a>邊緣節點

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>是否可以在創建群集后添加邊緣節點?

HDInsight 群集,或創建群集時的新群集。 如需詳細資訊，請參閱[在 HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](hdinsight-apps-use-edge-node.md)。

### <a name="how-can-i-connect-to-an-edge-node"></a>如何連接到邊緣節點?

創建邊緣節點後,可以使用埠 22 上的 SSH 連接到它。 可以從群集門戶中找到邊緣節點的名稱。 名稱通常以 *- ed*結尾。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>為什麼持久化腳本在新創建的邊緣節點上未自動運行?

使用持久化腳本通過縮放操作自定義添加到群集的新工作節點。 持久化腳本不適用於邊緣節點。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>REST API 呼叫什麼才能從群集中提取 Tez 查詢檢視?

您可以使用以下 REST 終結點以 JSON 格式提取必要的資訊。 使用基本身份驗證標頭髮出請求。

- Tez 查詢檢視*\//\<:HTTT:Ht:H.> azurehdinsight.net/ws/v1/時間線/HIVE_QUERY_ID/*
- Tez Dag 檢視*\//\<:HTT:H TEZ_DAG_ID...18*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>如何使用 Azure 活動目錄使用者從 HDI 群集檢索配置詳細資訊?

要與 AAD 使用者協商正確的身份驗證權杖,請使用以下格式透過閘道:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>如何使用 Ambari RestAPI 來監視 YARN 性能?

如果在同一虛擬網路或對等虛擬網路中呼叫 Curl 命令,則該指令是:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
如果從虛擬網路外部或非對等虛擬網路呼叫此指令,則指令格式為:

- 對於非 ESP 叢集:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- 對於 ESP 叢集:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl 會提示您輸入密碼。 您必須為群集登錄使用者名輸入有效密碼。

## <a name="billing"></a>計費

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>部署 HDInsight 群集需要多少成本?

有關與計費相關的定價和常見問題解答的詳細資訊,請參閱[Azure HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)頁面。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 計費何時開始&停止?

HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是每分鐘按比例計費的。

### <a name="how-do-i-cancel-my-subscription"></a>如何取消訂閱?

有關如何取消訂閱的資訊,請參閱取消 Azure[訂閱](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>對於即用即付訂閱,取消訂閱後會發生什麼情況?

有關取消訂閱後的資訊,請參閱[取消訂閱後會發生什麼情況?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>為什麼 Hive 版本在 Ambari UI 中顯示為 1.2.1000 而不是 2.1,即使我運行 HDInsight 3.6 群集也是如此?

雖然 Ambari UI 中只顯示 1.2,但 HDInsight 3.6 包含 Hive 1.2 和 Hive 2.1。

## <a name="other-faq"></a>其他常見問題解答

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight 在即時流處理功能方面提供什麼?

有關 Azure HDInsight 中流處理整合的資訊,請參閱[在 Azure 中選擇流處理技術](/azure/architecture/data-guide/technology-choices/stream-processing)。

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>當群集處於特定時間段空閒時,是否有一種方法來動態終止群集的頭節點?

不能使用 HDInsight 群集執行此操作。 對於這些方案,可以使用 Azure 數據工廠。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight 提供哪些合規性產品?

有關合規性資訊,請參閱[Microsoft 信任中心和](https://www.microsoft.com/trust-center) [Microsoft Azure 合規性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。
