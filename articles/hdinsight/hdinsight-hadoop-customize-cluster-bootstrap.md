---
title: 使用啟動程式自訂 Azure HDInsight 叢集設定
description: 瞭解如何使用 .NET、PowerShell 和 Resource Manager 範本，以程式設計方式自訂 HDInsight 叢集設定。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: eaddae04c198742007947831046139d80828534b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006580"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>使用 Bootstrap 自訂 HDInsight 叢集

啟動程式腳本可讓您以程式設計方式在 Azure HDInsight 中安裝及設定元件。

當您建立 HDInsight 叢集時，有三種方法可以設定設定檔設定：

* 使用 Azure PowerShell
* 使用 .NET SDK
* 使用 Azure Resource Manager 範本

例如，您可以使用這些程式設計方法來設定這些檔案中的選項：

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

如需在建立期間于 HDInsight 叢集上安裝其他元件的相關資訊，請參閱 [使用腳本動作自訂 hdinsight 叢集 (Linux) ](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="prerequisites"></a>Prerequisites

* 如果使用 PowerShell，您將需要 [Az 模組](https://docs.microsoft.com/powershell/azure/) \(部分機器翻譯\)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

下列 PowerShell 程式碼會自訂 [Apache Hive](https://hive.apache.org/) 組態：

> [!IMPORTANT]  
> 參數 `Spark2Defaults` 可能需要與 [AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue)搭配使用。 您可以將空值傳遞給參數，如下列程式碼範例所示。

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

1. 導覽至 `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` 您叢集的名稱。
1. 從左側功能表，流覽至**Hive 配置**  >  **Configs**  >  **Advanced**。
1. 展開 [ **Advanced hive-site**]。
1. 找出 **中繼存放區** ，並確認此值為 **90 年代**。

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

請參閱 [AZURE HDINSIGHT SDK for .net](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)。

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

![Hadoop 自訂叢集啟動程式 Azure Resource Manager 範本](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

在 spark2 中切換設定的範例 Resource Manager 範本程式碼片段-預設為定期清除儲存體中的事件記錄檔。  

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
* [開發 HDInsight 的腳本動作腳本](hdinsight-hadoop-script-actions-linux.md)
* [在 HDInsight 叢集上安裝和使用 Apache Spark](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [在 HDInsight 叢集上安裝和使用 Apache Giraph](hdinsight-hadoop-giraph-install.md)。

## <a name="appendix-powershell-sample"></a>附錄：PowerShell 範例

此 PowerShell 腳本會建立 HDInsight 叢集，並自訂 Hive 設定。 請務必輸入 `$nameToken` 、和的值 `$httpPassword` `$sshPassword` 。

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
