---
title: Azure 中 Windows 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs
description: 本文章提供在 Azure VM 上執行 SQL Server 的常見問題解答。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: f382e3cf0f5d2d60c2868c6698b1ea901fbac023
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121437"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure Vm 上 SQL Server 的常見問題
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

本文提供有關在[Windows Azure 虛擬機器 (vm) 上執行 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)的一些常見問題的解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> 映像

1. **可用的 SQL Server 虛擬機器資源庫映像有哪些？** 

   Azure 會針對所有 Windows 和 Linux 版本上所有支援的 SQL Server 主要版本維護虛擬機器映像。 如需詳細資訊，請參閱完整的 [Windows VM 映像](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)與 [Linux VM 映像](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)清單。

1. **現有的 SQL Server 虛擬機器資源庫映像是否已更新？**

   每隔兩個月，系統會以最新的 Windows 和 Linux 更新來更新虛擬機器資源庫中的 SQL Server 映像。 若為 Windows 映像，這包括 Windows Update 中標示為重要的任何更新，其中包含重要的 SQL Server 安全性更新和 Service Pack。 若為 Linux 映像，這包括最新的系統更新。 適用於 Linux 和 Windows 的 SQL Server 累計更新會以不同的方式處理。 針對 Linux，SQL Server 累計更新也會包含在重新整理中。 但此時，系統不會以 SQL Server 或 Windows Server 累計更新來更新 Windows VM。

1. **是否可以從資源庫中移除 SQL Server 虛擬機器映像？**

   是。 Azure 只會為每個主要版本維護一個映像。 例如，發行新的 SQL Server Service Pack 後，Azure 會將新的映像新增至該 Service Pack 的資源庫。 前一個 Service Pack 的 SQL Server 映像會立即從 Azure 入口網站中移除。 不過，在接下來三個月仍可用於從 PowerShell 佈建。 三個月之後，便無法再使用前一個 Service Pack 映像。 如果 SQL Server 版本在達到其生命週期結尾時就不提供支援，也適用此移除原則。


1. **較舊而未顯示在 Azure 入口網站中的 SQL Server 映像是否可供部署？**

   是，請使用 PowerShell。 如需使用 PowerShell 部署 SQL Server VM 的詳細資訊，請參閱[如何使用 Azure PowerShell 佈建 SQL Server 虛擬機器](create-sql-vm-powershell.md)。
   
1. **是否可以建立 SQL Server VM 的一般化 Azure Marketplace SQL Server 映射，並使用它來部署 Vm？**

   是，但您必須[將每個 SQL Server VM 註冊到 SQL Server VM 資源提供者](sql-vm-resource-provider-register.md)，才能在入口網站中管理 SQL Server VM，以及利用自動修補和自動備份等功能。 註冊到資源提供者時，還必須指定每個 SQL Server VM 的授權類型。

1. **如何在 Azure VM 上將 SQL Server 一般化，並使用其來部署新的 VM？**

   您可部署 Windows Server VM (其上未安裝 SQL Server) 並使用 [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15) 處理序，以使用 SQL Server 安裝媒體將 Azure VM (Windows) 上的 SQL Server 一般化。 具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)的客戶可從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得安裝媒體。 沒有軟體保證的客戶可以從具有所需版本的 Azure Marketplace SQL Server VM 映射使用安裝媒體。

   或者，使用 Azure Marketplace 的其中一個 SQL Server 映射，將 Azure VM 上的 SQL Server 一般化。 請注意，您必須先刪除來源映像中的下列登錄機碼，才能建立自己的映像。 若未這麼做，可能會導致 SQL Server 安裝程式啟動程序資料夾膨脹和/或 SQL IaaS 延伸模組處於失敗狀態。

   登錄機碼路徑：  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Azure VM (包括從自訂一般化映像部署的 VM) 上的 SQL Server 應該[註冊到 SQL VM 資源提供者](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)，以符合合規性需求，並利用自動修補和自動備份等選擇性功能。 資源提供者還可供為每部 SQL Server VM [指定授權類型](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal)。

1. **是否可使用自己的 VHD 來部署 SQL Server VM？**

   是，但必須[將每部 SQL Server VM 註冊到 SQL Server VM 資源提供者](sql-vm-resource-provider-register.md)，才能在入口網站中管理 SQL Server VM，以及利用自動修補和自動備份等功能。

