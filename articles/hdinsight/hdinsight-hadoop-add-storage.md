---
title: 將其他 Azure 存儲帳戶添加到 HDInsight
description: 瞭解如何將其他 Azure 存儲帳戶添加到現有 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206702"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>將其他儲存體帳戶新增至 HDInsight

瞭解如何使用腳本操作將其他 Azure 存儲*帳戶*添加到 HDInsight。 本文檔中的步驟將存儲*帳戶*添加到現有 HDInsight 群集。 本文適用于存儲*帳戶*（不是預設群集存儲帳戶），不適用於其他存儲，如[Azure 資料存儲庫第 1 代](hdinsight-hadoop-use-data-lake-store.md)和 Azure[資料存儲湖存儲 Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

> [!IMPORTANT]  
> 本文檔中的資訊是關於在創建群集後向群集添加其他存儲帳戶。 如需在叢集建立期間新增儲存體帳戶的資訊，請參閱[使用 Apache Hadoop、Apache Spark、Apache Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Hadoop 群集。 請參閱[在 Linux 上開始使用 HDInsight。](./hadoop/apache-hadoop-linux-tutorial-get-started.md)
* 存儲帳戶名稱和金鑰。 請參閱[管理存儲帳戶訪問金鑰](../storage/common/storage-account-keys-manage.md)。
* 如果使用 PowerShell，則需要 AZ 模組。  請參閱[Azure 電源外殼概述](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="how-it-works"></a>運作方式

在處理期間，此指令碼會執行下列動作：

* 如果儲存體帳戶已經存在於叢集的 core-site.xml 組態中，則指令碼會結束，不會執行任何進一步的動作。

* 確認儲存體帳戶存在並可使用金鑰來存取。

* 使用叢集認證加密金鑰。

* 將儲存體帳戶新增至 core-site.xml 檔案。

* 停止並重新啟動 [Apache Oozie](https://oozie.apache.org/)、[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)，以及 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 服務。 停止並啟動這些服務可讓它們使用新的儲存體帳戶。

> [!WARNING]  
> 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。

## <a name="add-storage-account"></a>新增儲存體帳戶

使用[腳本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)應用具有以下注意事項的更改：

|屬性 | 值 |
|---|---|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|節點類型|Head|
|參數|`ACCOUNTNAME``ACCOUNTKEY` `-p`|

* `ACCOUNTNAME`是要添加到 HDInsight 群集的存儲帳戶的名稱。
* `ACCOUNTKEY`是`ACCOUNTNAME`的便捷鍵。
* `-p` 是選擇性的。 如果指定，金鑰不會加密，並且以純文字形式存儲在核心 site.xml 檔中。

## <a name="verification"></a>驗證

在 Azure 門戶中查看 HDInsight 群集時，選擇 __"屬性__"下的 __"存儲帳戶__"條目不會顯示通過此腳本操作添加的存儲帳戶。 Azure PowerShell 和 Azure CLI 也不會顯示其他存儲帳戶。 不會顯示存儲資訊，因為腳本僅修改群集的`core-site.xml`配置。 使用 Azure 管理 API 檢索群集資訊時，不會使用此資訊。

要驗證附加存儲使用如下所示的方法之一：

### <a name="powershell"></a>PowerShell

該腳本將返回與給定群集關聯的存儲帳戶名稱。 替換為`CLUSTERNAME`實際的群集名稱，然後運行腳本。

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

1. 導航到**HDFS** > **配置** > **高級** > **自訂核心網站**。

1. 觀察以`fs.azure.account.key`開頭的鍵。 帳戶名稱將是金鑰的一部分，如以下示例圖像所示：

   ![通過阿帕奇·安巴里進行核查](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>移除儲存體帳戶

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

1. 導航到**HDFS** > **配置** > **高級** > **自訂核心網站**。

1. 刪除以下鍵：
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

卸下這些金鑰並保存配置後，您需要逐個重新開機 Oozie、紗線、MapReduce2、HDFS 和 Hive。

## <a name="known-issues"></a>已知問題

### <a name="storage-firewall"></a>存儲防火牆

如果您選擇使用 **"選定網路**上的**防火牆"和"虛擬網路**限制"來保護存儲帳戶，請確保啟用"**允許受信任的 Microsoft 服務"** 的異常，以便 HDInsight 可以訪問您的存儲帳戶。

### <a name="unable-to-access-storage-after-changing-key"></a>無法在變更金鑰之後存取儲存體

如果您變更儲存體帳戶的金鑰，HDInsight 就無法再存取儲存體帳戶。 HDInsight 在叢集的 core-site.xml 中使用金鑰的快取複本。 此快取副本必須更新以符合新的金鑰。

再次執行指令碼動作並__不會__更新金鑰，因為指令碼會查看儲存體帳戶的項目是否已經存在。 如果條目已存在，則不會進行任何更改。

要解決此問題，可以：  
1. 刪除存儲帳戶。
1. 添加存儲帳戶。

> [!IMPORTANT]  
> 不支援旋轉附加到群集的主存儲帳戶的存儲金鑰。

### <a name="poor-performance"></a>效能不佳

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會遇到效能不佳。 存取不同區域中的資料會傳送 Azure 資料中心外部和跨越公用網際網路的網路流量，這可能會造成延遲。

### <a name="additional-charges"></a>額外費用

如果儲存體帳戶位於與 HDInsight 叢集不同的區域中，您可能會發現 Azure 帳單上出現額外輸出費用。 當資料離開區域資料中心時，就會產生輸出費用。 即使流量的目的地是不同區域中的其他 Azure 資料中心，仍會產生此費用。

## <a name="next-steps"></a>後續步驟

您已經瞭解如何將其他存儲帳戶添加到現有的 HDInsight 群集。 如需指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
