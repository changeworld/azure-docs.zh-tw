---
title: 建立 Azure HDInsight-Azure Data Lake Storage Gen2-Azure CLI
description: 瞭解如何使用 Azure CLI Azure Data Lake Storage Gen2 搭配 Azure HDInsight 叢集。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: e8cfe55f4843439743535b978483d8518bd020fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858759"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>使用 Azure CLI 建立具有 Data Lake Storage Gen2 的叢集

若要建立使用 Data Lake Storage Gen2 儲存的 HDInsight 叢集，請遵循下列步驟。

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉 Azure Data Lake Storage Gen2，請參閱 [總覽一節](hdinsight-hadoop-use-data-lake-storage-gen2.md)。 
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行 CLI 指令碼範例，您有三個選項：
    - 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md) (請參閱下一節)。
    - 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
    - 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 使用 `az login` 與您想要部署使用者指派受控識別的 azure 訂用帳戶相關聯的帳戶，登入 azure。Azure CLI。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

您可以 [下載範例範本](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) 檔案，並 [下載範例參數](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)檔案。 使用範本和以下的 Azure CLI 程式碼片段之前，請使用正確的值取代下列預留位置：

| 預留位置 | 描述 |
|---|---|
| `<SUBSCRIPTION_ID>` | 您 Azure 訂用帳戶的識別碼 |
| `<RESOURCEGROUPNAME>` | 您要在其中建立新叢集和儲存體帳戶的資源群組。 |
| `<MANAGEDIDENTITYNAME>` | 受控識別的名稱，將會在您的儲存體帳戶上獲得 Azure Data Lake Storage Gen2 的許可權。 |
| `<STORAGEACCOUNTNAME>` | 將建立 Azure Data Lake Storage Gen2 的新儲存體帳戶。 |
| `<FILESYSTEMNAME>`  | 此叢集應該在儲存體帳戶中使用之檔案系統的名稱。 |
| `<CLUSTERNAME>` | 您的 HDInsight 叢集名稱。 |
| `<PASSWORD>` | 您選擇使用 SSH 和 Ambari 儀表板登入叢集的密碼。 |

下列程式碼片段會執行下列初始步驟：

1. 登入您的 Azure 帳戶。
1. 設定使用中的訂用帳戶來完成建立作業。
1. 為新的部署活動建立新的資源群組。
1. 建立使用者指派的受控識別。
1. 將擴充功能新增至 Azure CLI，以使用 Data Lake Storage Gen2 的功能。
1. 使用旗標，建立具有 Data Lake Storage Gen2 的新儲存體帳戶 `--hierarchical-namespace true` 。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下來，登入入口網站。 將新使用者指派的受控識別新增至儲存體帳戶上的 **儲存體 Blob 資料參與者** 角色。 此步驟會在 [使用 Azure 入口網站的](hdinsight-hadoop-use-data-lake-storage-gen2.md)步驟3中描述。

 > [!IMPORTANT]
 > 確定您的儲存體帳戶具有「 **儲存體 Blob 資料參與者** 」角色許可權的使用者指派身分識別，否則叢集建立將會失敗。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

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

## <a name="next-steps"></a>後續步驟

您已成功建立 HDInsight 叢集。 現在，請了解如何使用您的叢集。

### <a name="apache-spark-clusters"></a>Apache Spark 叢集

* [使用指令碼動作來自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)
* [使用 Scala 建立獨立應用程式](spark/apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 叢集

* [搭配 HDInsight 使用 Apache Hive](hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 MapReduce](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 叢集

* [開始使用 HDInsight 上的 Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md)
* [開發適用於 HDInsight 上 Apache HBase 的 Java 應用程式](hbase/apache-hbase-build-java-maven-linux.md)