---
title: 設定公用端點-Azure SQL 受控執行個體
description: 瞭解如何設定 Azure SQL 受控執行個體的公用端點
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/07/2019
ms.openlocfilehash: f3708885759a6a353742fe89b4454b39496aeeab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619979"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>在 Azure SQL 受控執行個體中設定公用端點
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[受控實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)的公用端點可讓您從[虛擬網路](../../virtual-network/virtual-networks-overview.md)外部存取受控實例的資料。 您可以從多租使用者 Azure 服務（例如 Power BI、Azure App Service 或內部部署網路）存取受控實例。 使用受控實例上的公用端點時，您不需要使用 VPN，這有助於避免 VPN 輸送量問題。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> - 在 Azure 入口網站中啟用受控實例的公用端點
> - 使用 PowerShell 啟用受控實例的公用端點
> - 設定受控實例網路安全性群組以允許流量傳送至受控實例的公用端點
> - 取得受控實例的公用端點連接字串

## <a name="permissions"></a>權限

由於受控實例中的資料敏感度，啟用受控實例公用端點的設定需要進行兩個步驟的處理常式。 此安全性措施符合 (SoD) 的職責分隔：

- 在受控實例上啟用公用端點必須由受控實例管理員完成。受控實例系統管理員可以在受控實例資源的 **[總覽** ] 頁面上找到。
- 使用需要由網路系統管理員完成的網路安全性群組來允許流量。如需詳細資訊，請參閱 [網路安全性群組許可權](../../virtual-network/manage-network-security-group.md#permissions)。

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>在 Azure 入口網站中啟用受控實例的公用端點

1. 啟動 Azure 入口網站于 <https://portal.azure.com/.>
1. 使用受控實例開啟資源群組，然後選取您想要設定公用端點的 **SQL 受控實例** 。
1. 在 [ **安全性** 設定] 中，選取 [ **虛擬網路** ] 索引標籤。
1. 在 [虛擬網路設定] 頁面中，選取 [ **啟用** ]，然後選取 [ **儲存** ] 圖示來更新設定。

![螢幕擷取畫面顯示已啟用公用端點之 SQL 受控實例的 [虛擬網路] 頁面。](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>使用 PowerShell 啟用受控實例的公用端點

### <a name="enable-public-endpoint"></a>啟用公用端點

請執行下列 PowerShell 命令。 將 **訂** 用帳戶識別碼取代為您的訂用帳戶識別碼。 也請將 **rg 名稱** 取代為您受控實例的資源群組，並將 **mi 名稱** 取代為您受控實例的名稱。

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

### <a name="disable-public-endpoint"></a>停用公用端點

若要使用 PowerShell 停用公用端點，請執行下列命令 (，如果您已設定) ，也不記得關閉輸入埠3342的 NSG：

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>允許網路安全性群組上的公用端點流量

1. 如果您有受控實例的 [設定] 頁面仍在開啟，請流覽至 [ **總覽** ] 索引標籤。否則，請返回您的 **SQL 受控實例** 資源。 選取 [ **虛擬網路/子網** ] 連結，這會帶您前往 [虛擬網路設定] 頁面。

    ![螢幕擷取畫面顯示 [虛擬網路設定] 頁面，您可以在其中找到您的虛擬網路/子網值。](./media/public-endpoint-configure/mi-overview.png)

1. 在虛擬網路的左側設定窗格中選取 [ **子網** ] 索引標籤，並記下您受控實例的 **安全性群組** 。

    ![螢幕擷取畫面顯示 [子網] 索引標籤，您可以在其中取得受控實例的安全性群組。](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. 返回您的資源群組，其中包含您的受控實例。 您應該會看到上面所述的 **網路安全性群組** 名稱。 選取名稱以進入 [網路安全性群組設定] 頁面。

1. 選取 [ **輸入安全性規則** ] 索引標籤，並使用下列設定 **新增** 優先順序高於 **deny_all_inbound** 規則的規則： </br> </br>

    |設定  |建議的值  |描述  |
    |---------|---------|---------|
    |**Source**     |任何 IP 位址或服務標記         |<ul><li>針對 Power BI 之類的 Azure 服務，請選取 Azure 雲端服務標記</li> <li>針對您的電腦或 Azure 虛擬機器，請使用 NAT IP 位址</li></ul> |
    |**來源連接埠範圍**     |* |將此設為 * (任何) ，因為來源埠通常會動態配置，因此無法預測 |
    |**目的地**     |任意         |將目的地保留為任何，以允許流量進入受控實例子網 |
    |**目的地連接埠範圍**     |3342         |將目的地埠範圍設為3342，這是受控實例公用 TDS 端點 |
    |**通訊協定**     |TCP         |SQL 受控執行個體針對 TDS 使用 TCP 通訊協定 |
    |**動作**     |允許         |允許透過公用端點對受控實例進行輸入流量 |
    |**優先順序**     |1300         |請確定此規則的優先順序高於 **deny_all_inbound** 規則 |

    ![螢幕擷取畫面顯示 deny_all_inbound 規則上方的新 public_endpoint_inbound 規則的輸入安全性規則。](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 埠3342用於受控實例的公用端點連線，此時無法變更。

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>取得受控實例的公用端點連接字串

1. 流覽至已啟用公用端點的受控實例設定頁面。 在 [**設定**] 底下，選取 [**連接字串**] 索引標籤。
1. 請注意，公用端點主機名稱的格式 <mi_name>。**public**. <dns_zone> database.windows.net 以及用於連接的埠是3342。

    ![螢幕擷取畫面：顯示公用和私人端點的連接字串。](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>後續步驟

深入瞭解如何使用 [AZURE SQL 受控執行個體與公用端點安全地](public-endpoint-overview.md)搭配使用。
