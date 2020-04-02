---
title: 使用引導策略自訂 Azure HDInsight 叢集設定
description: 瞭解如何使用 .NET、PowerShell 和資源管理器範本以程式設計方式自定義 HDInsight 叢集配置。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529355"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>使用 Bootstrap 自訂 HDInsight 叢集

引導文稿允許您以程式設計方式在 Azure HDInsight 中安裝和配置元件。

建立 HDInsight 叢集時,有三種方法可以設定設定檔設定:

* 使用 Azure PowerShell
* 使用 .NET SDK
* 使用 Azure Resource Manager 範本

例如,使用這些程式設計方法,可以在以下檔中設定選項:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (kafka-broker 設定)

有關在建立期間在 HDInsight 叢集上安裝其他元件的資訊,請參閱[使用文稿操作 (Linux) 自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="prerequisites"></a>Prerequisites

* 如果使用 PowerShell,則需要[Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

下列 PowerShell 程式碼會自訂 [Apache Hive](https://hive.apache.org/) 組態：

> [!IMPORTANT]  
> 該參數`Spark2Defaults`可能需要與[Add-AzHD InsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)一起使用。 您可以將空值傳遞給參數,如下代碼示例所示。

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

可完整運作的 PowerShell 指令碼可在[附錄](#appendix-powershell-sample)中找到。

**若要確認變更：**

1. 導航到`https://CLUSTERNAME.azurehdinsight.net/`群`CLUSTERNAME`集 的名稱所在的位置。
1. 從左邊選單導覽到**Hive** > **Configs** > **進階**。
1. 延伸**進階蜂巢站台**。
1. 找到**hive.metastore.client.套接字.超時**並確認值為**90 秒**。

以下是更多自訂其他組態檔的範例：

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>使用 .NET SDK

請參考[.NET 的 Azure HDInsight SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)。

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本

Resource Manager 範本中，您可以使用啟動程序︰

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop 自訂叢集開機 Azure 資源管理員範本](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

範例資源管理員範本代碼段,用於在spark2預設值中切換配置,以便定期從存儲中清理事件日誌。  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>另請參閱

* [在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-provision-linux-clusters.md)提供如何使用其他自訂選項建立 HDInsight 叢集的指示。
* [為 HDInsight 開發文稿操作文稿](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 叢集上安裝和使用 Apache Spark](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [在 HDInsight 群集上安裝和使用 Apache Giraph。](hdinsight-hadoop-giraph-install.md)

## <a name="appendix-powershell-sample"></a>附錄：PowerShell 範例

此 PowerShell 文本創建 HDInsight 群集並自訂 Hive 設置。 請務必輸入`$nameToken`的值`$httpPassword``$sshPassword`。

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
