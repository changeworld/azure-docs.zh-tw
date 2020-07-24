---
title: 使用 PowerShell 管理 Apache Hadoop 叢集-Azure HDInsight
description: 了解如何使用 Azure PowerShell 對 HDInsight 中的 Apache Hadoop 叢集執行管理工作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 1839cfe0f7295af583b7bd5397957a223e888d3e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086484"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 HDInsight 上的 Apache Hadoop 叢集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell 可讓您在 Azure 中用來控制和自動化工作負載的部署及管理。 在本文中，您將瞭解如何使用 Azure PowerShell Az 模組，在 Azure HDInsight 中管理[Apache Hadoop](https://hadoop.apache.org/)叢集。 如需 HDInsight PowerShell Cmdlet 的清單，請參閱[Az HDInsight reference](https://docs.microsoft.com/powershell/module/az.hdinsight)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

安裝 PowerShell [Az 模組](https://docs.microsoft.com/powershell/azure/)。

## <a name="create-clusters"></a>建立叢集

請參閱 [使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>列出叢集

使用下列命令列出目前訂用帳戶中的所有叢集：

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>顯示叢集

使用下列命令顯示目前訂用帳戶中特定叢集的詳細資料：

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>刪除叢集

使用下列命令來刪除叢集：

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

您也可以移除包含叢集的資源群組來刪除叢集。 刪除資源群組會刪除群組中的所有資源 (包括預設儲存體帳戶)。

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>調整叢集

叢集調整功能可讓您變更在 Azure HDInsight 中執行的叢集所用的背景工作節點數目，而不需要重新建立叢集。 若要使用 Azure PowerShell 變更 Hadoop 叢集大小，請從用戶端電腦執行下列命令：

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 如需調整叢集的詳細資訊，請參閱[調整 HDInsight 叢集規模](./hdinsight-scaling-best-practices.md)。

## <a name="update-http-user-credentials"></a>更新 HTTP 使用者認證

[AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential)會設定 Azure HDInsight 叢集的閘道 HTTP 認證。

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>尋找預設的儲存體帳戶

下列 PowerShell 指令碼示範如何取得預設儲存體帳戶名稱和相關資訊：

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>尋找資源群組

在 Resource Manager 模式中，每個 HDInsight 叢集皆屬於一個 Azure 資源群組。  尋找資源群組：

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>提交工作

**提交 MapReduce 作業**

請參閱[執行包含在 HDInsight 中的 MapReduce 範例](hadoop/apache-hadoop-run-samples-linux.md)。

**提交 Apache Hive 作業**

請參閱[使用 PowerShell 執行 Apache Hive 查詢](hadoop/apache-hadoop-use-hive-powershell.md)。

**提交 Apache Sqoop 作業**

請參閱[搭配 HDInsight 使用 Apache Sqoop](hadoop/hdinsight-use-sqoop.md)。

**提交 Apache Oozie 作業**

請參閱[在 HDInsight 中搭配 Apache Hadoop 使用 Apache Oozie 來定義並執行工作流程](hdinsight-use-oozie-linux-mac.md)。

## <a name="upload-data-to-azure-blob-storage"></a>將資料上傳至 Azure Blob 儲存體

請參閱[將資料上傳至 HDInsight](hdinsight-upload-data.md)。

## <a name="see-also"></a>另請參閱

* [Az HDInsight Cmdlet](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](hdinsight-administer-use-portal-linux.md)
* [使用命令列介面管理 HDInsight](hdinsight-administer-use-command-line.md)
* [建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [以程式設計方式提交 Apache Hadoop 作業](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
