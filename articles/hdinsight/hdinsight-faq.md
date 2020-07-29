---
title: Azure HDInsight 常見問題
description: HDInsight 的相關常見問題
keywords: 常見問題，常見問題
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: c0efdda24ae47ae65f0d469b50feaefdf6350678
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022209"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight：常見問題集

本文提供有關如何執行[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)的一些常見問題的解答。

## <a name="creating-or-deleting-hdinsight-clusters"></a>建立或刪除 HDInsight 叢集

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>如何? 布建 HDInsight 叢集？

若要查看 HDInsight 叢集類型和布建方法，請參閱[使用 Apache Hadoop 在 HDInsight 中設定叢集、Apache Spark、Apache Kafka 等](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>如何? 刪除現有的 HDInsight 叢集？

若要深入瞭解如何刪除不再使用的叢集，請參閱[刪除 HDInsight](hdinsight-delete-cluster.md)叢集。

請嘗試在建立和刪除作業之間保留至少30到60分鐘的時間。 否則，作業可能會失敗，並出現下列錯誤訊息：

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>如何? 針對我的工作負載選取正確的核心或節點數目嗎？

適當的核心數目和其他設定選項取決於各種因素。

如需詳細資訊，請參閱[HDInsight 叢集的容量規劃](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 叢集中的各種節點類型為何？

請參閱[Azure HDInsight 叢集中的資源類型](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)。

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>建立大型 HDInsight 叢集的最佳作法有哪些？

1. 建議使用[自訂 AMBARI DB](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)來設定 HDInsight 叢集，以改善叢集擴充性。
2. 使用[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)建立 HDInsight 叢集，以利用 Azure Data Lake Storage Gen2 的更高頻寬和其他效能特性。
3. 前端節點應該夠大，以容納在這些節點上執行的多個主要服務。
4. 某些特定的工作負載（例如互動式查詢）也需要較大的 Zookeeper 節點。 請考慮至少8個核心 Vm。
5. 在 Hive 和 Spark 的情況下，請使用[外部 Hive 中繼存放區](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores)。

## <a name="individual-components"></a>個別元件

### <a name="can-i-install-additional-components-on-my-cluster"></a>我可以在叢集上安裝其他元件嗎？

是。 若要安裝其他元件或自訂叢集設定，請使用：

- 在建立期間或之後的腳本。 腳本是透過[腳本動作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)叫用。 [腳本動作] 是您可以從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用的設定選項。 此設定選項可以從 Azure 入口網站、HDInsight Windows PowerShell Cmdlet 或 HDInsight .NET SDK 使用。

- 安裝應用程式的[HDInsight 應用程式平臺](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)。

如需支援的元件清單，請參閱[HDInsight 有哪些 Apache Hadoop 元件和版本？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>我可以升級叢集上預先安裝的個別元件嗎？

如果您升級已預先安裝在叢集上的內建元件或應用程式，Microsoft 將不會支援產生的設定。 這些系統組態尚未經過 Microsoft 測試。 嘗試使用可能已預先安裝元件之升級版本的不同 HDInsight 叢集版本。

例如，不支援將 Hive 升級為個別元件。 HDInsight 是受控服務，而且許多服務都與 Ambari 伺服器整合並經過測試。 自行升級 Hive 會導致其他元件的索引二進位檔變更，而且會導致您叢集上的元件整合問題。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark 和 Kafka 可以在相同的 HDInsight 叢集上執行嗎？

不可以，您無法在相同的 HDInsight 叢集上執行 Apache Kafka 和 Apache Spark。 為 Kafka 和 Spark 建立個別的叢集，以避免發生資源爭用問題。

### <a name="how-do-i-change-timezone-in-ambari"></a>如何? 變更 Ambari 中的時區嗎？

1. 開啟位於的 Ambari Web UI `https://CLUSTERNAME.azurehdinsight.net` ，其中 CLUSTERNAME 是您的叢集名稱。
2. 在右上角，選取 [管理] |設置。 

   ![Ambari 設定](media/hdinsight-faq/ambari-settings.png)

3. 在 [使用者設定] 視窗中，從 [時區] 下拉式下選取新的時區，然後按一下 [儲存]。

   ![Ambari 使用者設定](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>如何從現有的中繼存放區遷移至 Azure SQL Database？ 

若要從 SQL Server 遷移至 Azure SQL Database，請參閱[教學課程：使用 DMS，將 SQL Server 遷移到 Azure SQL Database 離線中的單一資料庫或](../dms/tutorial-sql-server-to-azure-sql.md)集區資料庫。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>刪除叢集時，Hive 中繼存放區是否刪除？

這取決於您的叢集設定要使用的中繼存放區類型。

若為預設中繼存放區：預設中繼存放區是叢集生命週期的一部分。 當您刪除叢集時，相應的中繼存放區和中繼資料會一併刪除。

若為自訂中繼存放區：中繼存放區的生命週期不會系結至叢集的生命週期。 因此，您可以建立和刪除叢集，而不會遺失中繼資料。 即使在您刪除並重新建立 HDInsight 叢集之後，您的 Hive 架構之類的中繼資料仍會保存。

如需詳細資訊，請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](hdinsight-use-external-metadata-stores.md)。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>遷移 Hive 中繼存放區也會遷移 Ranger 資料庫的預設原則嗎？

否，原則定義位於 Ranger 資料庫中，因此，遷移 Ranger 資料庫將會遷移其原則。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>您是否可以將 Hive 中繼存放區從企業安全性套件（ESP）叢集遷移至非 ESP 叢集，還有另一種方法？

是，您可以將 Hive 中繼存放區從 ESP 遷移至非 ESP 叢集。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>如何預估 Hive 中繼存放區資料庫的大小？

Hive 中繼存放區用來儲存 Hive 伺服器所使用之資料來源的中繼資料。 大小需求的部分取決於 Hive 資料來源的數目和複雜度。 這些專案無法預先預估。 如[Hive 中繼存放區指導方針](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)所述，您可以從 S2 層開始。 此層提供 50 DTU 和 250 GB 的儲存體，如果您看到瓶頸，請相應增加資料庫。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>您是否支援以外部中繼存放區以外的其他任何資料庫 Azure SQL Database？

否，Microsoft 僅支援以外部自訂中繼存放區 Azure SQL Database。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>我可以在多個叢集之間共用中繼存放區嗎？

是，您可以在多個叢集之間共用自訂中繼存放區，只要這些叢集使用相同的 HDInsight 版本即可。

## <a name="connectivity-and-virtual-networks"></a>連線能力與虛擬網路  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>在我的網路上封鎖埠22和23有哪些含意？

如果您封鎖埠22和埠23，就不會有叢集的 SSH 存取權。 HDInsight 服務不會使用這些埠。

如需詳細資訊，請參閱下列文件：

- [控制網路流量](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [以私人端點保護虛擬網路中 HDInsight 叢集的連入流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 管理 IP 位址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>我可以在與 HDInsight 叢集相同的子網內部署額外的虛擬機器嗎？

是，您可以在與 HDInsight 叢集相同的子網內部署其他虛擬機器。 可能的設定如下：

- 邊緣節點：您可以將另一個邊緣節點新增至叢集，如在[HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](hdinsight-apps-use-edge-node.md)中所述。

- 獨立節點 .. 您可以將獨立虛擬機器新增到相同的子網，並使用私用端點從該虛擬機器存取叢集 `https://<CLUSTERNAME>-int.azurehdinsight.net` 。 如需詳細資訊，請參閱[控制網路流量](./control-network-traffic.md)。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>我應該將資料儲存在邊緣節點的本機磁片上嗎？

否，將資料儲存在本機磁片上並不是個好主意。 如果節點失敗，則儲存在本機的所有資料都將遺失。 建議您將資料儲存在 Azure Data Lake Storage Gen2 或 Azure Blob 儲存體中，或藉由裝載 Azure 檔案儲存體共用來儲存資料。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>我可以將現有的 HDInsight 叢集新增到另一個虛擬網路嗎？

不行，您不能。 在布建時，應該指定虛擬網路。 如果在布建期間未指定任何虛擬網路，部署會建立無法從外部存取的內部網路。 如需詳細資訊，請參閱[將 HDInsight 新增至現有的虛擬網路](hdinsight-plan-virtual-network-deployment.md#existingvnet)。

## <a name="security-and-certificates"></a>安全性與憑證

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Azure HDInsight 叢集上的惡意程式碼防護有哪些建議？

如需惡意程式碼防護的相關資訊，請參閱[Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>如何? 建立 HDInsight ESP 叢集的 keytab？

為您的網域使用者名稱建立 Kerberos keytab。 您稍後可以使用此 keytab 來驗證已加入網域的遠端叢集，而不需要輸入密碼。 功能變數名稱為大寫：

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>我可以使用現有的 Azure Active Directory 租使用者來建立具有 ESP 的 HDInsight 叢集嗎？

在您可以建立具有 ESP 的 HDInsight 叢集之前，請先啟用 Azure Active Directory Domain Services （Azure AD DS）。 開放原始碼 Hadoop 依賴 Kerberos 來進行驗證（而不是 OAuth）。

若要將 Vm 加入網域，您必須具有網域控制站。 Azure AD DS 是受管理的網域控制站，而且會被視為 Azure Active Directory 的延伸。 Azure AD DS 提供所有的 Kerberos 需求，以管理的方式建立安全的 Hadoop 叢集。 HDInsight 作為受控服務，可與 Azure AD DS 整合以提供安全性。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>我可以在 AAD DS 安全 LDAP 安裝程式中使用自我簽署憑證，並布建 ESP 叢集嗎？

建議使用由憑證授權單位單位所發行的憑證。 但是，ESP 也支援使用自我簽署憑證。 如需詳細資訊，請參閱：

- [啟用 Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [教學課程：為 Azure Active Directory Domain Services 受控網域設定安全 LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>如何提取 Ranger 中顯示的登入活動？

針對審核需求，Microsoft 建議啟用 Azure 監視器記錄，如[使用 Azure 監視器記錄來監視 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)叢集中所述。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>我可以在叢集上停用 `Clamscan` 嗎？

`Clamscan`是在 HDInsight 叢集上執行並由 Azure 安全性（azsecd）用來保護您的叢集免于遭受病毒攻擊的防毒軟體。 Microsoft 強烈建議使用者避免對預設設定進行任何變更 `Clamscan` 。

此程式不會干擾或採取任何迴圈，而不是其他進程。 它一律會產生其他進程。 `Clamscan`只有在系統閒置時，才看得到 CPU 尖峰。  

在您必須控制排程的案例中，您可以使用下列步驟：

1. 使用下列命令停用自動執行：
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. 新增以 root 身分執行下列命令的 Cron 作業：
   
   `/usr/local/bin/azsecd manual -s clamav`

如需有關如何設定及執行 cron 作業的詳細資訊，請參閱[如何? 設定 cron 作業](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)？

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>為什麼 Spark ESP 叢集上有 LLAP 可供使用？
LLAP 是基於安全性考慮（Apache Ranger）而啟用，而不是效能。 使用較大的節點 Vm 來配合 LLAP 的資源使用量（例如，最低 D13V2）。 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>如何在建立 ESP 叢集之後新增其他 AAD 群組？
有兩種方式可達成此目標： 1-您可以重新建立叢集，並在叢集建立時新增額外的群組。 如果您在 AAD-DS 中使用範圍同步處理，請確定群組 B 已包含在限域的同步處理中。
2-將群組新增為先前用來建立 ESP 叢集之群組的嵌套子群組。 例如，如果您已建立具有群組的 ESP 叢集 `A` ，您稍後可以在 [新增群組] `B` 做為的嵌套子群組，然後在 `A` 大約一小時後自動同步處理並在叢集中使用。 

## <a name="storage"></a>儲存體

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>我可以將 Azure Data Lake Storage Gen2 新增至現有的 HDInsight 叢集做為額外的儲存體帳戶嗎？

不可以，目前無法將 Azure Data Lake Storage Gen2 儲存體帳戶新增至具有 blob 儲存體作為其主要儲存體的叢集。 如需詳細資訊，請參閱[比較儲存體選項](hdinsight-hadoop-compare-storage-options.md)。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>如何尋找 Data Lake 儲存體帳戶目前已連結的服務主體？

您可以在 Azure 入口網站的叢集屬性底下的**Data Lake Storage Gen1 存取**中找到您的設定。 如需詳細資訊，請參閱[驗證叢集設定](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>如何計算 HDInsight 叢集的儲存體帳戶和 blob 容器的使用量？

執行下列其中一個動作：

- [使用 PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- 尋找/user/hive/. 的大小 */user/hive/.Trash/* HDInsight 叢集上的垃圾桶/資料夾，使用下列命令列：
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>如何設定 blob 儲存體帳戶的審核功能？

若要審核 blob 儲存體帳戶，請使用在[Azure 入口網站中監視儲存體帳戶中](../storage/common/storage-monitor-storage-account.md)的程式來設定監視。 HDFS-audit 記錄僅提供本機 HDFS 檔案系統的審核資訊（hdfs://mycluster）。  它不包含在遠端存放裝置上完成的作業。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>如何在 blob 容器和 HDInsight 前端節點之間傳輸檔案？

在前端節點上執行類似下列 shell 腳本的腳本：

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 檔案*filenames.txt*將會擁有 blob 容器中檔案的絕對路徑。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>是否有任何適用于儲存體的 Ranger 外掛程式？

目前，blob 儲存體和 Azure Data Lake Storage Gen1 或 Gen2 不存在任何 Ranger 外掛程式。 針對 ESP 叢集，您應該使用 Azure Data Lake Storage。 您至少可以使用 HDFS 工具，以手動方式在檔案系統層級設定細微許可權。 此外，使用 Azure Data Lake Storage 時，ESP 叢集會使用叢集層級的 Azure Active Directory 來執行一些檔案系統存取控制。 

您可以使用 Azure 儲存體總管，將資料存取原則指派給使用者的安全性群組。 如需詳細資訊，請參閱：

- [如何? 將 Azure AD 使用者的許可權設定為使用 Hive 或其他服務來查詢 Data Lake Storage Gen2 中的資料嗎？](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [搭配 Azure Data Lake Storage Gen2 使用 Azure 儲存體總管設定檔案和目錄等級使用權限](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>我可以在叢集上增加 HDFS 儲存體，而不需要增加背景工作節點的磁片大小嗎？

否。 您無法增加任何背景工作角色節點的磁片大小。 因此，增加磁片大小的唯一方法就是卸載叢集，然後使用較大的背景工作 Vm 重新建立叢集。 請不要使用 HDFS 來儲存任何 HDInsight 資料，因為如果刪除叢集，就會刪除資料。 相反地，請將您的資料儲存在 Azure 中。 相應增加叢集也可以將額外的容量新增至您的 HDInsight 叢集。

## <a name="edge-nodes"></a>邊緣節點

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>我可以在建立叢集之後新增邊緣節點嗎？

請參閱在[HDInsight 中的 Apache Hadoop 叢集上使用空白邊緣節點](hdinsight-apps-use-edge-node.md)。

### <a name="how-can-i-connect-to-an-edge-node"></a>如何連接到邊緣節點？

建立邊緣節點之後，您可以在埠22上使用 SSH 來連線到它。 您可以從叢集入口網站尋找邊緣節點的名稱。 名稱通常以 *-ed*結尾。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>為什麼持續性腳本不會在新建立的邊緣節點上自動執行？

您可以使用持續性腳本來自訂透過調整規模作業新增至叢集的新背景工作節點。 持續性腳本不適用於邊緣節點。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>從叢集提取 Tez 查詢檢視的 REST API 呼叫有哪些？

您可以使用下列 REST 端點，以 JSON 格式提取必要的資訊。 使用基本驗證標頭來提出要求。

- `Tez Query View`： *HTTPs： \/ / \<cluster name> . azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`： *HTTPs： \/ / \<cluster name> . azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>如何? 使用 Azure Active Directory 使用者從 HDI 叢集取出設定詳細資料嗎？

若要與您的 AAD 使用者協商適當的驗證權杖，請使用下列格式來完成閘道：

* HTTPs:// `<cluster dnsname>` . azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>如何? 使用 Ambari Restful API 來監視 YARN 效能嗎？

如果您在相同的虛擬網路或對等互連虛擬網路中呼叫捲曲命令，命令為：

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
如果您從虛擬網路外部或非對等互連的虛擬網路呼叫命令，命令格式為：

- 若為非 ESP 叢集：
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- 針對 ESP 叢集：
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> 捲曲會提示您輸入密碼。 您必須為叢集登入使用者名稱輸入有效的密碼。

## <a name="billing"></a>計費

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>部署 HDInsight 叢集需要多少成本？

如需計費相關價格和常見問題的詳細資訊，請參閱[Azure HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)頁面。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 計費何時開始 & 停止？

HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是以每分鐘按比例計算。

### <a name="how-do-i-cancel-my-subscription"></a>如何? 取消我的訂用帳戶嗎？

如需如何取消訂用帳戶的相關資訊，請參閱[取消您的 Azure 訂](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)用帳戶。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>對於隨用隨付訂用帳戶，當我取消訂閱之後，會發生什麼事？

如需取消後的訂用帳戶相關資訊，請參閱[取消訂閱後會發生什麼情況？](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>為什麼當我執行 HDInsight 3.6 叢集時，Hive 版本會在 Ambari UI 中顯示為1.2.1000，而不是2.1？

雖然 Ambari UI 中只會出現1.2，但 HDInsight 3.6 同時包含 Hive 1.2 和 Hive 2.1。

## <a name="other-faq"></a>其他常見問題

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>HDInsight 提供的即時串流處理功能有哪些？

如需串流處理整合功能的相關資訊，請參閱[在 Azure 中選擇串流處理技術](/azure/architecture/data-guide/technology-choices/stream-processing)。

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>當叢集閒置一段特定期間時，是否有方法可以動態地終止叢集的前端節點？

您無法使用 HDInsight 叢集執行此動作。 在這些情況下，您可以使用 Azure Data Factory。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight 提供哪些合規性供應專案？

如需相容性資訊，請參閱[Microsoft 信任中心](https://www.microsoft.com/trust-center)和[Microsoft Azure 合規性的總覽](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。
