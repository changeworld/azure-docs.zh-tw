---
title: 使用 Python 為 Azure 資料資源管理器添加群集主體
description: 在本文中，您將瞭解如何使用 Python 為 Azure 資料資源管理器添加群集主體。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965134"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>使用 Python 為 Azure 資料資源管理器添加群集主體

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure 資源管理器範本](cluster-principal-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中，可以使用 Python 為 Azure 資料資源管理器添加群集主體。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [創建群集](create-cluster-database-python.md)。

## <a name="install-python-package"></a>安裝 Python 套件

若要為 Azure 資料總管 (Kusto) 安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元。 請執行這個命令：

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>添加群集主體

下面的示例演示如何以程式設計方式添加群集主體。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenant_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscription_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| client_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端 ID。|
| client_secret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。 |
| resource_group_name | *testrg* | 包含群集的資源組的名稱。|
| cluster_name | *mykustocluster* | 群集的名稱。|
| principal_assignment_name | *群集主體分配1* | 群集主體資源的名稱。|
| principal_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體 ID，可以是使用者電子郵件、應用程式 ID 或安全性群組名稱。|
| 角色 (role) | *所有資料庫管理* | 群集主體的角色，可以是"所有資料庫管理"或"所有資料庫檢視器"。|
| tenant_id_for_principal | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體的租戶 ID。|
| principal_type | *應用程式* | 主體的類型，可以是"使用者"、"應用"或"組"|

## <a name="next-steps"></a>後續步驟

* [添加資料庫主體](database-principal-python.md)
