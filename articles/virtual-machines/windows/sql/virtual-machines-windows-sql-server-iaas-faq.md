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
ms.openlocfilehash: 3b73c329c3db54ba78db15ced8e919af4d4a45d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249733"
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

   是。 Azure 只會為每個主要版本維護一個映像。 例如，發行新的 SQL Server Service Pack 後，Azure 會將新的映像新增至該 Service Pack 的資源庫。 前一個 Service Pack 的 SQL Server 映像會立即從 Azure 入口網站中移除。 不過，在接下來三個月仍可用於從 PowerShell 佈建。 三個月之後，便無法再使用前一個 Service Pack 映像。 如果 SQL Server 版本在達到其生命週期結尾時就不提供支援，也適用此移除原則。


1. **是否可以部署 Azure 門戶中不可見的 SQL Server 的舊映射？**

   是，請使用 PowerShell。 如需使用 PowerShell 部署 SQL Server VM 的詳細資訊，請參閱[如何使用 Azure PowerShell 佈建 SQL Server 虛擬機器](virtual-machines-windows-ps-sql-create.md)。

1. **是否可以創建 SQL Server VM 的通用 Azure SQL Server 應用商店映射，並用它來部署 VM？**

   是，但隨後您必須[將每個 SQL Server VM 註冊到 SQL Server VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)，以便在門戶中管理 SQL Server VM，以及利用自動修補和自動備份等功能。 在資來源提供者註冊時，還需要為每個 SQL Server VM 指定許可證類型。 

1. **我可以使用自己的 VHD 部署 SQL Server VM 嗎？**

   是，但隨後您必須[將每個 SQL Server VM 註冊到 SQL Server VM 資來源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)，以便在門戶中管理 SQL Server VM，以及利用自動修補和自動備份等功能。

1. **是否可以設定虛擬機器資源庫中未顯示的組態 (例如 Windows 2008 R2 + SQL Server 2012)？**

   否。 針對包含 SQL Server 的虛擬機器資源庫映像，您必須透過 Azure 入口網站或 [PowerShell](virtual-machines-windows-ps-sql-create.md) 選取其中一個提供的映像。 但是，您可以部署 Windows VM 並自行安裝 SQL Server。 然後，您必須[向 SQL Server VM 資來源提供者註冊 SQL Server VM，](virtual-machines-windows-sql-register-with-resource-provider.md)以便在門戶中管理 SQL Server VM，以及利用自動修補和自動備份等功能。 


## <a name="creation"></a>建立

