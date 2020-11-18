---
title: 快速入門：使用 Azure CLI 建立 Synapse 工作區
description: 遵循本指南中的步驟，使用 Azure CLI 建立 Azure Synapse 工作區。
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 46ab1df1b776bf8dc9d6d917e5394c3efeec0de4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659381"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure Synapse 工作區

Azure CLI 是管理 Azure 資源的 Azure 命令列體驗。 您可以在瀏覽器中使用它搭配 Azure Cloud Shell。 您也可以將它安裝在 macOS、Linux 或 Windows 上，並從命令列執行。

在本快速入門中，您會了解如何使用 Azure CLI 建立 Synapse 工作區。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

- 下載並安裝 [jq](https://stedolan.github.io/jq/download/)，這是輕量且彈性的命令列 JSON 處理器
- [Azure Data Lake Storage Gen2 儲存體帳戶](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Azure Synapse 工作區必須能夠讀取和寫入選取的 ADLS Gen2 帳戶。 此外，對於您連結為主要儲存體帳戶的任何儲存體帳戶，您必須已在建立儲存體帳戶時啟用 [階層式命名空間]，如[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)頁面所述。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>使用 Azure CLI 建立 Azure Synapse 工作區

1. 定義必要的環境變數，以建立 Azure Synapse 工作區的資源。

    | 環境變數名稱 | 描述 |
    |---|---|---|
    |StorageAccountName| 現有 ADLS Gen2 儲存體帳戶的名稱。|
    |StorageAccountResourceGroup| 現有 ADLS Gen2 儲存體帳戶資源群組的名稱。 |
    |FileShareName| 現有儲存體檔案系統的名稱。|
    |SynapseResourceGroup| 為您的 Azure Synapse 資源群組選擇新的名稱。 |
    |區域| 選擇其中一個 [Azure 區域](https://azure.microsoft.com/global-infrastructure/geographies/#overview)。 |
    |SynapseWorkspaceName| 為新的 Azure Synapse 工作區選擇唯一的名稱。 |
    |SqlUser| 選擇新使用者名稱的值。|
    |SqlPassword| 選擇安全的密碼。|
    |||

2. 建立資源群組作為 Azure Synapse 工作區的容器：
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. 擷取 ADLS Gen 2 儲存體帳戶金鑰：
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. 擷取 ADLS Gen 2 儲存體端點 URL：
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (選擇性) 您一律可以查看 ADLS Gen2 的儲存體帳戶金鑰和端點為何：
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. 建立 Azure Synapse 工作區：
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. 取得 Azure Synapse 工作區的 Web 和開發 URL：
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. 建立防火牆規則，以允許從您的電腦存取 Azure Synapse 工作區：

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. 開啟儲存在環境變數 `WorkspaceWeb` 中的 Azure Synapse 工作區 Web URL 位址，以存取您的工作區：

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Azure Synapse 工作區 Web](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>清除資源

請遵循下列步驟來刪除 Azure Synapse 工作區。
> [!WARNING]
> 刪除 Azure Synapse 工作區將會移除分析引擎和儲存在包含 SQL 集區和工作區繼資料之資料庫中的資料。 您無法再連線到 SQL 或 Apache Spark 端點。 系統將刪除所有程式碼成品 (查詢、筆記本、作業定義和管線)。
>
> 刪除工作區 **不會** 影響連結到工作區之 Data Lake Store Gen2 中的資料。

如果想刪除 Azure Synapse 工作區，請完成下列命令：

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>後續步驟

接下來，您可以[建立 SQL 集區](quickstart-create-sql-pool-studio.md)或 [建立 Apache Spark 集區](quickstart-create-apache-spark-pool-studio.md)，以開始分析和探索您的資料。
