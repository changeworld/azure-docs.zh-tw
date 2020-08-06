---
title: 教學課程：使用 Azure CLI 在 HDInsight 中建立 Apache Kafka REST Proxy 啟用的叢集
description: 了解如何在 Azure HDInsight 上使用 Kafka REST Proxy 執行 Apache Kafka 作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 44951fc19f36bb6652caf79ded96484bcc4b38f1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503135"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>教學課程：使用 Azure CLI 在 HDInsight 中建立 Apache Kafka REST Proxy 啟用的叢集

在本教學課程中，您會了解如何使用 Azure 命令列介面 (CLI)，在 Azure HDInsight 中建立 Apache Spark [REST Proxy 啟用](./rest-proxy.md)的叢集。 Azure HDInsight 是供企業使用的受控、全方位的開放原始碼分析服務。 Apache Kafka 是一個開放原始碼的分散式串流平台。 它通常會用來作為訊息代理程式，因為可以提供類似「發佈-訂閱」訊息佇列的功能。 Kafka REST Proxy 可讓您透過基於 HTTP 的 [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)，與您的 Kafka 叢集互動。 Azure CLI 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。

Apache Kafka API 只能由同一個虛擬網路中的資源來存取。 您可以使用 SSH 直接存取叢集。 若要將其他服務、網路或虛擬機器連線到 Apache Kafka，您必須先建立虛擬網路，然後建立網路中的資源。 如需詳細資訊，請參閱[使用虛擬網路連線到 Apache Kafka](./apache-kafka-connect-vpn-gateway.md)。

在此教學課程中，您將會學到：

> [!div class="checklist"]
> * Kafka REST Proxy 的必要條件
> * 使用 Azure CLI 建立 Apache Kafka 叢集

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>Prerequisites

* 向 Azure AD 註冊的應用程式。 您撰寫來與 Kafka REST Proxy 互動的用戶端應用程式，將使用此應用程式的識別碼和密碼向 Azure 進行驗證。 如需詳細資訊，請參閱[使用 Microsoft 身分識別平台註冊應用程式](../../active-directory/develop/quickstart-register-app.md)。

* 您已註冊的應用程式為其成員的 Azure AD 安全性群組。 此安全性群組將用來控制哪些應用程式能與 REST Proxy 互動。 如需建立 Azure AD 群組的詳細資訊，請參閱[使用 Azure Active Directory 建立基本群組和新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

* Azure CLI。 請確定您至少有 2.0.79 版。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-an-apache-kafka-cluster"></a>建立 Apache Kafka 叢集

1. 登入您的 Azure 訂用帳戶。

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. 設定環境變數。 本快速入門中使用的變數是以 Bash 為基礎。 針對其他環境，會需要一點變化。

    |變數 | 描述 |
    |---|---|
    |resourceGroupName|以新的資源群組名稱取代 RESOURCEGROUPNAME。|
    |location|以將要建立叢集的區域取代 LOCATION。 如需有效位置清單，請使用 `az account list-locations` 命令。|
    |clusterName|以新叢集的全域唯一名稱取代 CLUSTERNAME。|
    |storageAccount|以新的儲存體帳戶名稱取代 STORAGEACCOUNTNAME。|
    |httpPassword|以叢集登入的密碼 **admin** 取代 PASSWORD。|
    |sshPassword|以安全殼層使用者名稱的密碼 **sshuser** 取代 PASSWORD。|
    |securityGroupName|以 Kafka Rest Proxy 的用戶端 AAD 安全性群組名稱取代 SECURITYGROUPNAME。 變數將會傳遞至 `az-hdinsight-create` 的 `--kafka-client-group-name` 參數。|
    |securityGroupID|以 Kafka Rest Proxy 的用戶端 AAD 安全性群組識別碼取代 SECURITYGROUPID。 變數將會傳遞至 `az-hdinsight-create` 的 `--kafka-client-group-id` 參數。|
    |storageContainer|叢集將會使用的儲存體容器，在本教學課程中保持原狀。 此變數將會設定為叢集的名稱。|
    |workernodeCount|叢集中的背景工作節點數目，在本教學課程中保持原狀。 為了確保高可用性，Kafka 要求最少 3 個背景工作節點|
    |clusterType|HDInsight 叢集的類型，在本教學課程中保持原狀。|
    |clusterVersion|HDInsight 叢集版本，在本教學課程中保持原狀。 Kafka Rest Proxy 至少需要叢集版本 4.0。|
    |componentVersion|Kafka 版本，在本教學課程中保持原狀。 Kafka Rest Proxy 至少需要元件版本 2.1。|

    以所需的值更新變數。 然後輸入 CLI 命令來設定環境變數。

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. 輸入以下命令來[建立資源群組](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)：

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. 輸入以下命令來[建立 Azure 儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)：

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. 輸入以下命令，從 Azure 儲存體帳戶[擷取主要金鑰](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)並儲存至變數：

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. 輸入以下命令來[建立 Azure 儲存體容器](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)：

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [建立 HDInsight 叢集](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)。 輸入命令之前，請記下下列參數：

    1. Kafka 叢集的必要參數：

        |參數 | 描述|
        |---|---|
        |--type|此值必須是 **Kafka**。|
        |--workernode-data-disks-per-node|每個背景工作節點要使用的資料磁碟數目。 只有資料磁碟支援 HDInsight Kafka。 本教學課程使用的值為 **2**。|

    1. Kafka REST Proxy 的必要參數：

        |參數 | 描述|
        |---|---|
        |--kafka-management-node-size|節點的大小。 本教學課程使用的值為 **Standard_D4_v2**。|
        |--kafka-client-group-id|Kafka Rest Proxy 的用戶端 AAD 安全性群組識別碼。 此值會從 **$securityGroupID** 變數傳遞。|
        |--kafka-client-group-name|Kafka Rest Proxy 的用戶端 AAD 安全性群組名稱。 此值會從 **$securityGroupName** 變數傳遞。|
        |--version|HDInsight 叢集版本必須至少為 4.0。 此值會從 **$clusterVersion**變數傳遞。|
        |--component-version|Kafka 版本必須至少為 2.1。 此值會從 **$componentVersion**變數傳遞。|
    
        如果您想要建立不含 REST Proxy 的叢集，請從 `az hdinsight create` 命令刪除 `--kafka-management-node-size`、`--kafka-client-group-id` 和 `--kafka-client-group-name`。

    1. 如果您有現有的虛擬網路，請新增參數 `--vnet-name` 和 `--subnet`以及其值。

    輸入下列命令以建立叢集：

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    可能需要數分鐘的時間，才能完成叢集建立程序。 通常大約 15 分鐘。

## <a name="clean-up-resources"></a>清除資源

完成本文之後，您可能想要刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地刪除該叢集。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

輸入所有或部分的下列命令來移除資源：

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

既然您已使用 Azure CLI 在 Azure HDInsight 中成功建立 Apache Kafka REST Proxy 啟用的叢集，請使用 Python 程式碼與 REST Proxy 互動：

> [!div class="nextstepaction"]
> [建立範例應用程式](./rest-proxy.md#client-application-sample)
