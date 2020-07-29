---
title: Azure 資料庫移轉服務的必要條件
description: 深入了解使用 Azure 資料庫移轉服務來執行資料庫移轉的必要條件概觀。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 8d87052ecfe85fa35c41c8b306bb48551fd06be9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322844"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>使用 Azure 資料庫移轉服務的必要條件概觀

有幾個必要條件，以確保 Azure 資料庫移轉服務在執行資料庫移轉時順暢執行。 某些必要條件會套用到服務所支援的所有案例 (來源目標組)，而有些必要條件則是特定案例獨有的。

下列各節列出與使用 Azure 的資料庫移轉服務相關聯的必要條件。

## <a name="prerequisites-common-across-migration-scenarios"></a>在移轉案例之間通用的必要條件

Azure 資料庫移轉服務必要條件在所有支援的移轉案例中都是通用的，包含以下需求：

* 使用 Azure Resource Manager 部署模型建立 Azure 資料庫移轉服務的 Microsoft Azure 虛擬網路，以使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)為您的內部部署來源伺服器提供站對站連線能力。
* 請確定您的虛擬網路網路安全性群組（NSG）規則不會封鎖下列通訊埠443、53、9354、445、12000。 如需虛擬網路 NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 使用來源資料庫前面的防火牆應用裝置時，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取來源資料庫，以進行移轉。
* 設定[用於 Database Engine 存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 啟用 TCP/IP 通訊協定，在 SQL Server Express 安裝期間預設會停用，方法是遵循[啟用或停用伺服器網路通訊協定](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的指示。

    > [!IMPORTANT]
    > 建立 Azure 資料庫移轉服務的實例時，需要存取通常不在相同資源群組內的虛擬網路設定。 因此，建立 DMS 實例的使用者需要訂用帳戶層級的許可權。 若要建立必要的角色（您可以視需要指派），請執行下列腳本：
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>將 SQL Server 移轉至 Azure SQL Database 的先決條件

除了所有移轉案例中都通用的 Azure 資料庫移轉服務先決條件之外，還有一些是適用於個別案例的必要條件。

當您使用 Azure 資料庫移轉服務來執行將 SQL Server 移轉到 Azure SQL Database 時，除了所有移轉案例中都通用的必要條件之外，請務必提出下列其他先決條件：

* 建立 Azure SQL Database 實例的實例，您可以遵循在[Azure 入口網站的 Azure SQL Database 中建立資料庫一](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)文中的詳細資料來執行。
* 下載及安裝[資料移轉小幫手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更新版本。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設會通過 TCP 連接埠 1433。
* 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，也許會想要啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
* 建立 SQL Database 的伺服器層級[防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)，以允許 Azure 資料庫移轉服務存取目標資料庫。 提供用於 Azure 資料庫移轉服務之虛擬網路的子網範圍。
* 確定用來連線至來源 SQL Server 執行個體的認證具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 權限。
* 確定用來連接到目標資料庫的認證具有目標資料庫的 CONTROL DATABASE 許可權。

   > [!NOTE]
   > 如需使用 Azure 資料庫移轉服務執行從 SQL Server 移轉到 Azure SQL Database 所需的必要條件完整清單，請參閱本教學課程[將 SQL Server 移轉到 Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)。
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>將 SQL Server 遷移至 Azure SQL 受控執行個體的必要條件

* 遵循在[Azure 入口網站中建立 AZURE SQL 受控執行個體](https://aka.ms/sqldbmi)一文中的詳細資料，建立 SQL 受控執行個體。
* 開啟您的防火牆，以針對 Azure 資料庫移轉服務 IP 位址或子網路範圍，允許連接埠 445 上的 SMB 流量。
* 開啟您的 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設會通過 TCP 連接埠 1433。
* 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，也許會想要啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
* 確定用來連線來源 SQL Server 和目標受控執行個體的登入是 sysadmin 伺服器角色的成員。
* 建立 Azure 資料庫移轉服務可用來備份來源資料庫的網路共用。
* 確認執行來源 SQL Server 執行個體的服務帳戶在您所建立的網路共用上具有寫入權限，而且來源伺服器的電腦帳戶對相同的共用具備讀取/寫入存取權。
* 記下在您先前建立的網路共用上具有完整控制權限的 Windows 使用者 (和密碼)。 Azure 資料庫移轉服務會模擬使用者認證，以便將備份檔案上傳到 Azure 儲存體容器以進行還原作業。
* 利用[使用儲存體總管來管理 Azure Blob 儲存體資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)一文中的步驟，來建立 Blob 容器並擷取其 SAS URI。 建立 SAS URI 時，請務必選取原則視窗上的所有權限 (讀取、寫入、刪除、列示)。

   > [!NOTE]
   > 如需使用 Azure 資料庫移轉服務來執行從 SQL Server 到 SQL 受控執行個體遷移的必要條件的完整清單，請參閱將[SQL Server 遷移至 sql 受控執行個體](https://aka.ms/migratetomiusingdms)教學課程。

## <a name="next-steps"></a>後續步驟

如需 Azure 資料庫移轉服務和區域可用性的概觀，請參閱[什麼是 Azure 資料庫移轉服務](dms-overview.md)一文。
