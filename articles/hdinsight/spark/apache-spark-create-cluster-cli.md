---
title: 快速入門：Apache Spark 叢集搭配 Azure CLI - Azure HDInsight
description: 本快速入門顯示如何使用 Azure CLI 在 Azure HDInsight 中建立 Apache Spark 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: 02113988c76dd2565c7109c2ac18c3d1287189db
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891134"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>快速入門：使用 Azure CLI 在 Azure HDInsight 中建立 Apache Spark 叢集

在本快速入門中，您會了解如何使用 Azure 命令列介面 (CLI)，在 Azure HDInsight 中建立 Apache Spark 叢集。 Azure HDInsight 是供企業使用的受控、全方位的開放原始碼分析服務。 適用於 HDInsight 的 Apache Spark 架構能夠運用記憶體內部處理，使得資料分析及叢集運算更為快速。 Azure CLI 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。

如果您同時使用多個叢集，您會想要建立虛擬網路，如果您使用的是 Spark 叢集，也會想要使用 Hive Warehouse Connector。 如需詳細資訊，請參閱[針對 Azure HDInsight 規劃虛擬網路](../hdinsight-plan-virtual-network-deployment.md)和[整合 Apache Spark 和 Apache Hive 與 Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md)。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (如果您不想使用 Azure Cloud Shell)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>建立 Apache Spark 叢集

1. 登入您的 Azure 訂用帳戶。 如果您打算使用 Azure Cloud Shell，請選取以下程式碼區塊右上角的 [試試看]  。 另外，輸入下列命令：

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 設定環境變數。 本快速入門中使用的變數是以 Bash 為基礎的。 針對其他環境，會需要一點變化。 將以下程式碼片段中的 RESOURCEGROUPNAME、LOCATION、CLUSTERNAME、STORAGEACCOUNTNAME 和 PASSWORD 取代為所需的值。 然後輸入 CLI 命令來設定環境變數。

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. 輸入以下命令來建立資源群組：

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. 輸入以下命令來建立 Azure 儲存體帳戶：

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. 輸入以下命令，從 Azure 儲存體帳戶擷取主要金鑰並儲存至變數：

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. 輸入以下命令來建立 Azure 儲存體容器：

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. 輸入下列命令來建立 Apache Spark 叢集：

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

## <a name="clean-up-resources"></a>清除資源

完成此快速入門之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地刪除該叢集。 您也需支付 HDInsight 叢集的費用 (即使未使用該叢集)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

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

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會了解如何使用 Azure CLI，在 Azure HDInsight 中建立 Apache Spark 叢集。  前往下一個教學課程，以了解如何使用 HDInsight 叢集來執行範例資料的互動式查詢。

> [!div class="nextstepaction"]
> [在 Apache Spark 上執行互動式查詢](./apache-spark-load-data-run-query.md)
