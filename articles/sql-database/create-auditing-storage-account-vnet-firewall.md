---
title: 審核到 VNet 和防火牆後面的存儲帳戶
description: 配置審核以在虛擬網路和防火牆後面的存儲帳戶上寫入資料庫事件
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387621"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>將審核寫入 VNet 和防火牆後面的存儲帳戶

[對 Azure SQL 資料庫](sql-database-technical-overview.md)和 Azure[同步分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)進行審核支援將資料庫事件寫入虛擬網路和防火牆後面的[Azure 存儲帳戶](../storage/common/storage-account-overview.md)。 

本文介紹為此選項配置 Azure SQL Server 和 Azure 存儲帳戶的兩種方法。 第一個使用 Azure 門戶，第二個使用 REST。

### <a name="background"></a>背景

[Azure 虛擬網路 （VNet）](../virtual-network/virtual-networks-overview.md)是 Azure 中私人網路絡的基本構建基塊。 VNet 可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。 VNet 與您自己的資料中心中的傳統網路類似，但也帶來了 Azure 基礎結構的其他優勢，如擴展、可用性和隔離。

要瞭解有關 VNet 概念、最佳實踐等的更多資訊，請參閱什麼是[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

要瞭解有關如何創建虛擬網路的更多資訊，請參閱[快速入門：使用 Azure 門戶創建虛擬網路](../virtual-network/quick-create-portal.md)。

## <a name="prerequisites"></a>Prerequisites

要將審核寫入 VNet 或防火牆後面的存儲帳戶，需要以下先決條件：

> [!div class="checklist"]
> * 通用 v2 存儲帳戶。 如果您有通用 v1 或 Blob 存儲帳戶，則[升級到通用 v2 存儲帳戶](../storage/common/storage-account-upgrade.md)。 有關詳細資訊，請參閱[存儲帳戶的類型](../storage/common/storage-account-overview.md#types-of-storage-accounts)。
> * 存儲帳戶必須位於與 Azure SQL 資料庫伺服器相同的訂閱和位置。 
> * Azure 存儲帳戶需要`Allow trusted Microsoft services to access this storage account`。 在存儲帳戶**防火牆和虛擬網路**上設置。
> * 您必須對選定的`Microsoft.Authorization/roleAssignments/write`存儲帳戶具有許可權。 有關詳細資訊，請參閱[Azure 內置角色](../role-based-access-control/built-in-roles.md)。

## <a name="configure-in-azure-portal"></a>在 Azure 入口網站中設定

使用訂閱連接到[Azure 門戶](https://portal.azure.com)。 導航到資源組和 Azure SQL 資料庫伺服器。

1. 按一下"安全"標題下的**審核**。 選擇 **"打開**"。

2. 選取 [**儲存體**]。 選擇將保存日誌的存儲帳戶。 存儲帳戶必須符合[先決條件](#prerequisites)中列出的要求。

3. 打開**存儲詳細資訊** 

  > [!NOTE]
  > 如果所選存儲帳戶位於 VNet 後面，您將看到以下消息：
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >如果未看到此消息，則存儲帳戶不在 VNet 後面。

4. 選擇保留期的天數。 然後按一下 **[確定]**。 超過保留期日誌的日誌將被刪除。

5. 選擇 **"在**審核設置上保存"。

您已成功將審核配置為寫入 VNet 或防火牆後面的存儲帳戶。 

## <a name="configure-with-rest-commands"></a>使用 REST 命令進行配置

作為使用 Azure 門戶的替代方法，可以使用 REST 命令配置審核，以在 VNet 和防火牆後面的存儲帳戶上寫入資料庫事件。 

本節中的示例腳本要求您在運行腳本之前更新腳本。 取代指令碼中的下列值：

|範例值|示例描述|
|:-----|:-----|
|`<subscriptionId>`| Azure 訂用帳戶識別碼|
|`<resource group>`| 資源群組|
|`<sql database server>`| Azure SQL 資料庫伺服器名稱|
|`<administrator login>`| SQL 資料庫管理員帳戶 |
|`<complex password>`| 管理員帳戶的複雜密碼|

要配置 SQL 審核以將事件寫入 VNet 或防火牆後面的存儲帳戶，請：

1. 將 Azure SQL 資料庫伺服器註冊到 Azure 活動目錄 （Azure AD）。 使用 PowerShell 或 REST API。

   **電源外殼**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)：

   範例要求

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   Request body

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. 開啟 [Azure 入口網站](https://portal.azure.com)。 瀏覽至儲存體帳戶。 找到**存取控制 （IAM），** 然後按一下 **"添加角色指派**"。 將**存儲 Blob 資料參與者**RBAC 角色指派給 Azure SQL 伺服器，該伺服器託管在 Azure 活動目錄 （Azure AD） 中註冊的 Azure SQL 資料庫，如上一步驟所述。

   > [!NOTE]
   > 僅有具備「擁有者」權限的成員才能執行此步驟。 有關 Azure 資源的各種內置角色，請參閱[Azure 內置角色](../role-based-access-control/built-in-roles.md)。

3. 配置[Azure SQL 伺服器的 blob 稽核原則](/rest/api/sql/server%20auditing%20settings/createorupdate)，而不指定*存儲帳戶訪問金鑰*：

   範例要求

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   Request body

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 創建虛擬網路服務終結點，然後創建 Azure SQL 資料庫的虛擬網路規則。](sql-database-vnet-service-endpoint-rule-powershell.md)
- [虛擬網路規則：使用 REST API 的操作](/rest/api/sql/virtualnetworkrules)
- [對資料庫伺服器使用虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)