1. **是否可以設定虛擬機器資源庫中未顯示的設定 (例如 Windows 2008 R2 + SQL Server 2012) ？**

   否。 針對包含 SQL Server 的虛擬機器資源庫映像，您必須透過 Azure 入口網站或 [PowerShell](create-sql-vm-powershell.md) 選取其中一個提供的映像。 不過，您能夠在其上部署 Windows VM，並自行安裝 SQL Server。 接著，您必須向[SQL SERVER VM 資源提供者註冊您的 SQL SERVER vm](sql-vm-resource-provider-register.md) ，以在 Azure 入口網站中管理您的 SQL Server VM，以及利用自動修補和自動備份等功能。 


## <a name="creation"></a>建立

1. **如何使用 SQL Server 建立 Azure 虛擬機器？**

   最簡單的方法是建立包含 SQL Server 的虛擬機器。 如需註冊 Azure 並從入口網站建立 SQL Server VM 的教學課程，請參閱[在 Azure 入口網站中佈建 SQL Server 虛擬機器](create-sql-vm-portal.md)。 您可以選取使用以秒鐘計費 SQL Server 授權的虛擬機器映像，或者您可以使用映像，讓您自備自己的 SQL Server 授權。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 請務必[將 SQL Server VM 註冊到 SQL Server VM 資源提供者](sql-vm-resource-provider-register.md)，才能在入口網站中管理 SQL Server VM，以及利用自動修補和自動備份等功能。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](pricing-guidance.md)。

1. **如何將內部部署 SQL Server 資料庫移轉至雲端？**

   首先，請使用 SQL Server 執行個體來建立 Azure 虛擬機器。 然後將您的內部部署資料庫移轉至該執行個體。 如需資料移轉策略，請參閱 [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](migrate-to-vm-from-sql-server.md)。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   有三種方式可以執行此動作。 如果您是 Enterprise 合約 (EA) 客戶，您可以布建其中一個[支援授權的虛擬機器映射](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)，這也稱為「自備授權 (BYOL) 。 如果您有[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)，可以在現有的隨用隨付 (PAYG) 映射上啟用[Azure Hybrid Benefit](licensing-model-azure-hybrid-benefit-ahb-change.md) 。 或者，您可以將 SQL Server 安裝媒體複製到 Windows Server VM 上，然後在 VM 上安裝 SQL Server。 請務必將 SQL Server VM 註冊到[資源提供者](sql-vm-resource-provider-register.md)，以取得入口網站管理、自動備份和自動修補等功能。 

1. **如果是從其中一個隨用隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   是。 只要啟用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)，即可輕鬆地將隨用隨付 (PAYG) 資源庫映像切換至自備授權 (BYOL)。  如需詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](licensing-model-azure-hybrid-benefit-ahb-change.md)。 此機能目前僅適用於公用和 Azure Government 雲端客戶。

1. **切換授權模型是否必須讓 SQL Server 停機？**

   否。 [變更授權模型](licensing-model-azure-hybrid-benefit-ahb-change.md)不需要將 SQL Server 停機，因為變更會立即生效，且不需要重新啟動 VM。 不過，若要將 SQL Server VM 註冊到 SQL Server VM 資源提供者，[SQL IaaS 延伸模組](sql-server-iaas-agent-extension-automate-management.md)是先決條件，且以「完整」模式安裝 SQL IaaS 延伸模組後，會重新啟動 SQL Server 服務。 因此，如果需要安裝 SQL IaaS 延伸模組，請以有限功能的「輕量」模式加以安裝，或在維護期間以「完整」模式安裝。 以「輕量」模式安裝的 SQL IaaS 延伸模組可隨時升級為「完整」模式，但需要重新啟動 SQL Server 服務。 
   
1. **可以在使用傳統模型部署的 SQL Server VM 上切換授權模型嗎？**

   否。 傳統 VM 不支援變更授權模型。 您可以將 VM 遷移至 Azure Resource Manager 模型，並向 SQL Server VM 資源提供者註冊。 向 SQL Server VM 資源提供者註冊 VM 後，授權模型變更就會在 VM 上生效。

1. **是否可以使用 Azure 入口網站來管理相同 VM 上的多個執行個體？**

   否。 入口網站管理是 SQL Server VM 資源提供者所提供的一項功能，需依賴 SQL Server IaaS 代理程式擴充功能。 因此，資源提供者與擴充功能會受到相同的限制。 入口網站可以僅管理一個預設執行個體，或管理一個指定的執行個體，只要已正確設定即可。 如需這些限制的詳細資訊，請參閱 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)。 

