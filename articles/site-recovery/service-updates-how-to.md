---
title: Azure 網站恢復中的更新和元件升級
description: 提供 Azure 網站恢復服務更新和元件升級的概述。
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257819"
---
# <a name="service-updates-in-site-recovery"></a>網站恢復中的服務更新

本文概述了[Azure 網站恢復](site-recovery-overview.md)更新，並介紹了如何升級網站恢復元件。

網站恢復定期發佈服務更新。 更新包括新功能、支援改進、元件更新和錯誤修復。 為了利用最新的功能和修復程式，我們建議運行最新版本的網站恢復元件。 
 
 
## <a name="updates-support"></a>更新支援

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支援聲明

我們建議始終升級到最新的元件版本：

**發佈 Azure 網站恢復元件的每個新版本"N"，則"N-4"以下的所有版本都被視為不支援**。 

> [!IMPORTANT]
> 官方支援是從n-4>版本升級到N版本。 例如，如果您正在運行，則首先需要升級到 N-4，然後升級到 N。


### <a name="links-to-currently-supported-update-rollups"></a>目前支援的更新彙總套件連結

 查看[本文](site-recovery-whats-new.md)中的最新更新彙總套件（版本 N）。 請記住，網站恢復支援 N-4 版本。



## <a name="component-expiry"></a>元件過期

網站恢復通過電子郵件（如果您訂閱電子郵件通知）或閘戶中的保存庫儀表板上通知您過期的元件（或即將到期）。

- 此外，當更新可用時，在門戶中方案的基礎結構視圖中，元件旁邊將顯示 **"更新可用**"按鈕。 此按鈕將您重定向到用於下載最新元件版本的連結。
-  如果要複製超 VM，保存庫儀表板通知不可用。 

電子郵件通知發送方式如下。

**時間** | **頻率**
--- | ---
元件到期前 60 天 | 每兩週一次
未來 53 天 | 一週一次
過去 7 天 | 一天 1 次
到期後 | 每兩週一次


### <a name="upgrading-outside-official-support"></a>升級外部官方支援

如果元件版本和最新版本之間的差異大於 4，則視為不支援。 在這種情況下，升級方式如下： 

1. 將當前安裝的元件升級到當前版本加上四個元件。 例如，如果版本為 9.16，則升級到 9.20。
2. 然後，升級到下一個相容版本。 因此，在我們的示例中，在將 9.16 升級到 9.20 後，升級到 9.24。 

對所有相關元件遵循相同的流程。

### <a name="support-for-latest-operating-systemskernels"></a>支援最新的作業系統/內核

> [!NOTE]
> 如果已計畫維護視窗，並且其中包含重新開機，我們建議您首先升級網站恢復元件，然後繼續維護視窗中計畫的其他活動。

1. 在升級作業系統/內核版本之前，請驗證目標版本是否受支援網站恢復。 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)支援。
    - [VMware/物理伺服器](vmware-physical-azure-support-matrix.md#replicated-machines)支援
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms)支援。
2. 查看[可用更新](site-recovery-whats-new.md)，瞭解要升級的內容。
3. 升級到最新的網站恢復版本。
4. 將作業系統/內核升級到所需的版本。
5. 重新開機。


此過程可確保將電腦作業系統/內核升級到最新版本，並確保支援新版本所需的最新網站恢復更改載入到電腦上。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 至 Azure 災害復原

在這種情況下，我們強烈建議您[啟用自動更新](azure-to-azure-autoupdate.md)。 您可以允許網站恢復管理更新，如下所示：

- 在啟用複製過程中。
- 通過在保存庫內設置擴展更新設置。

如果要手動管理更新，可以執行以下操作：

1. 在複製**專案**>保存庫中，按一下螢幕頂部的此通知： 
    
    **新的網站恢復複製代理更新可用。按一下以安裝 ->**

4. 選擇要為其應用更新的 VM，然後按一下"**確定**"。


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/物理伺服器災害復原到 Azure

1. 基於當前版本[和支援語句](#support-statement-for-azure-site-recovery)，請首先使用這些[說明](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)在本地佈建服務器上安裝更新。 
2. 如果您有橫向擴展進程伺服器，請使用[這些說明](vmware-azure-manage-process-server.md#upgrade-a-process-server)，下一步更新它們。
3. 要更新每個受保護電腦上的移動代理，請參閱[本文](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)。

### <a name="reboot-after-mobility-service-upgrade"></a>移動服務升級後重新開機

建議在每次升級移動服務後重新開機，以確保在源電腦上載入所有最新的更改。

重新開機不是強制性的，除非上次重新開機期間的代理版本與當前版本之間的差值大於 4。

表中的示例顯示了其工作原理。

|**代理版本（上次重新開機）** | **升級到** | **強制重新開機？**|
|---------|---------|---------|
|9.16 |  9.18 | 非強制性|
|9.16 | 9.19 | 非強制性|
| 9.16 | 9.20 | 非強制性
 | 9.16 | 9.21 | Mandatory。<br/><br/> 升級到 9.20，然後在升級到 9.21 之前重新開機。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM 至 Azure 災害復原

### <a name="between-a-hyper-v-site-and-azure"></a>在 Hyper-V 網站和 Azure 之間

1. 下載 Microsoft Azure 網站恢復提供程式的更新。
2. 在網站恢復中註冊的每個 Hyper-V 伺服器上安裝提供程式。 如果運行群集，則在所有叢集節點上升級。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>在內部部署 VMM 網站及 Azure 之間
1. 下載 Microsoft Azure 網站恢復提供程式的更新。
2. 在 VMM 伺服器上安裝提供程式。 如果 VMM 部署在群集中，請在所有叢集節點上安裝提供程式。
3. 在所有 Hyper-V 主機或叢集節點上安裝最新的 Microsoft Azure 恢復服務代理。


## <a name="between-two-on-premises-vmm-sites"></a>在兩個內部部署 VMM 網站之間
1. 下載 Microsoft Azure 網站恢復提供程式的最新更新。
2. 在管理輔助恢復網站的 VMM 伺服器上安裝最新的提供程式。 如果 VMM 部署在群集中，請在所有叢集節點上安裝提供程式。
3. 更新恢復網站後，在管理主網站的 VMM 伺服器上安裝提供程式。

## <a name="next-steps"></a>後續步驟

關注 Azure[更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面以跟蹤新的更新和發佈。
