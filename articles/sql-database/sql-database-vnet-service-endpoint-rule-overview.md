---
title: 適用于單一和集區資料庫的 VNet 端點和規則
description: 將子網路標示為虛擬網路服務端點。 然後將端點標示為虛擬網路規則，以列在 Azure SQL Database 的 ACL 中。 您的 SQL Database 將會接受來自子網路上所有虛擬機器和其他節點的通訊。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0562d609231d69d95f1d2b5b838663b704f8f2f3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255739"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>對資料庫伺服器使用虛擬網路服務端點和規則

「虛擬網路規則」是一個防火牆安全性功能，可控制適用於 Azure [SQL Database](sql-database-technical-overview.md) 中單一資料庫和彈性集區的資料庫伺服器，或是適用於 [SQL 資料倉儲](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)中資料庫的資料庫伺服器，是否會接受虛擬網路中特定子網路所傳來的通訊。 本文說明為何虛擬網路規則功能有時是讓 Azure SQL Database 和 SQL 資料倉儲安全地接受通訊的最佳選項。

> [!IMPORTANT]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。 本文「不」適用 Azure SQL Database 中的**受控執行個體**部署，因為它沒有相關聯的服務端點。

若要建立虛擬網路規則，必須先有[虛擬網路服務端點][vm-virtual-network-service-endpoints-overview-649d]，才能參考該規則。

## <a name="how-to-create-a-virtual-network-rule"></a>如何建立虛擬網路規則