1. **CSP 訂用帳戶是否能啟用 Azure Hybrid Benefit？**

   是，Azure Hybrid Benefit 適用於 CSP 訂用帳戶。 CSP 客戶應該先部署隨用隨付映像，然後將[授權模式變更](licensing-model-azure-hybrid-benefit-ahb-change.md)為自備授權。
   
 
1. **如果只是用於待命/容錯移轉，需要對 Azure VM 上的 SQL Server 授權付費嗎？**

   若要有待命次要可用性群組或容錯移轉叢集執行個體的免費被動授權，則必須符合[產品授權條款](https://www.microsoft.com/licensing/product-licensing/products)中所述的下列所有準則：

   1. 您已透過[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)取得[授權行動性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被動 SQL Server 執行個體不會將 SQL Server 資料提供給用戶端，或執行主動 SQL Server 工作負載。 此執行個體只會用來與主要伺服器進行同步，或是維護處於暖待命狀態的被動資料庫。 如果此執行個體提供資料 (例如，向執行主動 SQL Server 工作負載的用戶端報告)，或執行產品條款中所指定內容以外的其他工作，則必須是付費的授權 SQL Server 執行個體。 次要執行個體上允許下列活動：資料庫一致性檢查或 CheckDB、完整備份、交易記錄備份，以及監視資源使用量資料。 您也可以在每 90 天一次的短期災害復原測試期間內，同時執行主要和對應的災害復原執行個體。 
   1. 主動 SQL Server 授權涵蓋於軟體保證中，可允許**一個**被動次要 SQL Server 執行個體，且計算數量最多只能與授權的主動伺服器相同。 
   1. 次要 SQL Server VM 會利用 Azure 入口網站中的[災害復原](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)授權。
   
1. **什麼是被動執行個體？**

   被動 SQL Server 執行個體不會將 SQL Server 資料提供給用戶端，或執行主動 SQL Server 工作負載。 此執行個體只會用來與主要伺服器進行同步，或是維護處於暖待命狀態的被動資料庫。 如果此執行個體提供資料 (例如，向執行主動 SQL Server 工作負載的用戶端報告)，或執行產品條款中所指定內容以外的其他工作，則必須是付費的授權 SQL Server 執行個體。 次要執行個體上允許下列活動：資料庫一致性檢查或 CheckDB、完整備份、交易記錄備份，以及監視資源使用量資料。 您也可以在每 90 天一次的短期災害復原測試期間內，同時執行主要和對應的災害復原執行個體。
   

1. **哪些案例可以使用災害復原 (DR) 權益？**

   [授權指南](https://aka.ms/sql2019licenseguide)說明可在哪些案例中使用災害復原權益。 如需詳細資訊，請參閱您的產品條款，並洽詢您的授權連絡人或帳戶管理員。

1. **哪些訂用帳戶支援災害復原 (DR) 權益？**

   提供軟體保證同等訂用帳戶權限作為固定權益的完整方案，可支援 DR 權益。 這包括 但不限於開放值 (OV) 、Open Value 訂用帳戶 (OVS-ES) 、Enterprise 合約 (EA) 、Enterprise 合約訂閱 (EAS) ，以及伺服器和雲端註冊 (SCE) 。 如需詳細資訊，請參閱[產品條款](https://www.microsoft.com/licensing/product-licensing/products)，並洽詢授權連絡人或帳戶管理員。 

   
 ## <a name="resource-provider"></a>資源提供者

1. **將 VM 註冊到新的 SQL Server VM 資源提供者是否會產生額外成本？**

   否。 SQL Server VM 資源提供者只會為 Azure VM 上的 SQL Server 啟用額外管理能力，而不會產生任何額外費用。 

1. **SQL Server VM 資源提供者是否適用於所有客戶？**
 
   是，只要 SQL Server VM 是使用 Resource Manager 模型 (而不是傳統模型) 部署到公用雲端即可。 所有其他客戶都能註冊到新的 SQL Server VM 資源提供者。 不過，只有具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)權益的客戶可在 SQL Server VM 上啟用 [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) 來使用自己的授權。 

1. **若移動或捨棄 VM 資源，則資源提供者 (_Microsoft.SqlVirtualMachine_) 資源會發生什麼事？** 

   當 Microsoft.Compute/VirtualMachine 資源被捨棄或移動時，系統會通知關聯的 Microsoft.SqlVirtualMachine 資源，讓其以非同步方式複寫作業。

1. **若捨棄資源提供者 (_Microsoft.SqlVirtualMachine_) 資源，則 VM 會發生什麼事？**

    當 Microsoft.SqlVirtualMachine 資源被卸除時，Microsoft.Compute/VirtualMachine 資源不會受影響。 不過，授權變更將會還原為預設的原始映像來源。 

1. **是否可將自我部署 SQL Server VM 註冊到 SQL Server VM 資源提供者？**

    是。 若您從自己的媒體部署 SQL Server，而且已安裝 SQL IaaS 延伸模組，您可以向資源提供者註冊您自己的 SQL Server VM，以取得 SQL IaaS 延伸模組所提供的管理能力優點。    


## <a name="administration"></a>系統管理

1. **是否可以在相同的 VM 上安裝第二個 SQL Server 執行個體？是否可以變更預設執行個體的已安裝功能？**

   是。 SQL Server 安裝媒體位於 **C** 磁碟機的資料夾中。 您可從該位置執行 **Setup.exe** 來新增新的 SQL Server 執行個體，或變更機器上 SQL Server 的其他已安裝功能。 請注意，某些功能 (例如自動備份、自動修補和 Azure Key Vault 整合) 只能在預設執行個體或正確設定的具名執行個體上運作 (請參閱問題 3)。 透過 Azure Hybrid Benefit 或**隨用隨付**授權模型使用[軟體保證](licensing-model-azure-hybrid-benefit-ahb-change.md)的客戶，可以在虛擬機器上安裝多個 SQL Server 實例，而不會產生額外的授權成本。 除非正確設定，否則其他 SQL Server 實例可能會消耗系統資源。 

1. **是否可以將 SQL Server 的預設執行個體解除安裝**

   可以，但有幾點考量。 首先，根據 VM 的授權模型，可能會繼續產生與 SQL Server 相關聯的費用。 其次，如前一個回答中所述，有一些功能依賴 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)。 如果您還在未移除 IaaS 延伸模組的情況下解除安裝預設執行個體，則此延伸模組會繼續尋找預設執行個體，且可能會產生事件記錄錯誤。 這些錯誤來自下列兩個來源：**Microsoft SQL Server 認證管理**和 **Microsoft SQL Server IaaS 代理程式**。 其中一個錯誤應如下所示：

      和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。

   如果您決定要將預設執行個體解除安裝，則也會將 [SQL Server IaaS 代理程式擴充功能](sql-server-iaas-agent-extension-automate-management.md)解除安裝。 

1. **是否可將 SQL Server 的具名執行個體與 IaaS 延伸模組搭配使用？**
   
   是，如果具名執行個體是 SQL Server 上唯一的執行個體，且原始的預設執行個體[已適當地解除安裝](sql-server-iaas-agent-extension-automate-management.md#install-on-a-vm-with-a-single-named-sql-server-instance)。 如果沒有預設執行個體，且單一 SQL Server VM 上有多個具名執行個體，則 SQL Server IaaS 代理程式延伸模組將無法安裝。 

1. **可以從 SQL Server VM 完全移除 SQL Server 嗎？**

   可以，不過如 [SQL Server Azure VM 的定價指引](pricing-guidance.md) 中所述，您仍需繼續支付 SQL Server VM 的費用。 如果您不再需要 SQL Server，則可以部署新的虛擬機器，並將資料和應用程式移轉到新的虛擬機器。 接著，您可以移除 SQL Server 虛擬機器。
   
## <a name="updating-and-patching"></a>更新和修補

1. **如何? 在 Azure VM 中變更為不同版本的 SQL Server 嗎？**

   客戶可以使用包含所需 SQL Server 版本或版次的安裝媒體來變更 SQL Server 的版本/版次。 變更版本之後，請使用 Azure 入口網站來修改 VM 的版本屬性，以精確反映 VM 的計費。 如需詳細資訊，請參閱 [SQL Server VM 的變更版本](change-sql-server-edition.md)。 不同版本的 SQL Server 的計費均無差異，因此一旦變更 SQL Server 版本，就不需要採取進一步的動作。

1. **我可以在哪裡取得安裝媒體，以變更 SQL Server 的版本？**

   具備[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得安裝媒體。 沒有軟體保證的客戶可以從具有所需版本的 Azure Marketplace SQL Server VM 映射使用安裝媒體。
   
1. **如何將更新和 Service Pack 套用到 SQL Server VM 上？**

   虛擬機器可讓您控制主機電腦，包括套用更新的時間與方法。 針對作業系統，您可以手動套用 Windows 更新，或是啟用名為 [自動修補](automated-patching.md)的排程服務。 自動修補會安裝任何標示為重要的更新，包括該類別目錄中的 SQL Server 更新。 其他選擇性的 SQL Server 更新，則必須手動安裝。

1. **是否可在 SQL Server 2008/2008 R2 執行個體註冊到 SQL Server VM 資源提供者之後，將其升級？**

   是。 您可使用任何安裝媒體來升級 SQL Server 的版本與版次，然後將 [SQL IaaS 延伸模組模式](sql-vm-resource-provider-register.md#management-modes)從「無代理程式」升級為「完整」。 這麼做可供利用 SQL IaaS 延伸模組的所有優點，例如入口網站管理能力、自動備份和自動修補。 

1. **如何取得不再提供支援的 SQL Server 2008 和 SQL Server 2008 R2 執行個體的免費擴充安全性更新？**

   您可以將您的 SQL Server 依現有方式移至 Azure 虛擬機器，以取得[免費的擴充安全性更新](sql-server-2008-extend-end-of-support.md)。 如需詳細資訊，請參閱[終止支援選項](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
  
   

## <a name="general"></a>一般

1. **Azure VM 上是否支援 SQL Server 容錯移轉叢集執行個體 (FCI)？**

   是。 您可使用[進階檔案共用 (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) 或[儲存空間直接存取 (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md)，為儲存子系統安裝容錯移轉叢集執行個體。 進階檔案共用提供 IOPS 和輸送量容量，以符合許多工作負載的需求。 針對需要大量 IO 的工作負載，請考慮使用以受控高階磁碟或 Ultra 磁碟為基礎的儲存空間直接存取。 或者，您可以使用[Azure 虛擬機器上 SQL Server 的高可用性和嚴重損壞修復](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)中所述的協力廠商叢集或儲存體解決方案。

   > [!IMPORTANT]
   > 目前不支援將「完整」 [SQL Server IaaS 代理程式延伸模組](sql-server-iaas-agent-extension-automate-management.md)用於 Azure 上的 SQL Server FCI。 我們建議從參與 FCI 的 VM 解除安裝「完整」延伸模組，並改以「輕量」模式安裝延伸模組。 針對 SQL Server，此延伸模組支援自動備份和修補之類的功能，以及部分入口網站功能。 在「完整」代理程式解除安裝後，這些功能即不適用於 SQL Server VM。

1. **SQL Server VM 和 SQL Database 服務之間的差異為何？**

   從概念上來說，在 Azure 虛擬機器上執行 SQL Server 與在遠端資料中心中執行 SQL Server 並沒什麼不同。 相反地， [Azure SQL Database](../../database/sql-database-paas-overview.md)提供資料庫即服務。 使用 SQL Database 時，您無法存取主控資料庫的機器。 如需完整的比較，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md) 上的 SQL Server。

1. **如何在 Azure VM 上安裝 SQL 資料工具？**

    請從 [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313) 下載並安裝 SQL 資料工具。

1. **SQL Server VM 是否支援使用 MSDTC 的分散式交易？**
   
    是。 SQL Server 2016 SP2 和更高版本支援本機 DTC。 不過，使用 Always On 可用性群組時，必須測試應用程式，因為容錯移轉期間進行中的交易將會失敗，且必須重試。 叢集 DTC 則從 Windows Server 2019 開始提供。 

## <a name="resources"></a>資源

**Windows VM**：

* [Windows VM 上的 SQL Server 概觀](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上布建 SQL Server](create-sql-vm-portal.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虛擬機器上 SQL Server 的高可用性和嚴重損壞修復](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虛擬機器上 SQL Server 的應用程式模式和開發策略](application-patterns-development-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux VM 上布建 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常見問題集 (Linux)](../linux/frequently-asked-questions-faq.md)
* [Linux 上的 SQL Server 文件](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) \(機器翻譯\)
