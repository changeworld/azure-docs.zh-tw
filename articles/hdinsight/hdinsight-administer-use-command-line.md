---
title: 使用 Azure CLI 管理 Azure HDInsight 叢集
description: 瞭解如何使用 Azure CLI 來管理 Azure HDInsight 叢集。 叢集類型包括 Apache Hadoop、Spark、HBase、風暴、Kafka、Interactive Query 和 ML 服務。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: d47c741bdf0d056a75a86cdb8aa14333320bb52a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742141"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>使用 Azure CLI 管理 Azure HDInsight 叢集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

瞭解如何使用 [Azure CLI](/cli/azure/) 來管理 Azure HDInsight 叢集。 Azure 命令列介面 (CLI) 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

* Azure CLI。 如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli) 以取得相關步驟。

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

## <a name="connect-to-azure"></a>連線到 Azure

登入 Azure 訂用帳戶。 如果您打算使用 Azure Cloud Shell，則選取程式碼區塊右上角的 [試試看]  。 或者，請輸入以下命令：

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>列出叢集

使用 [az hdinsight list](/cli/azure/hdinsight#az-hdinsight-list) 列出叢集。 以您的資源組名取代來編輯下列命令 `RESOURCE_GROUP_NAME` ，然後輸入命令：

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>顯示叢集

使用 [az hdinsight show](/cli/azure/hdinsight#az-hdinsight-show) 來顯示指定叢集的資訊。 藉由取代和相關資訊來編輯下列命令 `RESOURCE_GROUP_NAME` `CLUSTER_NAME` ，然後輸入命令：

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>刪除叢集

使用 [az hdinsight delete](/cli/azure/hdinsight#az-hdinsight-delete) 來刪除指定的叢集。 藉由取代和相關資訊來編輯下列命令 `RESOURCE_GROUP_NAME` `CLUSTER_NAME` ，然後輸入命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

您也可以刪除包含叢集的資源群組來刪除叢集。 請注意，這會刪除群組中的所有資源，包括預設儲存體帳戶。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>調整叢集

使用 [az hdinsight 重設](/cli/azure/hdinsight#az-hdinsight-resize) 大小，將指定的 hdinsight 叢集大小調整為指定的大小。 藉由取代 `RESOURCE_GROUP_NAME` 和相關資訊來編輯下列命令 `CLUSTER_NAME` 。 取代為您的叢集所 `WORKERNODE_COUNT` 需的背景工作節點數目。 如需調整叢集的詳細資訊，請參閱 [調整 HDInsight](./hdinsight-scaling-best-practices.md)叢集。 輸入命令：

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何執行不同的 HDInsight 叢集系統管理工作。 如需詳細資訊，請參閱下列文章：

* [使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)
