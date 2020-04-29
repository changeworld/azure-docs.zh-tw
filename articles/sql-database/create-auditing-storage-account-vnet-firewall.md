---
title: VNet 和防火牆後方的儲存體帳戶審核
description: 設定在虛擬網路和防火牆後方的儲存體帳戶上寫入資料庫事件的審核
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387621"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>將 audit 寫入 VNet 和防火牆後方的儲存體帳戶

[Azure SQL Database](sql-database-technical-overview.md)和[Azure Synapse 分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的審核支援將資料庫事件寫入虛擬網路和防火牆背後的[Azure 儲存體帳戶](../storage/common/storage-account-overview.md)。 

本文說明兩種設定 Azure SQL Server 和 Azure 儲存體帳戶以用於此選項的方法。 第一個使用 Azure 入口網站，第二個使用 REST。

### <a name="background"></a>背景

[Azure 虛擬網路（VNet）](../virtual-network/virtual-networks-overview.md)是您在 Azure 中私人網路的基本組建區塊。 VNet 可讓多種類型的 Azure 資源 (例如 Azure 虛擬機器 (VM)) 安全地彼此通訊，以及與網際網路和內部部署網路通訊。 VNet 與您自己的資料中心內的傳統網路類似，但帶來了 it 額外的 Azure 基礎結構優點，例如調整、可用性和隔離。

若要深入瞭解 VNet 概念、最佳作法及其他許多，請參閱[什麼是 Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。

若要深入瞭解如何建立虛擬網路，請參閱[快速入門：使用 Azure 入口網站建立虛擬網路](../virtual-network/quick-create-portal.md)。

## <a name="prerequisites"></a>先決條件

若要讓 audit 寫入 VNet 或防火牆後方的儲存體帳戶，需要下列必要條件：

> [!div class="checklist"]
> * 一般用途 v2 儲存體帳戶。 如果您有一般用途 v1 或 blob 儲存體帳戶，請[升級至一般用途 v2 儲存體帳戶](../storage/common/storage-account-upgrade.md)。 如需詳細資訊，請參閱[儲存體帳戶的類型](../storage/common/storage-account-overview.md#types-of-storage-accounts)。
> * 儲存體帳戶必須與 Azure SQL Database 伺服器位於相同的訂用帳戶和相同的位置。 
> * Azure 儲存體帳戶需要`Allow trusted Microsoft services to access this storage account`。 在儲存體帳戶的**防火牆和虛擬網路**上設定此資訊。
> * 您必須具有`Microsoft.Authorization/roleAssignments/write`所選儲存體帳戶的許可權。 如需詳細資訊，請參閱[Azure 內建角色](../role-based-access-control/built-in-roles.md)。

## <a name="configure-in-azure-portal"></a>在 Azure 入口網站中設定

使用您的訂用帳戶連接到[Azure 入口網站](https://portal.azure.com)。 流覽至資源群組和 Azure SQL database 伺服器。

1. 按一下 [安全性] 標題底下的 [**審核**]。 選取 [**開啟**]。

2. 選取 [**儲存體**]。 選取將儲存記錄檔的儲存體帳戶。 儲存體帳戶必須符合[必要條件](#prerequisites)中所列的需求。

3. 開啟**儲存體詳細資料** 

  > [!NOTE]
  > 如果選取的儲存體帳戶位於 VNet 後方，您會看到下列訊息：
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >如果您看不到此訊息，則儲存體帳戶不在 VNet 後方。

4. 選取保留週期的天數。 然後按一下 [ **確定**]。 早于保留期限的記錄會遭到刪除。

5. 在您的 [審核設定] 上選取 [**儲存**]。

您已成功設定 audit，以寫入 VNet 或防火牆後方的儲存體帳戶。 

## <a name="configure-with-rest-commands"></a>使用 REST 命令進行設定

除了使用 Azure 入口網站以外，您還可以使用 REST 命令來設定 audit，以在 VNet 和防火牆後方的儲存體帳戶上寫入資料庫事件。 

本節中的範例腳本會要求您在執行腳本之前先更新它。 取代指令碼中的下列值：

|範例值|範例描述|
|:-----|:-----|
|`<subscriptionId>`| Azure 訂用帳戶識別碼|
|`<resource group>`| 資源群組|
|`<sql database server>`| Azure SQL 資料庫伺服器名稱|
|`<administrator login>`| SQL 資料庫系統管理員帳戶 |
|`<complex password>`| 系統管理員帳戶的複雜密碼|

若要設定 SQL Audit 以將事件寫入 VNet 或防火牆後方的儲存體帳戶：

1. 向 Azure Active Directory （Azure AD）註冊您的 Azure SQL Database 伺服器。 請使用 PowerShell 或 REST API。

   **PowerShell**
   
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

   要求本文

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

2. 開啟 [Azure 入口網站](https://portal.azure.com)。 瀏覽至儲存體帳戶。 找出 **[存取控制（IAM）**]，然後按一下 [**新增角色指派**]。 將**儲存體 Blob 資料參與者**RBAC 角色指派給您在上一個步驟中向 Azure Active Directory （Azure AD）註冊的 azure SQL Server。

   > [!NOTE]
   > 僅有具備「擁有者」權限的成員才能執行此步驟。 如需 Azure 資源的各種內建角色，請參閱[azure 內建角色](../role-based-access-control/built-in-roles.md)。

3. 設定[AZURE SQL server 的 blob 稽核原則](/rest/api/sql/server%20auditing%20settings/createorupdate)，而不指定*storageAccountAccessKey*：

   範例要求

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   要求本文

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 建立虛擬網路服務端點，然後用 Azure SQL Database 的虛擬網路規則。](sql-database-vnet-service-endpoint-rule-powershell.md)
- [虛擬網路規則：使用 REST Api 的作業](/rest/api/sql/virtualnetworkrules)
- [對資料庫伺服器使用虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)
