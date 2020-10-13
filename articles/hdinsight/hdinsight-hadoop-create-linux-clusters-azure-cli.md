---
title: 使用 Azure CLI Azure HDInsight 建立 Apache Hadoop 叢集
description: 瞭解如何使用跨平臺 Azure CLI 來建立 Azure HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: 04def98108bf996a8f8cabe0ad36c022011aa533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86080681"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>使用 Azure CLI 建立 HDInsight 叢集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本檔中的步驟將逐步解說如何使用 Azure CLI 建立 HDInsight 3.6 叢集。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

Azure CLI。 如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 以取得相關步驟。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>建立叢集

1. 登入 Azure 訂用帳戶。 如果您打算使用 Azure Cloud Shell，則選取程式碼區塊右上角的 [試試看]  。 或者，請輸入以下命令：

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 設定環境變數。 本文中使用的變數是以 Bash 為基礎。 針對其他環境，會需要一點變化。 請參閱 [az-hdinsight-建立](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 以取得建立叢集的可能參數完整清單。

    |參數 | 說明 |
    |---|---|
    |`--workernode-count`| 叢集中的背景工作節點數目。 本文使用變數 `clusterSizeInNodes` 作為傳遞給的值 `--workernode-count` 。 |
    |`--version`| HDInsight 叢集版本。 本文使用變數 `clusterVersion` 作為傳遞給的值 `--version` 。 另請參閱： [支援的 HDInsight 版本](./hdinsight-component-versioning.md#supported-hdinsight-versions)。|
    |`--type`| HDInsight 叢集的類型，例如： hadoop、interactivehive、hbase、kafka、風暴、spark、rserver、mlservices。  本文使用變數 `clusterType` 作為傳遞給的值 `--type` 。 另請參閱：叢集 [類型和](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type)設定。|
    |`--component-version`|不同 Hadoop 元件的版本，以空間分隔版本的 ' component = version ' 格式。 本文使用變數 `componentVersion` 作為傳遞給的值 `--component-version` 。 另請參閱： [Hadoop 元件](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)。|

    `RESOURCEGROUPNAME`將、、、和取代為所 `LOCATION` `CLUSTERNAME` `STORAGEACCOUNTNAME` `PASSWORD` 需的值。 視需要變更其他變數的值。 然後輸入 CLI 命令。

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. 輸入下列命令來[建立資源群組](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)：

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    如需有效位置的清單，請使用 `az account list-locations` 命令，然後使用值中的其中一個位置 `name` 。

4. 輸入下列命令來[建立 Azure 儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)：

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 藉由輸入下列命令，將[Azure 儲存體帳戶中的主要金鑰解壓縮](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)，並將其儲存在變數中：

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 輸入下列命令來[建立 Azure 儲存體容器](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)：

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 輸入下列命令以[建立 HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)叢集：

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight 叢集有各種不同類型，這些類型各自對應到叢集微調時所針對的工作負載或技術。 沒有任何支援方法可建立結合多個類型的叢集，例如在一個叢集上並存 Storm 和 HBase。

    叢集建立程式可能需要幾分鐘的時間才能完成。 通常大約 15 分鐘。

## <a name="clean-up-resources"></a>清除資源

完成本文之後，您可能想要刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地刪除該叢集。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

輸入所有或部分的下列命令來移除資源：

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](./hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>接下來的步驟

既然您已使用 Azure CLI 成功建立 HDInsight 叢集，請使用下列內容來瞭解如何使用您的叢集：

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 叢集

* [開發適用於 HDInsight 上 Apache Storm 的 Java 拓撲](storm/apache-storm-develop-java-topology.md)
* [在 HDInsight 上的 Apache Storm 中使用 Python 元件](storm/apache-storm-develop-python-topology.md)
* [使用 Apache Storm on HDInsight 部署和監視拓撲](storm/apache-storm-deploy-monitor-topology-linux.md)
