---
title: 教學課程：在 Azure HDInsight 中建立端對端 ETL 管線以衍生 Sales Insights
description: 了解如何搭配 Azure HDInsight 使用建立 ETL 管線，以使用 Spark 隨選叢集和 Power BI 從銷售資料衍生見解。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: a0f081e0f8df00bbc99d2163fb54a2f15d92a159
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006427"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>教學課程：在 Azure HDInsight 中建立端對端資料管線以衍生 Sales Insights

在此教學課程中，您將建置可執行擷取、轉換和載入 (ETL) 作業的端對端資料管線。 此管線會使用在 Azure HDInsight 上執行的 [Apache Spark](./spark/apache-spark-overview.md) 和 Apache Hive 叢集來查詢和操作資料。 您也將使用諸如用於資料儲存的 Azure Data Lake Storage Gen2，以及用於視覺效果的 Power BI 等技術。

此資料管線會結合各種存放區中的資料、移除任何不必要的資料、附加新的資料，並將其全部重新載入至您的儲存體，以視覺化商業見解。 閱讀[大規模擷取、轉換和載入 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md) 以深入了解 ETL 管線。

![ETL 架構](./media/hdinsight-sales-insights-etl/architecture.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI - 至少為 2.2.0 版。 請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* jq，這是命令列 JSON 處理器。  請參閱 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

* [Azure 內建角色 - 擁有者](../role-based-access-control/built-in-roles.md)的成員。

* 如果使用 PowerShell 來觸發 Data Factory 管線，您將需要 [Az 模組](https://docs.microsoft.com/powershell/azure/)。

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller)，以在本教學課程結束時將商業深入解析視覺化。

## <a name="create-resources"></a>建立資源

### <a name="clone-the-repository-with-scripts-and-data"></a>複製含有指令碼和資料的存放庫

1. 登入 Azure 訂用帳戶。 如果您打算使用 Azure Cloud Shell，則選取程式碼區塊右上角的 [試試看]  。 或者，請輸入以下命令：

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. 確定您是 Azure 角色[擁有者](../role-based-access-control/built-in-roles.md)的成員。 請將 `user@contoso.com` 取代為您的帳戶，然後輸入下列命令：

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    若未傳回任何記錄，表示您不是成員，而無法完成本教學課程。

1. 從 [HDInsight Sales Insights ETL 存放庫](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)下載此教學課程的資料和指令碼。 輸入下列命令：

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. 確定已建立 `salesdata scripts templates`。 請使用下列命令進行驗證：

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>部署管線所需的 Azure 資源

1. 輸入下列程式碼，為所有指令碼新增執行權限：

    ```bash
    chmod +x scripts/*.sh
    ````

1. 設定資源群組的變數。 以現有或新的資源群組名稱取代 `RESOURCE_GROUP_NAME`，然後輸入以下命令：

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. 執行指令碼。 將 `LOCATION` 取代為所需的值，然後輸入以下命令：

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    如果您不確定要指定哪個區域，您可以使用 [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations) 命令擷取訂用帳戶所支援的區域清單。

    此命令會部署下列資源：

    * Azure Blob 儲存體帳戶。 此帳戶會保存公司銷售資料。
    * Azure Data Lake Storage Gen2 帳戶。 此帳戶將作為這兩個 HDInsight 叢集的儲存體帳戶。 在 [Azure HDInsight 與 Data Lake Storage Gen2 的整合](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)中深入了解 HDInsight 和 Data Lake Storage Gen2。
    * 使用者指派的受控識別。 此帳戶會為 HDInsight 叢集提供 Data Lake Storage Gen2 帳戶的存取權。
    * Apache Spark 叢集。 此叢集將用來清理和轉換原始資料。
    * Apache Hive [互動式查詢](./interactive-query/apache-interactive-query-get-started.md)叢集。 此叢集將允許查詢銷售資料並透過 Power BI 將其視覺化。
    * 網路安全性群組 (NSG) 規則支援的 Azure 虛擬網路。 此虛擬網路可讓叢集通訊並保護其通訊。

建立叢集可能需要約 20 分鐘的時間。

叢集的 SSH 存取所使用的預設密碼為 `Thisisapassword1`。 如果您想要變更密碼，請移至 `./templates/resourcesparameters_remainder.json` 檔案，然後變更 `sparksshPassword`、`sparkClusterLoginPassword`、`llapClusterLoginPassword` 和 `llapsshPassword` 參數的密碼。

### <a name="verify-deployment-and-collect-resource-information"></a>驗證部署並收集資源資訊

1. 如果您想要檢查部署的狀態，請移至 Azure 入口網站上的資源群組。 在 [設定]  下，選取 [部署]  ，然後選取您的部署。 在此，您可以看到已成功部署的資源，以及仍在部署中的資源。

1. 若要檢視叢集的名稱，請輸入下列命令：

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. 若要檢視 Azure 儲存體帳戶和存取金鑰，請輸入下列命令：

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. 若要檢視 Data Lake Storage Gen2 帳戶和存取金鑰，請輸入下列命令：

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>建立 Data Factory

Azure Data Factory 是有助於自動執行 Azure Pipelines 的工具。 它並非完成這些工作的唯一方法，但它是將這些程序自動化的絕佳途徑。 如需有關 Azure Data Factory 的詳細資訊，請參閱 [Azure Data Factory 文件](https://azure.microsoft.com/services/data-factory/)。

此 Data Factory 會有一個具有兩項活動的管線：

* 第一個活動會將資料從 Azure Blob 儲存體複製到 Data Lake Storage Gen 2 儲存體帳戶，以模擬資料內嵌。
* 第二個活動會轉換 Spark 叢集中的資料。 指令碼會移除不必要的資料行來轉換資料。 它也會附加可計算單一交易所產生之收益的新資料行。

若要設定您的 Azure Data Factory 管線，請執行以下命令。  您仍然應該位於 `hdinsight-sales-insights-etl` 目錄。

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

此指令碼會執行下列作業︰

1. 在 Data Lake Storage Gen2 儲存體帳戶上建立具有 `Storage Blob Data Contributor` 權限的服務主體。
1. 取得驗證權杖，以授與對 [Data Lake Storage Gen2 檔案系統 REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) 的 POST 要求權限。
1. 在 `sparktransform.py` 和 `query.hql` 檔案中填入 Data Lake Storage Gen2 儲存體帳戶的實際名稱。
1. 取得 Data Lake Storage Gen2 和 Blob 儲存體帳戶的儲存體金鑰。
1. 建立另一個資源部署，以建立 Azure Data Factory 管線及其相關聯的連結服務和活動。 它會將儲存體金鑰當作參數傳至範本檔案，讓連結的服務可正確存取儲存體帳戶。

## <a name="run-the-data-pipeline"></a>執行資料管線

### <a name="trigger-the-data-factory-activities"></a>觸發 Data Factory 活動

您在 Data Factory 管線中建立的第一個活動會將資料從 Blob 儲存體移至 Data Lake Storage Gen2。 第二個活動會在資料上套用 Spark 轉換，並將轉換後的 .csv 檔案儲存到新的位置。 整個管線可能需要幾分鐘的時間才能完成。

若要擷取 Data Factory 名稱，請輸入以下命令：

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

若要觸發管線，您可以執行下列其中一項：

* 在 PowerShell 中觸發 Data Factory 管線。 以適當的值取代 `RESOURCEGROUP` 和 `DataFactoryName`，然後執行下列命令：

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    視需要重新執行 `Get-AzDataFactoryV2PipelineRun` 以監視進度。

    Or

* 開啟 Data Factory，然後選取 [撰寫和監視]  。 從入口網站觸發 `IngestAndTransform` 管線。 如需有關透過入口網站觸發管線的詳細資訊，請參閱[使用 Azure Data Factory 在 HDInsight 中建立隨選 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)。

若要確認管線是否已執行，您可以採取下列其中一個步驟：

* 透過入口網站，在您的 Data Factory 中移至 [監視]  區段。
* 在 Azure 儲存體總管中，移至您的 Data Lake Storage Gen 2 儲存體帳戶。 移至 `files` 檔案系統，然後移至 `transformed` 資料夾，並檢查其內容以查看管線是否成功。

如需使用 HDInsight 轉換資料的其他方式，請參閱這篇有關於使用 [Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query) 的文章。

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>在互動式查詢叢集上建立資料表，以在 Power BI 上檢視資料

1. 使用 SCP 將 `query.hql` 檔案複製到 LLAP 叢集。 輸入命令：

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    提醒：預設密碼為 `Thisisapassword1`。

1. 使用 SSH 存取 LLAP 叢集。 輸入命令：

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. 使用下列命令執行指令碼：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    此指令碼會在互動式查詢叢集上建立受控資料表，供您從 Power BI 存取。

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>從銷售資料建立 Power BI 儀表板

1. 開啟 Power BI Desktop。

1. 從功能表，瀏覽至 [取得資料]   > [其他...]   > [Azure]   > [HDInsight 互動式查詢]  。

1. 選取 [連接]  。

1. 從 [HDInsight 互動式查詢]  對話方塊：
    1. 在 [伺服器]  文字方塊中，以 `https://LLAPCLUSTERNAME.azurehdinsight.net` 格式輸入 LLAP 叢集的名稱。
    1. 在 [資料庫]  文字方塊中，輸入 `default`。
    1. 選取 [確定]  。

1. 從 [AzureHive]  對話方塊：
    1. 在 [使用者名稱]  文字方塊中，輸入 `admin`。
    1. 在 [密碼]  文字方塊中，輸入 `Thisisapassword1`。
    1. 選取 [連接]  。

1. 從 [導覽器]  ，選取 `sales` 和/或 `sales_raw` 以預覽資料。 資料載入後，您可以使用您想要建立的儀表板進行試驗。 請參閱下列連結，以了解如何開始使用 Power BI 儀表板：

* [Power BI 設計工具的儀表板簡介](https://docs.microsoft.com/power-bi/service-dashboards)
* [教學課程：開始使用 Power BI 服務](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列命令刪除所有資源，以免產生相關費用。

1. 若要移除資源群組，請輸入以下命令：

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. 若要移除服務主體，請輸入以下命令：

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [大規模擷取、轉換和載入 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