如果只是建立虛擬網路規則，您可以直接跳到[本文稍後](#anchor-how-to-by-using-firewall-portal-59j)的步驟和說明。

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>虛擬網路規則的詳細資料

本節描述虛擬網路規則的一些詳細資料。

### <a name="only-one-geographic-region"></a>只有一個地理區域

每個虛擬網路服務端點只套用至一個 Azure 區域。 端點無法讓其他區域接受來自子網路的通訊。

任何虛擬網路規則只以套用其基礎端點的區域為對象。

### <a name="server-level-not-database-level"></a>伺服器層級，非資料庫層級

每個虛擬網路規則會套用至整個 Azure SQL Database 伺服器，而不只是伺服器上的一個特定資料庫。 換句話說，虛擬網路規則是在伺服器層級套用，而不是資料庫層級。

- 相反地，IP 規則可以在任一個層級套用。

### <a name="security-administration-roles"></a>安全性管理角色

有一組獨立的安全性角色負責管理虛擬網路服務端點。 下列每個角色都需要採取動作：

- **網路系統管理員：** &nbsp; 開啟端點。
- **資料庫管理員：** &nbsp; 更新存取控制清單（ACL），以將指定的子網新增至 SQL Database 伺服器。

RBAC 替代方案：

「網路管理員」和「資料庫管理員」角色的能力已超過管理虛擬網路規則所需。 只需要其中一部分能力。

您可以選擇在 Azure 中使用[角色型存取控制（RBAC）][rbac-what-is-813s] ，以建立只具有必要功能子集的單一自訂角色。 您可以使用自訂角色，而不是涉及網路系統管理員或資料庫管理員。如果您將使用者新增至自訂角色，而不是將使用者新增至其他兩個主要系統管理員角色，則安全性暴露的介面區會較低。

> [!NOTE]
> 某些案例中，Azure SQL Database 和 VNet 子網路是在不同的訂用帳戶。 在這些情況下，您必須確保下列設定：
> - 兩個訂用帳戶在相同的 Azure Active Directory 租用戶中。
> - 使用者具備啟動作業的必要權限，例如啟用服務端點、將 VNet 子網路新增至指定的伺服器。
> - 這兩個訂用帳戶都必須註冊 Microsoft.Sql 提供者。

## <a name="limitations"></a>限制

對於 Azure SQL Database，虛擬網路規則功能具有下列限制：

- 在針對 SQL Database 的防火牆中，每個虛擬網路規則都會參考一個子網路。 裝載所有這些參考子網路的地理區域，必須和裝載 SQL Database 的地理區域相同。

- 在任何給定的虛擬網路中，每個 Azure SQL Database 伺服器最多只能有 128 個 ACL 項目。

- 虛擬網路規則只適用于 Azure Resource Manager 虛擬網路;而不是[傳統部署模型][arm-deployment-model-568f]網路。

- 開啟 Azure SQL Database 的虛擬網路服務端點也可啟用 MySQL 和 PostgreSQL Azure 服務的端點。 不過，開啟端點時，嘗試從端點連線到您的 MySQL 或 PostgreSQL 執行個體可能會失敗。
  - 根本原因是 MySQL 和 PostgreSQL 可能未設定虛擬網路規則。 您必須為適用於 MySQL 和 PostgreSQL 的 Azure 資料庫設定虛擬網路規則，設定之後就能成功連線。

- 在防火牆上，IP 位址範圍會套用到下列網路項目，但虛擬網路規則不這麼做：
  - [站對站（S2S）虛擬私人網路（VPN）][vpn-gateway-indexmd-608y]
  - 透過[ExpressRoute][expressroute-indexmd-744v]的內部部署

### <a name="considerations-when-using-service-endpoints"></a>使用服務端點時的注意事項

使用 Azure SQL Database 的服務端點時，請檢閱下列注意事項：

- **輸出至 Azure SQL Database 公用 IP 是必需的**：必須針對 Azure SQL Database IP 開啟網路安全性群組 (NSG)，才能夠進行連線。 為了完成此操作，您可以使用適用於 Azure SQL Database 的 NSG [服務標籤](../virtual-network/security-overview.md#service-tags)。

### <a name="expressroute"></a>ExpressRoute

如果您使用來自內部部署的 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 進行公用對等互連或 Microsoft 對等互連，您將必須識別所使用的 NAT IP 位址。 在公用對等互連中，每個 Expressroute 線路預設都會使用兩個 NAT IP 位址，而這兩個位址會在流量進入 Microsoft Azure 網路骨幹時套用至 Azure 服務流量。 在 Microsoft 對等互連中，所用的 NAT IP 位址是由客戶提供或由服務提供者提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 深入了解 [ExpressRoute 公用與 Microsoft 對等互連的 NAT。](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
若要允許從您的線路與 Azure SQL Database 通訊，您必須為 NAT 的公用 IP 位址建立 IP 網路規則。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>使用 VNet 服務端點搭配 Azure 儲存體的影響

Azure 儲存體已實作功能，可讓您限制連線至 Azure 儲存體帳戶的連線。 如果您選擇使用這項功能，並使用 Azure SQL Server 正在使用的 Azure 儲存體帳戶，可能會遇到問題。 接下來是受此影響的 Azure SQL Database 和 Azure SQL 資料倉儲功能清單和討論。

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL 資料倉儲 PolyBase

PolyBase 通常用於將資料從 Azure 儲存體帳戶載入 Azure SQL 資料倉儲。 如果您正在載入資料的來源 Azure 儲存體帳戶限制只能存取一組 VNet 子網路，從 PolyBase 到帳戶的連線會中斷。 如需透過連線至固定到 VNet 的 Azure 儲存體的 Azure SQL 資料倉儲來啟用 PolyBase 匯入和匯出案例，請按照下列所示的步驟進行：

#### <a name="prerequisites"></a>Prerequisites

- 使用此[指南](https://docs.microsoft.com/powershell/azure/install-az-ps)安裝 Azure PowerShell。
- 如果您有一般用途 v1 或 Blob 儲存體帳戶，您必須先使用此[指南](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)先升級至一般用途 v2。
- 您必須開啟 Azure 儲存體帳戶 [防火牆與虛擬網路] 設定功能表下方的 [允許信任的 Microsoft 服務存取此儲存體帳戶]。 如需詳細資訊請參閱此[指南](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az .Sql 模組。 AzureRM 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

#### <a name="steps"></a>步驟

1. 在 PowerShell 中，使用 Azure Active Directory （AAD）**註冊您的 Azure SQL Server**裝載您的 Azure SQL 資料倉儲實例：

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. 以此**指南**建立[一般用途的 v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。

   > [!NOTE]
   > - 如果您有一般用途 v1 或 Blob 儲存體帳戶，您必須先使用此 **指南**[升級至 v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)。
   > - 關於 Azure Data Lake Storage Gen2 的已知問題，請參閱此[指南](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)。
    
1. 請瀏覽至您儲存體帳戶之下的 [存取控制 \(IAM\)]，然後按一下 [新增角色指派]。 將**儲存體 Blob 資料參與者**RBAC 角色指派給您的 Azure SQL Server 裝載您已向 AZURE ACTIVE DIRECTORY （AAD）註冊的 Azure SQL 資料倉儲，如步驟 # 1 所示。

   > [!NOTE]
   > 僅有具備「擁有者」權限的成員才能執行此步驟。 關於 Azure 資源的各種內建角色，請參閱此[指南](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。
  
1. **Polybase 連線至 Azure 儲存體帳戶：**

   1. 建立資料庫 **[主要金鑰](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** (如果先前尚未建立)：

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. 使用 **IDENTITY = 'Managed Service Identity'** 建立資料庫範圍的認證：

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - 不需要使用 Azure 儲存體存取金鑰指定 SECRET，因為此機制會秘密使用[受控身分識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。
       > - PolyBase 連線的 IDENTITY 名稱應為 **'Managed Service Identity'** ，才能搭配使用固定至 VNet 的 Azure 儲存體帳戶。

   1. 建立具有 `abfss://` 配置的外部資料源，以使用 PolyBase 連接到您的一般用途 v2 儲存體帳戶：

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - 如果您已有與一般用途 v1 或 Blob 儲存體帳戶相關聯的外部資料表，請先卸除這些外部資料表，再卸除對應的外部資料來源。 然後，使用 `abfss://` 配置來建立外部資料源，並連接到上述的一般用途 v2 儲存體帳戶，然後使用這個新的外部資料源重新建立所有外部資料表。 您可以使用[產生和發佈指令碼精靈](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard)，輕鬆地為所有的外部資料表產生建立指令碼。
       > - 如需 `abfss://` 配置的詳細資訊，請參閱本[指南](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)。
       > - 如需 CREATE EXTERNAL DATA SOURCE 的詳細資訊，請參閱此[指南](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) (英文)。

   1. 以一般方式使用[外部資料表](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)查詢。

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database Blob 稽核

Blob 稽核會將稽核記錄推送到您自己的儲存體帳戶。 如果這個儲存體帳戶使用 VNet 服務端點功能，則 Azure SQL Database 與儲存體帳戶的連線將會中斷。

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>在不開啟 VNet 服務端點的情況下將 VNet 防火牆規則新增至伺服器

很久以前，在尚未增強這項功能之前，您必須先開啟 VNet 服務端點，才可以在防火牆中實作即時的 VNet 規則。 端點會將指定的 VNet 子網路與 Azure SQL Database 建立關聯。 但是從 2018 年 1 月起，您不再需要這樣做，只需設定 **IgnoreMissingVNetServiceEndpoint** 旗標即可。

只是設定防火牆規則不能協助您保護伺服器。 您也必須開啟 VNet 服務端點，安全性才會生效。 當您開啟服務端點時，您的 VNet 子網路會停機，直到完成關閉到開啟的轉換。 特別是大型的 VNet，這會更明顯。 您可以使用 **IgnoreMissingVNetServiceEndpoint** 旗標來減少或排除在轉換期間的停機時間。

您可以使用 PowerShell 設定 **IgnoreMissingVNetServiceEndpoint** 旗標。 如需詳細資訊，請參閱[PowerShell 以建立 Azure SQL Database 的虛擬網路服務端點和規則][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="errors-40914-and-40615"></a>錯誤 40914 和 40615

連線錯誤 40914 與「虛擬網路規則」有關，這些規則會在 Azure 入口網站中的 [防火牆] 窗格中指定。 錯誤 40615 很類似，但它與防火牆上的「IP 位址規則」相關。

### <a name="error-40914"></a>錯誤 40914

訊息文字：無法開啟登入所要求的伺服器 ' *[server-name]* '。 用戶端不得存取該伺服器。

錯誤說明：用戶端所在的子網路含有虛擬網路伺服器端點。 但 Azure SQL Database 伺服器沒有將和 SQL Database 通訊的權限授與子網路的虛擬網路規則。

錯誤解決方式：在 Azure 入口網站的 [防火牆] 窗格上，使用虛擬網路規則控制來為子網路[新增虛擬網路規則](#anchor-how-to-by-using-firewall-portal-59j)。

### <a name="error-40615"></a>錯誤 40615

訊息文字：無法開啟登入所要求的伺服器 '{0}'。 不允許 IP 位址為 '{1}' 的用戶端存取伺服器。

錯誤說明：未授權 IP 位址連線到 Azure SQL Database 伺服器，但用戶端嘗試從其連線。 伺服器防火牆沒有允許用戶端從指定的 IP 位址與 SQL 資料庫通訊的 IP 位址規則。

錯誤解決方式：輸入用戶端的 IP 位址作為 IP 規則。 您可以使用 Azure 入口網站中的 [防火牆] 窗格來執行這項工作。

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>入口網站可以建立虛擬網路規則

本節說明如何使用[Azure 入口網站][http-azure-portal-link-ref-477t]，在您的 Azure SQL Database 中建立*虛擬網路規則*。 此規則會指示 SQL Database 接受一個標記為「虛擬網路服務端點」的特定子網路所傳來的通訊。

> [!NOTE]
> 如果您想要將服務端點新增至 Azure SQL Database 伺服器的 VNet 防火牆規則，請先確認已為子網路開啟服務端點。
>
> 若未在子網路上開啟服務端點，入口網站將會要求您加以啟用。 在您新增規則的相同刀鋒視窗上，按一下 [啟用] 按鈕。

## <a name="powershell-alternative"></a>PowerShell 替代方案

腳本也可以使用 PowerShell Cmdlet **New-AzSqlServerVirtualNetworkRule**或[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)來建立虛擬網路規則。 如有興趣，請參閱[PowerShell 以建立 Azure SQL Database 的虛擬網路服務端點和規則][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="rest-api-alternative"></a>REST API 替代方案

SQL VNet 動作的 PowerShell cmdlet 會在內部呼叫 REST API。 您可以直接呼叫 REST API。

- [虛擬網路規則：作業][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Prerequisites

您必須已有一個子網路是以 Azure SQL Database 相關的特定虛擬網路服務端點「類型名稱」所標記。

- 相關的端點類型名稱是 **Microsoft.Sql**。
- 如果您的子網可能未以類型名稱標記，請參閱[驗證您的子網是端點][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure 入口網站步驟

1. 登入 [Azure 入口網站][http-azure-portal-link-ref-477t]。

2. 搜尋並選取 **[SQL**server]，然後選取您的伺服器。 在 [**安全性**] 底下，選取 [**防火牆和虛擬網路**]。

3. 將 [允許存取 Azure 服務] 控制項設為 [關閉]。

    > [!IMPORTANT]
    > 如果您將此控制項保持設定為 [開啟]，您的 Azure SQL Database 伺服器就會接受來自 Azure 界限內任何子網的通訊，也就是源自 Azure 資料中心定義範圍內的其中一個 IP 位址。 就安全性觀點而言，讓此控制項保持 [開啟] 可能使存取過多。 Microsoft Azure 虛擬網路服務端點功能，連同 SQL Database 的虛擬網路規則功能，可縮小安全性曝露面。

4. 按一下 [虛擬網路] 區段中的 [+ 新增現有的] 控制項。

    ![按一下新增現有的 (子網路端點，作為 SQL 規則)。][image-portal-firewall-vnet-add-existing-10-png]

5. 在新的 [建立/更新] 窗格中，將您的 Azure 資源名稱填入控制項中。

    > [!TIP]
    > 必須包含子網路的正確 [位址首碼]。 您可以在入口網站中找到值。
    > 流覽**所有資源**&gt; &gt;**虛擬網路**的**所有類型**。 篩選條件會顯示您的虛擬網路。 按一下您的虛擬網路，然後按一下 [子網路]。 [位址範圍] 資料行具有您需要的位址首碼。

    ![填入新規則的欄位。][image-portal-firewall-create-update-vnet-rule-20-png]

6. 按一下窗格底端附近的 [確定]按鈕。

7. 在防火牆窗格上查看產生的虛擬網路規則。

    ![在防火牆窗格上查看新的規則。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 規則有下列狀態：
> - **就緒：** 指出您起始的作業已成功。
> - **失敗：** 指出您起始的作業已失敗。
> - **刪除：** 只適用於刪除作業，指出規則已被刪除，且不再套用。
> - **進行中：** 指出正在進行作業。 作業處於此狀態時，會套用舊規則。

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>相關文章

- [Azure 虛擬網路服務端點][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database 伺服器層級和資料庫層級防火牆規則][sql-db-firewall-rules-config-715d]

Azure SQL Database 的虛擬網路規則功能，已於 2017 年 9 月底推出。

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 建立虛擬網路服務端點，然後用 Azure SQL Database 的虛擬網路規則。][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [虛擬網路規則：][rest-api-virtual-network-rules-operations-862r]使用 REST Api 的作業

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
