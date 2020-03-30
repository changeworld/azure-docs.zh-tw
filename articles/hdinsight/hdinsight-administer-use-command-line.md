---
title: 使用 Azure CLI 管理 Azure HDInsight 群集
description: 瞭解如何使用 Azure CLI 管理 Azure HDInsight 群集。 群集類型包括 Apache Hadoop、Spark、HBase、風暴、卡夫卡、互動式查詢和 ML 服務。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272769"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>使用 Azure CLI 管理 Azure HDInsight 群集

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

瞭解如何使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)管理 Azure HDInsight 群集。 Azure 命令列介面 (CLI) 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI。 如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 以取得相關步驟。

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[在 Linux 上開始使用 HDInsight。](hadoop/apache-hadoop-linux-tutorial-get-started.md)

## <a name="connect-to-azure"></a>連線到 Azure

登入您的 Azure 訂用帳戶。 如果計畫使用 Azure 雲外殼，請在代碼塊的右上角選擇 **"試用它**"。 或者，請輸入以下命令：

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>列出叢集

使用[az hdinsight 清單](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list)列出群集。 使用資源組的名稱替換`RESOURCE_GROUP_NAME`編輯以下命令，然後輸入以下命令：

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

使用[az hdinsight 顯示](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show)顯示指定群集的資訊。 通過替換`RESOURCE_GROUP_NAME`編輯下面的命令，然後`CLUSTER_NAME`使用相關資訊，然後輸入命令：

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>刪除叢集

使用[az hdinsight 刪除](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete)刪除指定的群集。 通過替換`RESOURCE_GROUP_NAME`編輯下面的命令，然後`CLUSTER_NAME`使用相關資訊，然後輸入命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

您也可以刪除包含叢集的資源群組來刪除叢集。 請注意，這將刪除組中的所有資源，包括預設存儲帳戶。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>調整叢集

使用[az hdinsight 調整大小](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize)以將指定的 HDInsight 群集調整到指定大小。 通過替換`RESOURCE_GROUP_NAME`和`CLUSTER_NAME`使用相關資訊編輯下面的命令。 替換為`WORKERNODE_COUNT`群集所需的輔助節點數。 有關縮放群集的詳細資訊，請參閱縮放[HDInsight 群集](./hdinsight-scaling-best-practices.md)。 輸入命令：

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何執行不同的 HDInsight 群集管理工作。 如需詳細資訊，請參閱下列文章：

* [使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [開始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