1. **如何使用 SQL Server 建立 Azure 虛擬機器？**

   最簡單的方法是創建包含 SQL Server 的虛擬機器。 有關註冊 Azure 和從門戶創建 SQL Server VM 的教程，請參閱[在 Azure 門戶中預配 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 您可以選取使用以秒鐘計費 SQL Server 授權的虛擬機器映像，或者您可以使用映像，讓您自備自己的 SQL Server 授權。 您也可以選擇使用免費授權版本 (Developer 或 Express) 或重複使用內部部署授權，在 VM 上手動安裝 SQL Server。 請務必向[SQL Server VM 資來源提供者註冊 SQL Server VM，](virtual-machines-windows-sql-register-with-resource-provider.md)以便在門戶中管理 SQL Server VM，以及利用自動修補和自動備份等功能。 如果您自備授權，必須具備 [Azure 上透過軟體保證的授權流動性](https://azure.microsoft.com/pricing/license-mobility/)。 如需詳細資訊，請參閱 [SQL Server Azure VM 的定價指導方針](virtual-machines-windows-sql-server-pricing-guidance.md)。

1. **如何將內部部署 SQL Server 資料庫移轉至雲端？**

   首先，請使用 SQL Server 執行個體來建立 Azure 虛擬機器。 然後將您的內部部署資料庫移轉至該執行個體。 如需資料移轉策略，請參閱 [將 SQL Server 資料庫移轉至 Azure VM 中的 SQL Server](virtual-machines-windows-migrate-sql.md)。

## <a name="licensing"></a>授權

1. **如何在 Azure VM 上安裝 SQL Server 授權版本？**

   有三種方法可以做到這一點。 如果您是企業協定 （EA） 客戶，則可以預配[支援許可證的虛擬機器映射](virtual-machines-windows-sql-server-iaas-overview.md#BYOL)之一，也稱為自帶許可證 （BYOL）。 如果您有[軟體保證](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)，則可以在現有即用即付 （PAYG） 映射上啟用[Azure 混合權益](virtual-machines-windows-sql-ahb.md)。 或者，您可以將 SQL Server 安裝介質複製到 Windows 伺服器 VM，然後在 VM 上安裝 SQL Server。 請務必向[資源供應商](virtual-machines-windows-sql-register-with-resource-provider.md)註冊 SQL Server VM，以訪問門戶管理、自動備份和自動修補等功能。 

1. **如果是從其中一個隨用隨付資源庫映像建立，可以將 VM 變更為使用自己的 SQL Server 授權嗎？**

   是。 通過啟用[Azure 混合權益](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)，可以輕鬆地切換即用即付 （PAYG） 庫映射，以自帶許可證 （BYOL）。  如需詳細資訊，請參閱[如何變更 SQL Server VM 的授權模式](virtual-machines-windows-sql-ahb.md)。 目前，此功能僅提供給公用雲端客戶。

1. **切換授權模型是否必須讓 SQL Server 停機？**

   否。 [更改許可模型](virtual-machines-windows-sql-ahb.md)不需要 SQL Server 的任何停機時間，因為更改立即生效，並且不需要重新開機 VM。 但是，要將 SQL Server VM 註冊到 SQL Server VM 資來源提供者[，SQL IaaS 擴展](virtual-machines-windows-sql-server-agent-extension.md)是先決條件，_在全_模式下安裝 SQL IaaS 擴展將重新開機 SQL Server 服務。 因此，如果需要安裝 SQL IaaS 擴展，請將其安裝在_輕型_模式下，以獲得有限的功能，或者在維護時段內以_全_模式安裝。 在_羽量級_模式下安裝的 SQL IaaS 擴展可以隨時升級到_完全_模式，但需要重新開機 SQL Server 服務。 
   
1. **是否可以切換使用經典模型部署的 SQL Server VM 上的許可模型？**

   否。 經典 VM 不支援更改許可模型。 您可以將 VM 遷移到 Azure 資源管理器模型，然後向 SQL Server VM 資來源提供者註冊。 向 SQL Server VM 資來源提供者註冊 VM 後，VM 上將可用許可模型更改。

1. **是否可以使用 Azure 門戶管理同一 VM 上的多個實例？**

   否。 門戶管理是 SQL Server VM 資來源提供者提供的一項功能，它依賴于 SQL Server IaaS 代理擴展。 因此，與擴展一樣，資來源提供者也適用相同的限制。 門戶只能管理一個預設實例，也可以管理一個命名實例，只要它配置正確。 有關這些限制的詳細資訊，請參閱 SQL [Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)。 

1. **CSP 訂用帳戶是否能啟用 Azure Hybrid Benefit？**

   是，Azure Hybrid Benefit 適用於 CSP 訂用帳戶。 CSP 客戶應該先部署隨用隨付映像，然後將[授權模式變更](virtual-machines-windows-sql-ahb.md)為自備授權。
   
 
1. **如果只是用於待命/容錯移轉，需要對 Azure VM 上的 SQL Server 授權付費嗎？**

   要為備用輔助可用性組或容錯移轉叢集實例提供免費被動許可證，必須滿足[產品授權條款](https://www.microsoft.com/licensing/product-licensing/products)概述的所有以下條件：

   1. 您可以通過[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)[獲得許可證移動性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被動 SQL Server 實例不向用戶端提供 SQL Server 資料或運行活動 SQL Server 工作負荷。 它僅用於與主伺服器同步，否則將被動資料庫保持在暖備用狀態。 如果它提供資料（例如向運行活動 SQL Server 工作負荷的用戶端報告）或執行產品術語中指定的任何工作以外的任何工作，則它必須是付費許可的 SQL Server 實例。 輔助實例允許以下活動：資料庫一致性檢查或 CheckDB、完整備份、事務記錄備份和監視資源使用方式資料。 您還可以在 90 天內同時運行主災害復原實例，進行短暫的災害復原測試。 
   1. 軟體保證涵蓋活動 SQL Server 許可證，並允許**使用一個**被動輔助 SQL Server 實例，其計算量僅與許可的活動伺服器相同。 
   1. 輔助 SQL Server VM 利用 Azure 門戶中的[災害復原](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)許可證。
   
1. **什麼是被動實例？**

   被動 SQL Server 實例不向用戶端提供 SQL Server 資料或運行活動 SQL Server 工作負荷。 它僅用於與主伺服器同步，否則將被動資料庫保持在暖備用狀態。 如果它提供資料（例如向運行活動 SQL Server 工作負荷的用戶端報告）或執行產品術語中指定的任何工作以外的任何工作，則它必須是付費許可的 SQL Server 實例。 輔助實例允許以下活動：資料庫一致性檢查或 CheckDB、完整備份、事務記錄備份和監視資源使用方式資料。 您還可以在 90 天內同時運行主災害復原實例，進行短暫的災害復原測試。
   

1. **哪些方案可以利用"厭惡者恢復 （DR）"好處？**

   [許可指南](https://aka.ms/sql2019licenseguide)提供了可以使用災害復原收益的方案。 有關詳細資訊，請參閱您的產品條款並與您的許可連絡人或客戶經理聯繫。

1. **哪些訂閱支援災害復原 （DR） 權益？**

   提供軟體保證等價訂閱許可權作為固定權益的綜合計畫支援 DR 權益。 這包括。 但不限於開放值 （OV）、開放價值訂閱 （OVS）、企業協定 （EA）、企業訂閱協定 （EAS） 以及伺服器和雲註冊 （SCE）。 有關詳細資訊，請參閱[產品條款](https://www.microsoft.com/licensing/product-licensing/products)並與您的許可連絡人或 acocunt 經理聯繫。 

   
 ## <a name="resource-provider"></a>資源提供者

1. **向新的 SQL Server VM 資來源提供者註冊 VM 是否會帶來額外費用？**

   否。 SQL Server VM 資來源提供者只是支援 Azure VM 上的 SQL Server 的其他可管理性，無需支付額外費用。 

1. **SQL Server VM 資來源提供者是否適用于所有客戶？**
 
   是的，只要 SQL Server VM 使用資源管理器模型部署在公共雲上，而不是使用經典模型。 所有其他客戶都能夠向新的 SQL Server VM 資來源提供者註冊。 但是，只有具有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)權益的客戶才能通過在 SQL Server VM 上啟動[Azure 混合權益 （AHB）](https://azure.microsoft.com/pricing/hybrid-benefit/)來使用自己的許可證。 

1. **如果移動或刪除 VM 資源，資來源提供者 （_Microsoft.SqlVirtualMachine_） 資源會發生什麼情況？** 

   當 Microsoft.Compute/VirtualMachine 資源被捨棄或移動時，系統會通知關聯的 Microsoft.SqlVirtualMachine 資源，讓其以非同步方式複寫作業。

1. **如果資來源提供者 （_Microsoft.SqlVirtual電腦_） 資源被丟棄，VM 會發生什麼情況？**

    當 Microsoft.SqlVirtualMachine 資源被卸除時，Microsoft.Compute/VirtualMachine 資源不會受影響。 不過，授權變更將會還原為預設的原始映像來源。 

1. **是否可以向 SQL Server VM 資來源提供者註冊自部署的 SQL Server VM VM？**

    是。 若您從自己的媒體部署 SQL Server，而且已安裝 SQL IaaS 延伸模組，您可以向資源提供者註冊您自己的 SQL Server VM，以取得 SQL IaaS 延伸模組所提供的管理能力優點。 但是，您無法將自部署的 SQL Server VM 轉換為即用即付。


   


## <a name="administration"></a>系統管理

1. **是否可以在同一 VM 上安裝 SQL Server 的第二個實例？是否可以更改預設實例的已安裝功能？**

   是。 SQL Server 安裝介質位於**C**磁碟機上的資料夾中。 從該位置運行**安裝程式.exe**以添加新 SQL Server 實例或更改電腦上的 SQL Server 的其他已安裝功能。 請注意，某些功能（如自動備份、自動修補和 Azure 金鑰保存庫集成）僅針對預設實例或正確配置的命名實例（請參閱問題 3）。 

1. **是否可以將 SQL Server 的預設執行個體解除安裝**

   可以，但有幾點考量。 首先，根據 VM 的許可證模型，SQL Server 關聯的計費可能會繼續發生。 其次，如上一個答案所述，有些功能依賴于[SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)。 如果卸載預設實例而不同時刪除 IaaS 擴展，則擴展將繼續查找預設實例，並可能產生事件日誌錯誤。 這些錯誤來自下列兩個來源：**Microsoft SQL Server 認證管理**和 **Microsoft SQL Server IaaS 代理程式**。 其中一個錯誤應如下所示：

      和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。

   如果確實決定卸載預設實例，也可以卸載[SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)。 

1. **是否可以將 SQL Server 的命名實例與 IaaS 擴展一起使用**？
   
   是，如果命名實例是 SQL Server 上的唯一實例，並且原始預設實例已[正確卸載](virtual-machines-windows-sql-server-agent-extension.md#install-on-a-vm-with-a-single-named-sql-server-instance)。 如果單個 SQL Server VM 上沒有預設實例，並且有多個命名實例，則 SQL Server IaaS 代理擴展將無法安裝。 

1. **是否可以從 SQL Server VM 中完全刪除 SQL 伺服器？**

   可以，但您將繼續為 SQL Server VM 付費，如[SQL Server Azure VM 的定價指南](virtual-machines-windows-sql-server-pricing-guidance.md)中所述。 如果您不再需要 SQL Server，則可以部署新的虛擬機器，並將資料和應用程式移轉到新的虛擬機器。 接著，您可以移除 SQL Server 虛擬機器。
   
## <a name="updating-and-patching"></a>更新和修補

1. **如何將 Azure VM 中的 SQL Server 變更為不同版本？**

   客戶可以使用包含所需 SQL Server 版本或版次的安裝媒體來變更 SQL Server 的版本/版次。 變更版本之後，請使用 Azure 入口網站來修改 VM 的版本屬性，以精確反映 VM 的計費。 有關詳細資訊，請參閱[SQL Server VM 的更改版本](virtual-machines-windows-sql-change-edition.md)。 不同版本的 SQL Server 沒有計費差異，因此一旦 SQL Server 版本更改，就無需執行進一步操作。

1. **我在哪裡可以獲取設置介質來更改 SQL Server 的版本或版本？**

   擁有[軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客戶可以從[大量授權中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)獲得其安裝介質。 沒有軟體保證的客戶可以使用具有所需版本的應用商店 SQL Server VM 映射中的設置媒體。
   
1. **如何將更新和 Service Pack 套用到 SQL Server VM 上？**

   虛擬機器可讓您控制主機電腦，包括套用更新的時間與方法。 針對作業系統，您可以手動套用 Windows 更新，或是啟用名為 [自動修補](virtual-machines-windows-sql-automated-patching.md)的排程服務。 自動修補會安裝任何標示為重要的更新，包括該類別目錄中的 SQL Server 更新。 其他選擇性的 SQL Server 更新，則必須手動安裝。

1. **我可以在 SQL Server VM 資來源提供者註冊 SQL Server 2008 / 2008 R2 實例後升級它嗎？**

   是。 您可以使用任何設置媒體來升級 SQL Server 的版本和版本，然後可以將[SQL IaaS 擴展模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)從_無代理_升級到_完整_。 這樣做將使您能夠訪問 SQL IaaS 擴展的所有優勢，如門戶可管理性、自動備份和自動修補。 

1. **如何為支援 SQL Server 2008 和 SQL Server 2008 R2 實例的末尾獲得免費擴展的安全更新？**

   通過將 SQL Server 按"按"情況移動到 Azure SQL 虛擬機器，可以獲取[免費擴展的安全更新](virtual-machines-windows-sql-server-2008-eos-extend-support.md)。 有關詳細資訊，請參閱[支援選項的結尾](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
  
   

## <a name="general"></a>一般

1. **Azure VM 上支援 SQL Server 容錯移轉叢集實例 （FCI）嗎？**

   是。 您可以使用[高級檔共用 （PFS）](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)或儲存子系統[的直接存儲空間 （S2D）](virtual-machines-windows-portal-sql-create-failover-cluster.md)安裝容錯移轉叢集實例。 高級檔共用提供 IOPS 和輸送量容量，可滿足許多工作負載的需求。 對於 IO 密集型工作負載，請考慮直接使用基於人造高級磁片或超磁片的存儲空間。 或者，您可以使用 [Azure 虛擬機器中的 SQL Server 的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)中所述的協力廠商叢集或儲存體解決方案。

   > [!IMPORTANT]
   > 此時，Azure 上的 SQL Server FCI 不支援_完整的_ [SQL Server IaaS 代理擴展](virtual-machines-windows-sql-server-agent-extension.md)。 我們建議您從參與 FCI 的 VM 卸載_完整_擴展，然後以_羽量級_模式安裝擴展。 此擴展支援功能，如自動備份和修補以及 SQL Server 的一些門戶功能。 卸載_完整_代理後，這些功能不適用於 SQL Server VM。

1. **SQL Server VM 和 SQL 資料庫服務之間的區別是什麼？**

   從概念上來說，在 Azure 虛擬機器上執行 SQL Server 與在遠端資料中心中執行 SQL Server 並沒什麼不同。 對比之下， [SQL Database](../../../sql-database/sql-database-technical-overview.md) 可提供資料庫即服務的功能。 使用 SQL Database 時，您無法存取主控資料庫的機器。 如需完整的比較，請參閱 [選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何在 Azure VM 上安裝 SQL 資料工具？**

    請從 [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313) 下載並安裝 SQL 資料工具。

1. **SQL 伺服器 VM 上支援使用 MSDTC 的分散式交易？**
   
    是。 SQL Server 2016 SP2 及更高更高部分支援本地 DTC。 但是，在使用"始終打開可用性"組時，必須測試應用程式，因為容錯移轉期間在途中的事務將失敗，必須重試。 群集 DTC 可從 Windows 伺服器 2019 開始使用。 

## <a name="resources"></a>資源

**視窗 VM**：

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)。
* [佈建 SQL Server Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [將資料庫移轉到 Azure VM 上的 SQL 伺服器](virtual-machines-windows-migrate-sql.md)
* [Azure 虛擬機器中 SQL 伺服器的高可用性和災害復原](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-windows-sql-performance.md)
* [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**：

* [Linux VM 上的 SQL Server 概觀](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [佈建 SQL Server Linux VM](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [常見問題集 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Linux 文檔上的 SQL 伺服器](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
