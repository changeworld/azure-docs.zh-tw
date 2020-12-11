---
title: Azure SQL Database 中資料庫的虛擬網路端點和規則
description: 將子網標示為虛擬網路服務端點。 然後，將端點作為虛擬網路規則新增至您資料庫的 ACL。 然後，您的資料庫會接受來自子網上所有虛擬機器和其他節點的通訊。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: d480239c0eb99ed48c13ec2fdb5b052574acc318
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092494"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>在 Azure SQL Database 中使用伺服器的虛擬網路服務端點和規則

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*虛擬網路規則* 是防火牆安全性功能，可控制 [Azure SQL Database](sql-database-paas-overview.md) 中的資料庫和彈性集區的伺服器，或 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 中的資料庫是否接受從虛擬網路中的特定子網傳送的通訊。 本文說明為何虛擬網路規則有時是最佳選項，可讓您安全地在 SQL Database 和 Azure Synapse Analytics 中與您的資料庫進行通訊。

> [!NOTE]
> 本文同時適用于 SQL Database 和 Azure Synapse Analytics。 為了簡單起見， *資料庫* 一詞是指 SQL Database 和 Azure Synapse Analytics 中的兩個資料庫。 同樣地， *伺服器* 的任何參考都會參考裝載 SQL Database 和 Azure Synapse Analytics 的 [邏輯 SQL server](logical-servers.md) 。

若要建立虛擬網路規則，必須先有[虛擬網路服務端點][vm-virtual-network-service-endpoints-overview-649d]規則可供參考。

## <a name="create-a-virtual-network-rule"></a>建立虛擬網路規則

