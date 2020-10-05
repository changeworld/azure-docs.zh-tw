---
title: Azure Windows 虛擬機器上的 SQL Server 概觀 | Microsoft Docs
description: 了解如何在雲端執行完整版本的 Azure 虛擬機器上的 SQL Server，而不需要管理任何內部部署硬體。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ae00703805bef6f5e9758bafcb781eda91cd93f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91272152"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>什麼是 Azure 虛擬機器上的 SQL Server (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 可讓您在雲端使用完整的 SQL Server 版本，而不需要管理任何內部部署硬體。 當您使用隨用隨付方案時，SQL Server 虛擬機器 (VM) 也會簡化授權成本。

Azure 虛擬機器會在全球許多不同的[地理區域](https://azure.microsoft.com/regions/)中執行。 此外，也提供各種[機器大小](../../../virtual-machines/windows/sizes.md)。 虛擬機器映像庫可讓您使用正確的版本、版次及作業系統建立 SQL Server VM。 這可讓虛擬機器成為許多不同 SQL Server 工作負載的適合選項。

如果您不熟悉 Azure VM 上的 SQL Server，請參閱我們深入的 [Azure SQL 影片系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的  *Azure VM 上的 SQL Server 概觀*影片：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>自動化更新

Azure 虛擬機器上的 SQL Server 可以利用[自動修補](automated-patching.md)來排程維護期間，以便自動安裝重要的 Windows 和 SQL Server 更新。

## <a name="automated-backups"></a>自動備份

Azure 虛擬機器上的 SQL Server 可以利用[自動備份](automated-backup.md)，以定期建立資料庫的備份至 Blob 儲存體。 您也可以手動使用此技術。 如需詳細資訊，請參閱 [使用 Azure 儲存體進行 SQL Server 備份與還原](azure-storage-sql-server-backup-restore-use.md)。

Azure 也針對在 Azure VM 中執行的 SQL Server 提供企業級備份解決方案。 完全受控備份解決方案，其支援 Always On 可用性群組、長期保留、時間點復原，以及集中管理和監視。 如需詳細資訊，請參閱 [Azure VM 中的 SQL Server 的 Azure 備份](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)。
  

## <a name="high-availability"></a>高可用性

如果您需要高可用性，請考慮設定 SQL Server 可用性群組。 這牽涉到虛擬網路中的多個 Azure 虛擬機器上的 SQL Server 執行個體。 您可以手動設定高可用性解決方案，也可以在 Azure 入口網站使用範本進行自動設定。 如需所有高可用性選項的概觀，請參閱 [Azure 虛擬機器上的 SQL Server 高可用性和災害復原](business-continuity-high-availability-disaster-recovery-hadr-overview.md)。

## <a name="performance"></a>效能

Azure 虛擬機器提供不同的機器大小，以符合各種工作負載需求。 SQL Server VM 也會提供已針對您的效能需求而最佳化的自動化儲存體組態。 如需為 SQL Server VM 設定儲存體的詳細資訊，請參閱 [SQL Server VM 的儲存體組態](storage-configuration.md)。 若要微調效能，請參閱 [Azure 虛擬機器上的 SQL Server 效能最佳做法](performance-guidelines-best-practices.md)。

## <a name="get-started-with-sql-server-vms"></a>開始使用 SQL Server VM

若要開始使用，請選擇具有您所需版本、版次及作業系統的 SQL Server 虛擬機器映像。 下列各節提供 Azure 入口網站中 SQL Server 虛擬機器資源庫映像的直接連結。

> [!TIP]
> 如需了解 SQL Server 映像定價的詳細資訊，請參閱 [Azure 虛擬機器上的 SQL Server 的定價指導方針](pricing-guidance.md)。 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> 隨用隨付方案
下表提供隨用隨付 SQL Server 映像的對照表。

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise)、[Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard)、[Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web)、[Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

若要查看可用的 Linux 虛擬機器上的 SQL Server 映像，請參閱 [Azure 虛擬機器上的 SQL Server 概觀 (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)。

> [!NOTE]
> 現在已可將 SQL Server VM 的依使用量付費授權模式變更成使用自己的授權。 如需詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](licensing-model-azure-hybrid-benefit-ahb-change.md)。 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> 自備授權
您也可以自備授權 (BYOL)。 在此案例中，您只需支付 VM 費用，而 SQL Server 授權不需任何額外的費用。  長期下來，自備授權可以讓您節省連續生產工作負載的成本。 如需使用此選項的相關需求，請參閱 [SQL Server Azure VM 的價格指引](pricing-guidance.md#byol)。

若要自備授權，您可以轉換依使用量付費的現有 SQL Server VM，或部署加上 **{BYOL}** 前置詞的映像。 如需有關在依使用量付費和 BYOL 授權模式之間切換的詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](licensing-model-azure-hybrid-benefit-ahb-change.md)。 

| 版本 | 作業系統 | 版本 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise)、[Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

較舊而無法在 Azure 入口網站中使用的 SQL Server 映像，可以使用 PowerShell 來部署。 若要使用 PowerShell 檢視所有可用的映像，請使用下列命令：

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

如需使用 PowerShell 部署 SQL Server VM 的詳細資訊，請檢視[如何使用 Azure PowerShell 佈建 SQL Server 虛擬機器](create-sql-vm-powershell.md)。


### <a name="connect-to-the-vm"></a>連接至 VM
建立 SQL Server VM 之後，從 SQL Server Management Studio (SSMS) 等應用程式或工具來連線該 VM。 如需相關指示，請參閱[連線到 Azure 上的 SQL Server 虛擬機器](ways-to-connect-to-sql.md)。

### <a name="migrate-your-data"></a>遷移資料
如果您有現有的資料庫，您會想要將該資料庫移到新佈建的 SQL Server VM。 如需移轉選項的清單和指引，請參閱 [將資料庫移轉至 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)。

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>使用 Azure 入口網站來建立及管理 Azure SQL 資源

Azure 入口網站提供單一頁面，您可以在其中管理 [所有的 Azure SQL 資源，](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql)包括 SQL 虛擬機器。

若要存取 **Azure SQL 資源**頁面，請選取 Azure 入口網站功能表中的 [Azure SQL]，或從任何頁面搜尋並選取 [Azure SQL]。

![搜尋 Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL 可讓您快速輕鬆地存取所有的 Azure SQL 資料庫、彈性集區、邏輯伺服器、受控執行個體和虛擬機器。 Azure SQL 不是服務或資源。 

若要管理現有的資源，請在清單中選取所需的項目。 若要建立新的 Azure SQL 資源，請選取 [+ 新增]。 

![建立 Azure SQL 資源](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

選取 [+ 新增] 後，您可在任意檔案上選取 [顯示詳細資料] 以檢視關於不同選項的其他資訊。

![資料庫圖格詳細資料](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

如需詳細資料，請參閱：

- [建立單一資料庫](../../database/single-database-create-quickstart.md)
- [建立彈性集區](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [建立受控執行個體](../../managed-instance/instance-create-quickstart.md)
- [建立 SQL Server 虛擬機器](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM 映像重新整理原則
Azure 只會針對每個支援的作業系統、版本和版次組合，維護一個虛擬機器映像。 這表示經過一段時間會重新整理映像，而且會移除舊的映像。 如需詳細資訊，請參閱 [SQL Server VM 常見問題集](frequently-asked-questions-faq.md#images)的「映像」一節。

## <a name="customer-experience-improvement-program-ceip"></a>客戶經驗改進計畫 (CEIP)
預設會啟用「客戶經驗改進計畫」(CEIP)。 這會定期將報表傳送至 Microsoft 以協助改進 SQL Server。 CEIP 不需要管理工作，除非您要在佈建之後將它停用。 您可以透過遠端桌面連接到 VM，以自訂或停用 CEIP。 然後執行 [SQL Server 錯誤和使用情況報告]  公用程式。 請遵循指示來停用報告功能。 如需有關資料收集的詳細資訊，請參閱 [SQL Server 隱私權聲明](https://docs.microsoft.com/sql/sql-server/sql-server-privacy)。

## <a name="related-products-and-services"></a>相關產品與服務
### <a name="windows-virtual-machines"></a>Windows 虛擬機器
* [Azure 虛擬機器概觀](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>儲存體
* [Microsoft Azure 儲存體簡介](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>網路功能
* [虛擬網路概觀](../../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 位址](../../../virtual-network/public-ip-addresses.md)
* [在 Azure 入口網站中建立完整格式的網域名稱](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server 文件](https://docs.microsoft.com/sql/index)
* [Azure SQL Database 比較](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>後續步驟

在 Azure 虛擬機器上開始使用 SQL Server：

* [在 Azure 入口網站中建立 SQL Server VM](sql-vm-create-portal-quickstart.md)

獲得有關 SQL Server VM 常見問題的答案：

* [Azure 虛擬機器上的 SQL Server 常見問題集](frequently-asked-questions-faq.md)

查看在 IaaS 中的 SQL Server 上執行多層式應用程式的參考架構

* [Azure 上具有 SQL Server 的 Windows 多層式架構 (N-tier) 應用程式](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [在多個 Azure 區域中執行多層式架構 (N-Tier) 應用程式以獲得高可用性](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
