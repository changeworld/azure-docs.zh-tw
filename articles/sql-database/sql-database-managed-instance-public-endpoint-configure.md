---
title: 配置公共終結點 - 託管實例
description: 瞭解如何為託管實例配置公共終結點
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256155"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中設定公用端點

[託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)的公共終結點允許從[虛擬網路](../virtual-network/virtual-networks-overview.md)外部對託管實例進行資料訪問。 您可以通過多租戶 Azure 服務（如 Power BI、Azure 應用服務或本地網路）訪問託管實例。 通過在託管實例上使用公共終結點，您無需使用 VPN，這有助於避免 VPN 輸送量問題。

在本文中，您將學會如何：

> [!div class="checklist"]
> - 在 Azure 門戶中為託管實例啟用公共終結點
> - 使用 PowerShell 為託管實例啟用公共終結點
> - 配置託管實例網路安全性群組以允許流量到託管實例公共終結點
> - 獲取託管實例公共終結點連接字串

## <a name="permissions"></a>權限

由於託管實例中的資料的敏感性，啟用託管實例公共終結點的配置需要兩步過程。 此安全措施堅持職責分離 （SoD）：

- 在託管實例上啟用公共終結點需要由託管實例管理員完成。可在 SQL 託管實例資源的 **"概述"** 頁上找到託管實例管理員。
- 允許使用需要由網路系統管理員完成的網路安全性群組進行流量。有關詳細資訊，請參閱[網路安全性群組許可權](../virtual-network/manage-network-security-group.md#permissions)。

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>為 Azure 門戶中的託管實例啟用公共終結點

1. 在以下處啟動 Azure 門戶<https://portal.azure.com/.>
1. 使用託管實例打開資源組，然後選擇要配置公共終結點的**SQL 託管實例**。
1. 在 **"安全**設置"上，選擇 **"虛擬網路**"選項卡。
1. 在虛擬網路配置頁中，選擇 **"啟用**"，然後選擇 **"保存**"圖示以更新配置。

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>使用 PowerShell 為託管實例啟用公共終結點

### <a name="enable-public-endpoint"></a>啟用公用端點

請執行下列 PowerShell 命令。 將**訂閱 ID**替換為訂閱 ID。 還將**rg 名稱**替換為託管實例的資源組，並將**mi 名稱**替換為託管實例的名稱。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>禁用公共終結點

要使用 PowerShell 禁用公共終結點，將執行以下命令（如果配置了入站埠 3342，不要忘記關閉 NSG）：

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>允許在網路安全性群組中進行公共終結點流量

1. 如果託管實例的配置頁仍然打開，請導航到 **"概述"** 選項卡。否則，請返回**SQL 託管實例**資源。 選擇**虛擬網路/子網**連結，該連結將帶您到虛擬網路配置頁面。

    ![mi-概述.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 選擇虛擬網路左側配置窗格上的 **"子網**"選項卡，並記下託管實例的安全**組**。

    ![mi-vnet-子網.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 返回包含託管實例的資源組。 您應該會看到上面注明的**網路安全性群組**名稱。 選擇要進入網路安全性群組配置頁的名稱。

1. 選擇"**入站安全規則"** 選項卡，**並添加**具有以下設置的**deny_all_inbound**規則更高的優先順序的規則： </br> </br>

    |設定  |建議的值  |描述  |
    |---------|---------|---------|
    |**來源**     |任何 IP 位址或服務標記         |<ul><li>對於電源 BI 等 Azure 服務，請選擇 Azure 雲服務標記</li> <li>對於您的電腦或 Azure VM，請使用 NAT IP 位址</li></ul> |
    |**源埠範圍**     |*         |保留此為 * （任何），因為源埠通常是動態分配的，因此不可預測 |
    |**目的地**     |任意         |將目標保留為"Any"，以允許流量進入託管實例子網 |
    |**目標埠範圍**     |3342         |將目標埠範圍限定為 3342，這是託管實例公共 TDS 終結點 |
    |**協定**     |TCP         |託管實例使用 TCP 協定進行 TDS |
    |**動作**     |Allow         |允許通過公共終結點對託管實例的入站流量 |
    |**優先順序**     |1300         |確保此規則比**deny_all_inbound**規則更高的優先順序 |

    ![米-nsg-規則.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 埠 3342 用於公共終結點連接到託管實例，此時無法更改。

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>獲取託管實例公共終結點連接字串

1. 導航到已為公共終結點啟用的 SQL 託管實例配置頁。 在 **"設置"** 配置下選擇 **"連接字串**"選項卡。
1. 請注意，公共終結點主機名稱的格式<mi_name>。**公共**.<dns_zone>.database.windows.net，用於連接的埠為 3342。

    ![mi-公共-終結點-conn-字串.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[將 Azure SQL 資料庫託管實例安全地用於公共終結點](sql-database-managed-instance-public-endpoint-securely.md)。