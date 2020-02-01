---
title: 使用 Python 建立 Azure 資料總管叢集與資料庫
description: 了解如何使用 Python 建立 Azure 資料總管叢集與資料庫。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8d43965e87ab57d9f0c79c6661a761b06ccb7073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902112"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>使用 Python 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM 範本](create-cluster-database-resource-manager.md)

Azure Data Explorer 是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 在本文中，您會使用 Python 建立叢集和資料庫。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="install-python-package"></a>安裝 Python 套件

若要為 Azure 資料總管 (Kusto) 安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元。 請執行這個命令：

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>驗證
若要執行本文中的範例，我們需要 Azure AD 應用程式和服務主體，才能存取資源。 核取 [[建立 Azure AD 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)] 以建立免費的 Azure AD 應用程式，並在訂用帳戶範圍中新增角色指派。 它也會說明如何取得 `Directory (tenant) ID`、`Application ID`和 `Client Secret`。

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列命令建立您的叢集：

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from azure.common.credentials import ServicePrincipalCredentials

    #Directory (tenant) ID
    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Application ID
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    #Client Secret
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 所需的叢集名稱。|
   | sku_name | *Standard_D13_v2* | 將用於叢集的 SKU。 |
   | tier | *Standard* | SKU 層。 |
   | 存儲 | *number* | 叢集的實例數目。 |
   | resource_group_name | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    > [!NOTE]
    > **建立**叢集是長時間執行的作業。 方法**create_or_update**會傳回 LROPoller 的實例，請參閱[LROPoller 類別](/python/api/msrest/msrest.polling.lropoller?view=azure-python)以取得詳細資訊。

1. 執行下列命令來檢查是否已成功建立叢集：

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

如果結果中包含有 `Succeeded` 值的 `provisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列命令建立您的資料庫：

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | cluster_name | *mykustocluster* | 將在其中建立資料庫的叢集名稱。|
   | database_name | *mykustodatabase* | 您的資料庫名稱。|
   | resource_group_name | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | soft_delete_period | *3650 天，0:00:00* | 將保留資料以供查詢的時間長度。 |
   | hot_cache_period | *3650 天，0:00:00* | 資料將保留在快取中的時間長度。 |

1. 執行下列命令以查看您所建立的資料庫：

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您打算遵循其他文章，請保留您建立的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料總管 Python 程式庫內嵌資料](python-ingest-data.md)