如果您只想要建立虛擬網路規則，您可以直接跳到本文 [稍後](#anchor-how-to-by-using-firewall-portal-59j)的步驟和說明。

## <a name="details-about-virtual-network-rules"></a>虛擬網路規則的詳細資料

本節描述虛擬網路規則的一些詳細資料。

### <a name="only-one-geographic-region"></a>只有一個地理區域

每個虛擬網路服務端點只適用于一個 Azure 區域。 端點不會讓其他區域接受來自子網的通訊。

任何虛擬網路規則只以套用其基礎端點的區域為對象。

### <a name="server-level-not-database-level"></a>伺服器層級，而非資料庫層級

每個虛擬網路規則都會套用至您的整個伺服器，而不只套用至伺服器上的一個特定資料庫。 換句話說，虛擬網路規則適用于伺服器層級，而不是在資料庫層級。

相反地，IP 規則可以在任一個層級套用。

### <a name="security-administration-roles"></a>安全性管理角色

虛擬網路服務端點的管理中會有安全性角色的分隔。 下列每個角色都需要採取動作：

- **網路系統管理員 ([網路參與者](../../role-based-access-control/built-in-roles.md#network-contributor) 角色) ：** &nbsp;開啟端點。
- **資料庫管理員 ([SQL Server 參與者](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 角色) ：** &nbsp;更新 (ACL) 的存取控制清單，以將指定的子網新增至伺服器。

#### <a name="azure-rbac-alternative"></a>Azure RBAC 替代方案

「網路管理員」和「資料庫管理員」角色的能力已超過管理虛擬網路規則所需。 只需要其中一部分能力。

在 Azure 中，您可以選擇使用[角色型存取控制 (RBAC)][rbac-what-is-813s] 來建立單一自訂安全性角色，而且只給予一部分必要的能力。 您可以使用自訂角色，而不是涉及網路系統管理員或資料庫管理員。如果您將使用者新增至自訂角色，而不是將使用者新增至其他兩個主要系統管理員角色，則安全性暴露的介面區會較低。

> [!NOTE]
> 在某些情況下，SQL Database 中的資料庫和虛擬網路子網位於不同的訂用帳戶中。 在這些情況下，您必須確定下列設定：
>
> - 這兩個訂用帳戶都必須位於相同的 Azure Active Directory (Azure AD) 租使用者中。
> - 使用者具有起始作業的必要許可權，例如啟用服務端點，以及將虛擬網路子網新增至指定的伺服器。
> - 這兩個訂用帳戶都必須註冊 Microsoft.Sql 提供者。

## <a name="limitations"></a>限制

針對 SQL Database，虛擬網路規則功能具有下列限制：

- 在 SQL Database 的資料庫防火牆中，每個虛擬網路規則都會參考一個子網。 所有這些參考的子網都必須裝載在主控資料庫的相同地理區域中。
- 每部伺服器最多可以有128個 ACL 專案適用于任何虛擬網路。
- 虛擬網路規則只適用于 Azure Resource Manager 的虛擬網路，而不適用於 [傳統部署模型][arm-deployment-model-568f] 網路。
- 開啟虛擬網路服務端點以 SQL Database 也會啟用適用於 MySQL 的 Azure 資料庫和適用於 PostgreSQL 的 Azure 資料庫的端點。 當端點設定為 **ON 時**，嘗試從端點連接到您的適用於 MySQL 的 Azure 資料庫或適用於 PostgreSQL 的 Azure 資料庫實例可能會失敗。
  - 根本原因是適用於 MySQL 的 Azure 資料庫和適用於 PostgreSQL 的 Azure 資料庫可能沒有設定虛擬網路規則。 您必須為適用於 MySQL 的 Azure 資料庫和適用於 PostgreSQL 的 Azure 資料庫設定虛擬網路規則，且連接將會成功。
  - 若要在已設定私人端點的 SQL 邏輯伺服器上定義虛擬網路防火牆規則，請將 [ **拒絕公用網路存取** ] 設定為 [ **否**]。
- 在防火牆上，IP 位址範圍適用于下列網路功能專案，但虛擬網路規則不會：
  - [站對站 (S2S) 虛擬私人網路 (VPN) ][vpn-gateway-indexmd-608y]
  - 透過[Azure ExpressRoute](../../expressroute/index.yml)從內部部署

### <a name="considerations-when-you-use-service-endpoints"></a>使用服務端點時的考慮

當您使用服務端點進行 SQL Database 時，請參閱下列考慮：

- **需要輸出至 Azure SQL Database 的公用 Ip。** 必須開啟 (Nsg) 的網路安全性群組，才能 SQL Database Ip 以允許連線。 您可以使用 NSG [服務標記](../../virtual-network/network-security-groups-overview.md#service-tags) 進行 SQL Database。

### <a name="expressroute"></a>ExpressRoute

如果您使用來自內部部署的 [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，針對公用對等互連或 Microsoft 對等互連，您必須識別所使用的 NAT IP 位址。 在公用對等互連中，每個 Expressroute 線路預設都會使用兩個 NAT IP 位址，而這兩個位址會在流量進入 Microsoft Azure 網路骨幹時套用至 Azure 服務流量。 針對 Microsoft 對等互連，使用的 NAT IP 位址是由客戶或服務提供者所提供。 若要允許存取您的服務資源，就必須在資源 IP 防火牆設定中允許這些公用 IP 位址。 若要尋找您的公用對等互連 ExpressRoute 線路 IP 位址，請透過 Azure 入口網站[開啟有 ExpressRoute 的支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 若要深入瞭解適用于 ExpressRoute 公用和 Microsoft 對等互連的 NAT，請參閱 [Azure 公用對等互連的 nat 需求](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

若要允許從您的線路進行 SQL Database 的通訊，您必須為 NAT 的公用 IP 位址建立 IP 網路規則。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>使用虛擬網路服務端點搭配 Azure 儲存體的影響

Azure 儲存體已實作功能，可讓您限制連線至 Azure 儲存體帳戶的連線。 如果您選擇使用此功能搭配 SQL Database 使用的 Azure 儲存體帳戶，您可能會遇到問題。 接下來是受此影響的 SQL Database 和 Azure Synapse Analytics 功能的清單和討論。

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase 和 COPY 語句

PolyBase 和 COPY 語句通常用來將資料從 Azure 儲存體帳戶載入 Azure Synapse Analytics，以進行高輸送量資料內嵌。 如果您要從中載入資料的 Azure 儲存體帳戶僅能存取一組虛擬網路子網，則當您使用 PolyBase 且將複製語句儲存至儲存體帳戶時，即會中斷連線。 若要使用 COPY 和 PolyBase 搭配 Azure Synapse Analytics 連接到受虛擬網路保護的 Azure 儲存體來啟用匯入和匯出案例，請遵循本節中的步驟。

#### <a name="prerequisites"></a>必要條件

- 使用 [本指南](/powershell/azure/install-az-ps)來安裝 Azure PowerShell。
- 如果您有一般用途 v1 或 Azure Blob 儲存體帳戶，您必須遵循 [升級至一般用途 v2 儲存體帳戶](../../storage/common/storage-account-upgrade.md)中的步驟，先升級至一般用途 v2。
- 您必須啟用 [允許信任的 Microsoft 服務]，才能在 [Azure 儲存體帳戶 **防火牆和虛擬網路** 設定] 功能表下，開啟 **此儲存體帳戶的存取權**。 啟用此設定可讓 PolyBase 和 COPY 語句使用強式驗證來連線到儲存體帳戶，其中網路流量會留在 Azure 骨幹上。 如需詳細資訊，請參閱 [本指南](../../storage/common/storage-network-security.md#exceptions)。

> [!IMPORTANT]
> SQL Database 仍支援 PowerShell Azure Resource Manager 模組，但未來所有的開發都是針對 Az. Sql 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到 Bug 修正。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

#### <a name="steps"></a>步驟

1. 如果您有獨立的專用 SQL 集區，請使用 PowerShell 向 Azure AD 註冊您的 SQL server：

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Azure Synapse Analytics 工作區內的專用 SQL 集區不需要此步驟。

1. 如果您有 Azure Synapse Analytics 工作區，請註冊您工作區的系統管理身分識別：

   1. 移至 Azure 入口網站中的 Azure Synapse Analytics 工作區。
   2. 移至 [ **受控** 識別] 窗格。
   3. 請確定已啟用 [ **允許管線** ] 選項。
   
1. 遵循 [建立儲存體帳戶](../../storage/common/storage-account-create.md)中的步驟，建立 **一般用途 v2 儲存體帳戶**。

   > [!NOTE]
   >
   > - 如果您有一般用途 v1 或 Blob 儲存體帳戶，您必須先遵循 [升級至一般用途 v2 儲存體帳戶](../../storage/common/storage-account-upgrade.md)中的步驟，*以升級為 v2* 。
   > - 如 Azure Data Lake Storage Gen2 的已知問題，請參閱 [Azure Data Lake Storage Gen2 的已知問題](../../storage/blobs/data-lake-storage-known-issues.md)。

1. 在您的儲存體帳戶底下，移至 [ **存取控制] (IAM)**，然後選取 [ **新增角色指派**]。 將 **儲存體 Blob 資料參與者** Azure 角色指派給裝載您專用 SQL 集區的伺服器或工作區，您已向 Azure AD 註冊。

   > [!NOTE]
   > 只有在儲存體帳戶上具有擁有者許可權的成員可以執行此步驟。 如需各種 Azure 內建角色，請參閱 [azure 內建角色](../../role-based-access-control/built-in-roles.md)。
  
1. 若要啟用 Azure 儲存體帳戶的 PolyBase 連線能力：

   1. 如果您先前未建立資料庫 [主要金鑰](/sql/t-sql/statements/create-master-key-transact-sql) ，請建立一個。

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. 建立身分 **識別 = 「受控服務識別**」的資料庫範圍認證。

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - 不需要使用 Azure 儲存體存取金鑰來指定秘密，因為這項機制在幕後使用 [受控識別](../../active-directory/managed-identities-azure-resources/overview.md) 。
       > - 身分識別名稱必須是「 **受控服務識別** 」，PolyBase 連線才能使用保護虛擬網路的 Azure 儲存體帳戶。

   1. 建立外部資料源，其 `abfss://` 配置可使用 PolyBase 連接到您的一般用途 v2 儲存體帳戶。

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - 如果您已經有與一般用途 v1 或 Blob 儲存體帳戶相關聯的外部資料表，您應該先卸載這些外部資料表。 然後卸載對應的外部資料源。 接下來，使用 `abfss://` 連接至一般用途 v2 儲存體帳戶的配置來建立外部資料源，如先前所示。 然後使用這個新的外部資料源重新建立所有外部資料表。 您可以使用 [ [產生和發佈腳本] Wizard](/sql/ssms/scripting/generate-and-publish-scripts-wizard) ，輕鬆地產生所有外部資料表的建立腳本。
       > - 如需配置的詳細資訊 `abfss://` ，請參閱 [使用 Azure Data Lake Storage Gen2 URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。
       > - 如需建立外部資料源的詳細資訊，請參閱 [本指南](/sql/t-sql/statements/create-external-data-source-transact-sql)。

   1. 使用 [外部資料表](/sql/t-sql/statements/create-external-table-transact-sql)查詢為一般。

### <a name="sql-database-blob-auditing"></a>SQL Database blob 審核

Blob 稽核會將稽核記錄推送到您自己的儲存體帳戶。 如果此儲存體帳戶使用虛擬網路服務端點功能，從 SQL Database 到儲存體帳戶的連線將會中斷。

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>將虛擬網路防火牆規則新增到您的伺服器

不久之前，在增強這項功能之前，您必須先開啟虛擬網路服務端點，才能在防火牆中執行即時虛擬網路規則。 與指定的虛擬網路子網相關聯的端點 SQL Database 中的資料庫。 從2018年1月起，您可以藉由設定 **>ignoremissingvnetserviceendpoint** 旗標來規避這項需求。 現在，您可以將虛擬網路防火牆規則新增到您的伺服器，而不需要開啟虛擬網路服務端點。

只是設定防火牆規則不能協助保護伺服器的安全。 您也必須開啟虛擬網路服務端點，安全性才會生效。 當您開啟服務端點時，您的虛擬網路子網會發生停機，直到它完成從關閉到開啟的轉換為止。 這段停機時間特別適用于大型虛擬網路的環境。 您可以使用 **IgnoreMissingVNetServiceEndpoint** 旗標來減少或排除在轉換期間的停機時間。

您可以使用 PowerShell 設定 **IgnoreMissingVNetServiceEndpoint** 旗標。 如需詳細資訊，請參閱 [PowerShell 以建立 SQL Database 的虛擬網路服務端點和規則][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="errors-40914-and-40615"></a>錯誤 40914 和 40615

連接錯誤40914與 *虛擬網路規則* 相關，如 Azure 入口網站的 **防火牆** 窗格中所指定。 錯誤40615很類似，但它與防火牆上的 *IP 位址規則* 相關。

### <a name="error-40914"></a>錯誤 40914

**郵件內文：** 「無法開啟登入所要求的伺服器 '*[伺服器名稱]*'。 不允許用戶端存取伺服器。」

錯誤說明：用戶端所在的子網路含有虛擬網路伺服器端點。 但是伺服器沒有任何虛擬網路規則可將與資料庫通訊的許可權授與子網。

**錯誤解決方式：** 在 Azure 入口網站的 [ **防火牆** ] 窗格上，使用虛擬網路規則控制來新增子網的 [虛擬網路規則](#anchor-how-to-by-using-firewall-portal-59j) 。

### <a name="error-40615"></a>錯誤 40615

**郵件內文：** 「無法開啟登入所要求的伺服器 '」 {0} 。 {1}不允許 IP 位址為 ' ' 的用戶端存取伺服器。」

**錯誤描述：** 用戶端嘗試從未獲授權連接至伺服器的 IP 位址進行連接。 伺服器防火牆沒有任何 IP 位址規則，可讓用戶端從指定的 IP 位址與資料庫進行通訊。

錯誤解決方式：輸入用戶端的 IP 位址作為 IP 規則。 使用 Azure 入口網站中的 [ **防火牆** ] 窗格來執行此步驟。

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>使用入口網站來建立虛擬網路規則

本節說明如何使用 [Azure 入口網站][http-azure-portal-link-ref-477t] ，在 SQL Database 的資料庫中建立 *虛擬網路規則* 。 此規則會告知您的資料庫接受已標記為 *虛擬網路服務端點* 的特定子網的通訊。

> [!NOTE]
> 如果您想要將服務端點新增至伺服器的虛擬網路防火牆規則，請先確定子網的服務端點已開啟。
>
> 如果子網的服務端點未開啟，入口網站會要求您啟用。 在您新增規則的相同窗格上，選取 [ **啟用** ] 按鈕。

## <a name="powershell-alternative"></a>PowerShell 替代方案

腳本也可以使用 PowerShell Cmdlet **New-AzSqlServerVirtualNetworkRule** 或 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)來建立虛擬網路規則。 如果您有興趣，請參閱 [PowerShell 以建立 SQL Database 的虛擬網路服務端點和規則][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="rest-api-alternative"></a>REST API 替代方案

就內部而言，適用于 SQL 虛擬網路動作的 PowerShell Cmdlet 會呼叫 REST Api。 您可以直接呼叫 REST API。

- [虛擬網路規則：作業][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>必要條件

您必須已經有以特定虛擬網路服務端點 *類型名稱* 標記的子網，SQL Database 相關聯。

- 相關的端點類型名稱是 **Microsoft.Sql**。
- 如果您的子網路可能未標記類型名稱，請參閱[確認您的子網路是端點][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure 入口網站步驟

1. 登入 [Azure 入口網站][http-azure-portal-link-ref-477t]。

1. 搜尋並選取 **[SQL** server]，然後選取您的伺服器。 在 [ **安全性**] 底下，選取 [ **防火牆和虛擬網路**]。

1. 將 [ **允許存取 Azure 服務** ] 設為 [ **關閉**]。

    > [!IMPORTANT]
    > 如果您讓控制項保持設定為 **開啟**，則您的伺服器會接受來自 Azure 界限內任何子網的通訊。 這是來自某個 IP 位址的通訊，這些 IP 位址可辨識為 Azure 資料中心定義範圍內的其中一個 IP 位址。 從安全性的觀點來看，將控制項設定為 [ **開啟** ] 可能會有過多的存取權。 與 SQL Database 的虛擬網路規則功能協調 Microsoft Azure 虛擬網路服務端點功能，可減少您的安全性介面區。

1. 在 [**虛擬網路**] 區段中選取 [ **+ 新增現有** 的]。

    ![顯示選取 [+ 新增現有的 (子網端點] 的螢幕擷取畫面，作為 SQL 規則) 。][image-portal-firewall-vnet-add-existing-10-png]

1. 在新的 [ **建立/更新** ] 窗格中，以您的 Azure 資源名稱填寫方塊。

    > [!TIP]
    > 您的子網必須包含正確的位址前置詞。 您可以在入口網站中找到 **位址前置** 詞值。 移至所有 **資源** &gt; **類型** &gt; 的 **虛擬網路**。 篩選條件會顯示您的虛擬網路。 選取您的虛擬網路，然後選取 [ **子網**]。 **位址範圍** 資料行具有您需要的位址前置詞。

    ![顯示針對新規則填入方塊的螢幕擷取畫面。][image-portal-firewall-create-update-vnet-rule-20-png]

1. 選取窗格底部附近的 **[確定]** 按鈕。

1. 請在 [ **防火牆** ] 窗格上查看產生的虛擬網路規則。

    ![在 [防火牆] 窗格中顯示新規則的螢幕擷取畫面。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 規則有下列狀態：
>
> - **就緒**：表示您起始的作業已成功。
> - **Failed**：表示您起始的作業已失敗。
> - **已刪除**：僅適用于刪除作業，指出規則已被刪除且不再適用。
> - **InProgress**：表示作業正在進行中。 作業處於此狀態時，會套用舊規則。

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>相關文章

- [Azure 虛擬網路服務端點][vm-virtual-network-service-endpoints-overview-649d]
- [伺服器層級和資料庫層級防火牆規則][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>後續步驟

- [使用 PowerShell 來建立虛擬網路服務端點，然後使用虛擬網路規則來 SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [虛擬網路規則：][rest-api-virtual-network-rules-operations-862r] 使用 REST Api 的作業

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
