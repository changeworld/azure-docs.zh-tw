---
title: Azure 中 Windows 虛擬機器上的 SQL Server 常見問題集 | Microsoft Docs
description: 本文章提供在 Azure VM 上執行 SQL Server 的常見問題解答。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: felixwu
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 5e1f61641eed0584ecb5bb33f1a510c7df6e60e3
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839074"
---
# <a name="frequently-asked-questions-for-sql-server-running-on-windows-virtual-machines-in-azure"></a>在 Azure 中 Windows 虛擬機器上執行的 SQL Server 常見問題集

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

本文提供一些最常見關於 [Azure 中 Windows 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 執行問題的解答。

> [!NOTE]
> 本文將重點放在 Windows VM 上 SQL Server 的特定問題。 如果您在 Linux VM 上執行 SQL Server，請參閱 [Linux 常見問題集](../../linux/sql/sql-server-linux-faq.md)。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>圖像

1. **可用的 SQL Server 虛擬機器資源庫映像有哪些？** 

   Azure 會針對所有 Windows 和 Linux 版本上所有支援的 SQL Server 主要版本維護虛擬機器映像。 如需詳細資訊，請參閱完整的 [Windows VM 映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)與 [Linux VM 映像](../../linux/sql/sql-server-linux-virtual-machines-overview.md#create)清單。

1. **現有的 SQL Server 虛擬機器資源庫映像是否已更新？**

   每隔兩個月，系統會以最新的 Windows 和 Linux 更新來更新虛擬機器資源庫中的 SQL Server 映像。 若為 Windows 映像，這包括 Windows Update 中標示為重要的任何更新，其中包含重要的 SQL Server 安全性更新和 Service Pack。 若為 Linux 映像，這包括最新的系統更新。 適用於 Linux 和 Windows 的 SQL Server 累計更新會以不同的方式處理。 針對 Linux，SQL Server 累計更新也會包含在重新整理中。 但此時，系統不會以 SQL Server 或 Windows Server 累計更新來更新 Windows VM。

1. **是否可以從資源庫中移除 SQL Server 虛擬機器映像？**

   可以。 Azure 只會為每個主要版本維護一個映像。 例如，發行新的 SQL Server Service Pack 後，Azure 會將新的映像新增至該 Service Pack 的資源庫。 前一個 Service Pack 的 SQL Server 映像會立即從 Azure 入口網站中移除。 不過，在接下來三個月仍可用於從 PowerShell 佈建。 三個月之後，便無法再使用前一個 Service Pack 映像。 如果 SQL Server 版本在達到其生命週期結尾時就不提供支援，也適用此移除原則。


1. **是否可以部署 Azure 入口網站中看不到 SQL Server 的舊版映射？**

   是，請使用 PowerShell。 如需使用 PowerShell 部署 SQL Server VM 的詳細資訊，請參閱[如何使用 Azure PowerShell 佈建 SQL Server 虛擬機器](virtual-machines-windows-ps-sql-create.md)。
   
1. **是否可以建立 SQL Server VM 的一般化 Azure SQL Server Marketplace 映射，並使用它來部署 Vm？**

   是，但您必須接著向[SQL SERVER VM 資源提供者註冊每個 SQL SERVER vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以在入口網站中管理您的 SQL Server VM，以及利用自動修補和自動備份等功能。 向資源提供者註冊時，您也必須指定每個 SQL Server VM 的授權類型。

1. **如何? 在 Azure VM 上一般化 SQL Server，並使用它來部署新的 Vm 嗎？**

   您可以部署 Windows Server VM （未安裝 SQL Server），並使用[SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep?view=sql-server-ver15)進程，以 SQL Server 安裝媒體將 Azure Vm （Windows）上的 SQL Server 一般化。 具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)的客戶可以從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得其安裝媒體。 沒有軟體保證的客戶可以從具有所需版本的 Marketplace SQL Server VM 映射使用安裝媒體。

   或者，使用 Azure marketplace 的其中一個 SQL Server 影像，將 Azure VM 上的 SQL Server 一般化。 請注意，您必須先刪除來源映射中的下列登錄機碼，才能建立自己的映射。 若未這麼做，可能會導致 SQL Server 安裝程式啟動載入資料夾和/或 SQL IaaS 擴充功能處於失敗狀態的小心。

   登錄機碼路徑：  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > 在 Azure Vm 上 SQL Server （包括從自訂一般化映射部署的虛擬機器），應該向[SQL VM 資源提供者註冊](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-register-with-resource-provider?tabs=azure-cli%2Cbash)，以符合合規性需求，並使用自動修補和自動備份等選用功能。 資源提供者也可讓您指定每個 SQL Server VM[的授權類型](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-ahb?tabs=azure-portal)。

1. **我可以使用自己的 VHD 來部署 SQL Server VM 嗎？**

   是，但您必須接著向[SQL SERVER VM 資源提供者註冊每個 SQL SERVER vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以在入口網站中管理您的 SQL Server VM，以及利用自動修補和自動備份等功能。

1. **是否可以設定虛擬機器資源庫中未顯示的組態 (例如 Windows 2008 R2 + SQL Server 2012)？**

   否。 針對包含 SQL Server 的虛擬機器資源庫映像，您必須透過 Azure 入口網站或 [PowerShell](virtual-machines-windows-ps-sql-create.md) 選取其中一個提供的映像。 不過，您能夠部署 Windows VM，並自行安裝 SQL Server。 接著，您必須向[SQL SERVER VM 資源提供者註冊您的 SQL SERVER vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以在入口網站中管理您的 SQL Server VM，以及利用自動修補和自動備份等功能。 


## <a name="creation"></a>建立

1. **如何使用 SQL Server 建立 Azure 虛擬機器？**

   最簡單的方法是建立包含 SQL Server 的虛擬機器。 如需註冊 Azure 並從入口網站建立 SQL Server VM 的教學課程，請參閱 Azure 入口網站中的布建[SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 您可以選取使用以秒鐘計費 SQL Server 授權的虛擬機器映像，或者您可以使用映像，讓您自備自己的 SQL Server 授權。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 請務必向[SQL SERVER VM 資源提供者註冊您的 SQL SERVER vm](virtual-machines-windows-sql-register-with-resource-provider.md) ，以在入口網站中管理您的 SQL Server VM，以及利用自動修補和自動備份等功能。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如何將內部部署 SQL Server 資料庫移轉至雲端？**

   首先，請使用 SQL Server 執行個體來建立 Azure 虛擬機器。 然後將您的內部部署資料庫移轉至該執行個體。 如需資料移轉策略，請參閱 [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   有三種方式可執行此動作。 如果您是 enterprise 合約（EA）客戶，則可以布建其中一個[支援授權的虛擬機器映射](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)，也就是所謂的自備授權（BYOL）。 如果您有[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)，可以在現有的隨用隨付（PAYG）映射上啟用[Azure Hybrid Benefit](virtual-machines-windows-sql-ahb.md) 。 或者，您可以將 SQL Server 安裝媒體複製到 Windows Server VM，然後在 VM 上安裝 SQL Server。 請務必向[資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)註冊您的 SQL Server VM，以取得入口網站管理、自動備份和自動修補等功能。 

1. **如果是從其中一個隨用隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   可以。 您可以藉由啟用[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)，輕鬆地切換隨用隨付（PAYG）資源庫映射以自備授權（BYOL）。  如需詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)。 此設施目前僅適用于公用和 Azure Government 雲端客戶。

1. **切換授權模型是否必須讓 SQL Server 停機？**

   否。 變更[授權模型](virtual-machines-windows-sql-ahb.md)不需要任何停機時間進行 SQL Server，因為變更會立即生效，且不需要重新開機 VM。 不過，若要向 SQL Server VM 資源提供者註冊您的 SQL Server VM， [Sql iaas 擴充](virtual-machines-windows-sql-server-agent-extension.md)功能是必要條件，而且以_完整_模式安裝 sql iaas 延伸模組會重新開機 SQL Server 服務。 因此，如果需要安裝 SQL IaaS 延伸模組，請以_輕量_模式將它安裝到有限的功能，或在維護期間以_完整_模式安裝。 以_輕量_模式安裝的 SQL IaaS 擴充功能可以隨時升級為_完整_模式，但需要重新開機 SQL Server 服務。 
   
1. **可以在使用傳統模型部署的 SQL Server VM 上切換授權模型嗎？**

   否。 傳統 VM 不支援變更授權模型。 您可以將 VM 遷移至 Azure Resource Manager 模型，並向 SQL Server VM 資源提供者註冊。 一旦向 SQL Server VM 資源提供者註冊 VM 之後，授權模型變更就會在 VM 上提供。

1. **我是否可以使用 Azure 入口網站來管理相同 VM 上的多個實例？**

   否。 入口網站管理是 SQL Server VM 資源提供者所提供的功能，其依賴 SQL Server IaaS 代理程式延伸模組。 因此，相同的限制也適用于延伸模組的資源提供者。 只要已正確設定，入口網站就可以管理一個預設實例或一個已命名的實例。 如需這些限制的詳細資訊，請參閱[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)。 

1. **CSP 訂用帳戶是否能啟用 Azure Hybrid Benefit？**

   是，Azure Hybrid Benefit 適用於 CSP 訂用帳戶。 CSP 客戶應該先部署隨用隨付映像，然後將[授權模式變更](virtual-machines-windows-sql-ahb.md)為自備授權。
   
 
1. **如果只是用於待命/容錯移轉，需要對 Azure VM 上的 SQL Server 授權付費嗎？**

   若要讓待命次要可用性群組或容錯移轉叢集實例具有免費的被動授權，您必須符合[產品授權條款](https://www.microsoft.com/licensing/product-licensing/products)中所述的下列所有準則：

   1. 您具有透過[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)的[授權流動性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被動 SQL Server 實例不會將 SQL Server 資料提供給用戶端，或執行 active SQL Server 工作負載。 它只會用來與主伺服器進行同步處理，否則會以暖待命狀態維護被動資料庫。 如果它正在提供資料，例如向執行中 SQL Server 工作負載的用戶端報告，或除了產品條款中所指定內容以外的其他工作，則必須是付費的授權 SQL Server 實例。 在次要實例上允許下列活動：資料庫一致性檢查或 CheckDB、完整備份、交易記錄備份，以及監視資源使用量資料。 您也可以同時執行主要和對應的嚴重損壞修復實例，以執行每90天的嚴重損壞修復測試。 
   1. Active SQL Server 授權涵蓋于軟體保證中，並允許**一個**被動的次要 SQL Server 實例，與授權作用中伺服器的計算數量相同。 
   1. 次要 SQL Server VM 會利用 Azure 入口網站中的故障[修復](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)授權。
   
1. **什麼是被動實例？**

   被動 SQL Server 實例不會將 SQL Server 資料提供給用戶端，或執行 active SQL Server 工作負載。 它只會用來與主伺服器進行同步處理，否則會以暖待命狀態維護被動資料庫。 如果它正在提供資料，例如向執行中 SQL Server 工作負載的用戶端報告，或除了產品條款中所指定內容以外的其他工作，則必須是付費的授權 SQL Server 實例。 在次要實例上允許下列活動：資料庫一致性檢查或 CheckDB、完整備份、交易記錄備份，以及監視資源使用量資料。 您也可以同時執行主要和對應的嚴重損壞修復實例，以執行每90天的嚴重損壞修復測試。
   

1. **哪些案例可以使用嚴重損壞修復（DR）權益？**

   [授權指南](https://aka.ms/sql2019licenseguide)提供可使用嚴重損壞修復權益的案例。 如需詳細資訊，請參閱您的產品條款，並與您的授權連絡人或客戶經理交談。

1. **哪些訂閱支援嚴重損壞修復（DR）權益？**

   提供軟體保證對等訂用帳戶許可權做為固定權益的完整程式可支援 DR 權益。 這包括。 但不限於開放值（OV）、開放值訂用帳戶（OVS-ES）、Enterprise 合約（EA）、企業訂閱合約（EAS），以及伺服器和雲端註冊（SCE）。 如需詳細資訊，請參閱[產品條款](https://www.microsoft.com/licensing/product-licensing/products)，並與您的授權連絡人或客戶經理交談。 

   
 ## <a name="resource-provider"></a>資源提供者

1. **使用新的 SQL Server VM 資源提供者註冊我的 VM 會帶來額外的成本嗎？**

   否。 SQL Server 的 VM 資源提供者只會針對 Azure VM 上的 SQL Server 啟用額外的管理性，而不需要額外付費。 

1. **是否有適用于所有客戶的 SQL Server VM 資源提供者？**
 
   是的，只要 SQL Server VM 是使用 Resource Manager 模型部署到公用雲端，而不是傳統模型。 所有其他客戶都能夠向新的 SQL Server VM 資源提供者註冊。 不過，只有具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)權益的客戶可以藉由啟用 SQL Server VM 上的[Azure Hybrid Benefit （AHB）](https://azure.microsoft.com/pricing/hybrid-benefit/)來使用自己的授權。 

1. **如果移動或卸載 VM 資源，資源提供者（_microsoft.sqlvirtualmachine_）資源會發生什麼事？** 

   當 Microsoft.Compute/VirtualMachine 資源被捨棄或移動時，系統會通知關聯的 Microsoft.SqlVirtualMachine 資源，讓其以非同步方式複寫作業。

1. **如果資源提供者（_microsoft.sqlvirtualmachine_）資源已卸載，VM 會發生什麼事？**

    當 Microsoft.SqlVirtualMachine 資源被卸除時，Microsoft.Compute/VirtualMachine 資源不會受影響。 不過，授權變更將會還原為預設的原始映像來源。 

1. **是否可以向 SQL Server VM 資源提供者註冊自我部署的 SQL Server Vm？**

    可以。 若您從自己的媒體部署 SQL Server，而且已安裝 SQL IaaS 延伸模組，您可以向資源提供者註冊您自己的 SQL Server VM，以取得 SQL IaaS 延伸模組所提供的管理能力優點。    


## <a name="administration"></a>系統管理

1. **我可以在相同的 VM 上安裝第二個 SQL Server 實例嗎？我可以變更預設實例的已安裝功能嗎？**

   可以。 SQL Server 安裝媒體位於**C**磁片磁碟機上的資料夾中。 從**該位置執行 setup.exe 以**加入新的 SQL Server 實例，或變更電腦上 SQL Server 的其他已安裝功能。 請注意，某些功能（例如自動備份、自動修補和 Azure Key Vault 整合）只會針對預設實例或已正確設定的已命名實例進行操作（請參閱問題3）。 

1. **是否可以將 SQL Server 的預設執行個體解除安裝**

   可以，但有幾點考量。 首先，根據 VM 的授權模型，SQL Server 相關的計費可能會繼續進行。 第二，如先前的答案所述，有一些依賴[SQL Server IaaS 代理程式延伸](virtual-machines-windows-sql-server-agent-extension.md)模組的功能。 如果您在未移除 IaaS 擴充功能的情況下卸載預設實例，延伸模組會繼續尋找預設實例，而且可能會產生事件記錄檔錯誤。 這些錯誤來自下列兩個來源：**Microsoft SQL Server 認證管理**和 **Microsoft SQL Server IaaS 代理程式**。 其中一個錯誤應如下所示：

      和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。

   如果您決定要卸載預設實例，也請同時卸載[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能。 

1. **是否可以搭配 IaaS 擴充功能使用 SQL Server 的已命名實例**？
   
   是，如果命名的實例是 SQL Server 上唯一的實例，而且原始的預設實例已[正確卸載](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)，則為是。 如果沒有預設實例，且單一 SQL Server VM 上有多個已命名的實例，則 SQL Server IaaS 代理程式擴充功能將無法安裝。 

1. **我可以從 SQL Server VM 完全移除 SQL Server 嗎？**

   是，但您會繼續向 SQL Server VM 收費，如[SQL Server Azure vm 的定價指導](virtual-machines-windows-sql-server-pricing-guidance.md)方針中所述。 如果您不再需要 SQL Server，則可以部署新的虛擬機器，並將資料和應用程式移轉到新的虛擬機器。 接著，您可以移除 SQL Server 虛擬機器。
   
## <a name="updating-and-patching"></a>更新和修補

1. **如何將 Azure VM 中的 SQL Server 變更為不同版本？**

   客戶可以使用包含所需 SQL Server 版本或版次的安裝媒體來變更 SQL Server 的版本/版次。 變更版本之後，請使用 Azure 入口網站來修改 VM 的版本屬性，以精確反映 VM 的計費。 如需詳細資訊，請參閱[SQL SERVER VM 的變更版本](virtual-machines-windows-sql-change-edition.md)。 不同版本的 SQL Server 沒有任何計費差異，因此一旦變更 SQL Server 版本後，就不需要採取進一步的動作。

1. **我可以在哪裡取得安裝媒體，以變更 SQL Server 的版本？**

   具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可以從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)取得其安裝媒體。 沒有軟體保證的客戶可以從具有所需版本的 Marketplace SQL Server VM 映射使用安裝媒體。
   
1. **如何將更新和 Service Pack 套用到 SQL Server VM 上？**

   虛擬機器可讓您控制主機電腦，包括套用更新的時間與方法。 針對作業系統，您可以手動套用 Windows 更新，或是啟用名為 [自動修補](virtual-machines-windows-sql-automated-patching.md)的排程服務。 自動修補會安裝任何標示為重要的更新，包括該類別目錄中的 SQL Server 更新。 其他選擇性的 SQL Server 更新，則必須手動安裝。

1. **我可以在向 SQL Server VM 資源提供者註冊之後，升級我的 SQL Server 2008/2008 R2 實例嗎？**

   可以。 您可以使用任何安裝媒體來升級 SQL Server 的版本，然後將您的[SQL IaaS 延伸模組模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)從_無代理程式_升級至_完整_版。 這麼做可讓您存取 SQL IaaS 擴充功能的所有優點，例如入口網站管理性、自動備份和自動修補。 

1. **如何取得 SQL Server 2008 和 SQL Server 2008 R2 實例之支援的免費擴充安全性更新？**

   您可以將您的 SQL Server 依現有方式移至 Azure SQL 虛擬機器，以取得[免費的擴充安全性更新](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 如需詳細資訊，請參閱[支援選項結束](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
  
   

## <a name="general"></a>一般

1. **Azure Vm 上是否支援 SQL Server 容錯移轉叢集實例（FCI）？**

   可以。 您可以針對儲存子系統使用[premium 檔案共用（PFS）](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)或[儲存空間直接存取（S2D）](virtual-machines-windows-portal-sql-create-failover-cluster.md)來安裝容錯移轉叢集實例。 Premium 檔案共用提供 IOPS 和輸送量容量，以符合許多工作負載的需求。 針對需要大量 IO 的工作負載，請考慮使用以受控 premium 或 ultra 為基礎的儲存空間直接存取。 或者，您可以使用 [Azure 虛擬機器中的 SQL Server 的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述的協力廠商叢集或儲存體解決方案。

   > [!IMPORTANT]
   > 目前，Azure 上的 SQL Server FCI 不支援_完整_的[SQL Server IaaS 代理程式擴充](virtual-machines-windows-sql-server-agent-extension.md)功能。 建議您從參與 FCI 的 Vm 卸載_完整_的延伸模組，並改為在_輕量_模式中安裝延伸模組。 此延伸模組支援一些功能，例如自動備份和修補，以及一些適用于 SQL Server 的入口網站功能。 卸載_完整_代理程式之後，這些功能將無法在 SQL Server vm 上使用。

1. **SQL Server Vm 和 SQL Database 服務之間有何差異？**

   從概念上來說，在 Azure 虛擬機器上執行 SQL Server 與在遠端資料中心中執行 SQL Server 並沒什麼不同。 對比之下， [SQL Database](../../../sql-database/sql-database-technical-overview.md) 可提供資料庫即服務的功能。 使用 SQL Database 時，您無法存取主控資料庫的機器。 如需完整的比較，請參閱 [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何在 Azure VM 上安裝 SQL 資料工具？**

    請從 [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313) 下載並安裝 SQL 資料工具。

1. **SQL Server Vm 上是否支援具有 MSDTC 的分散式交易？**
   
    可以。 SQL Server 2016 SP2 和更新版本支援本機 DTC。 不過，使用 Always On 可用性群組時，必須測試應用程式，因為容錯移轉期間進行中的交易將會失敗，而且必須重試。 從 Windows Server 2019 開始提供叢集 DTC。 

## <a name="resources"></a>資源

**Windows vm**：

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器中 SQL Server 的高可用性和嚴重損壞修復](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux vm**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 上的 SQL Server 檔](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
