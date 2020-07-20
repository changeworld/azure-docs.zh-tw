---
title: Azure Site Recovery 中的更新和元件升級
description: 提供 Azure Site Recovery 服務更新和元件升級的總覽。
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 483d2152f3af13c82830528ea73e837fe3788ca4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629377"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery 中的服務更新

本文提供[Azure Site Recovery](site-recovery-overview.md)更新的總覽，並說明如何升級 Site Recovery 元件。

Site Recovery 會定期發佈服務更新。 更新包含新功能、支援改進、元件更新和 bug 修正。 為了充分利用最新功能和修正程式，建議您執行最新版本的 Site Recovery 元件。 
 
 
## <a name="updates-support"></a>更新支援

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支援聲明

我們建議您一律升級至最新的元件版本：

**隨著發行之 Azure Site Recovery 元件的每個新版本 ' n '，所有低於 ' n-1 ' 的版本都被視為不支援**。 

> [!IMPORTANT]
> 正式支援是從 > N-4 版升級至 N 版。 例如，如果您執行的是 N-6，您需要先升級至 N-4，然後再升級為 N。


### <a name="links-to-currently-supported-update-rollups"></a>目前支援的更新彙總套件連結

 請參閱[本文中的](site-recovery-whats-new.md)最新更新彙總套件（版本 N）。 請記住，Site Recovery 提供 N-4 版本的支援。



## <a name="component-expiry"></a>元件到期

Site Recovery 會透過電子郵件（如果您已訂閱電子郵件通知），或在入口網站的保存庫儀表板上，通知您過期的元件（或即將到期）。

- 此外，當有可用的更新時，在入口網站中案例的基礎結構視圖中，元件旁邊會出現 [**可用更新**] 按鈕。 此按鈕會將您重新導向至下載最新元件版本的連結。
-  如果您要複寫 Hyper-v Vm，則無法使用保存庫儀表板通知。 

電子郵件通知的傳送方式如下。

**Time** | **頻率**
--- | ---
元件到期前60天 | Bi-每週
未來53天 | 一週一次
過去 7 天 | 一天一次
到期後 | Bi-每週


### <a name="upgrading-outside-official-support"></a>在官方支援以外進行升級

如果元件版本與最新發行版本之間的差異大於4，則會被視為不支援這種情況。 在此情況下，請如下所示升級： 

1. 將目前安裝的元件升級至您目前的版本，再加上四個。 例如，如果您的版本是9.16，則升級至9.20。
2. 然後，升級到下一個相容的版本。 因此，在我們的範例中，將9.16 升級至9.20 之後，請升級至9.24。 

針對所有相關元件遵循相同的程式。

### <a name="support-for-latest-operating-systemskernels"></a>支援最新的作業系統/核心

> [!NOTE]
> 如果您已排程維護時段，且其中包含重新開機，建議您先升級 Site Recovery 元件，然後在維護期間繼續進行排程活動的其餘部分。

1. 升級作業系統/核心版本之前，請先確認是否支援目標版本 Site Recovery。 

    - [AZURE VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)支援。
    - [VMware/實體伺服器](vmware-physical-azure-support-matrix.md#replicated-machines)支援
    - [Hyper-v](hyper-v-azure-support-matrix.md#replicated-vms)支援。
2. 查看[可用的更新](site-recovery-whats-new.md)，以瞭解您想要升級的內容。
3. 升級至最新的 Site Recovery 版本。
4. 將作業系統/核心升級為所需的版本。
5. 重新開機。


此程式可確保電腦作業系統/核心已升級至最新版本，而且支援新版本所需的最新 Site Recovery 變更會載入至電腦。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 至 Azure 災害復原

在此案例中，我們強烈建議您[啟用自動更新](azure-to-azure-autoupdate.md)。 您可以允許 Site Recovery 管理更新，如下所示：

- 在啟用複寫過程中。
- 藉由在保存庫內設定擴充功能更新設定。

如果您想要手動管理更新，可以選擇下列其中一個選項：

1. 當有新的代理程式更新可用時，Site Recovery 會在保存庫中提供通知，指向頁面頂端。 在保存庫 >**已**複寫的專案中，按一下畫面頂端的此通知： 
    
    **有新的 Site Recovery 複寫代理程式更新可供使用。按一下以安裝->** <br/><br/>選取您要套用更新的 Vm，然後按一下 **[確定]**。

2. 在 [VM 嚴重損壞修復總覽] 頁面上，如果代理程式是因為過期，您會發現「代理程式狀態」欄位會顯示「重大升級」。 按一下該檔案，並遵循後續指示以手動升級虛擬機器。

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/實體伺服器嚴重損壞修復至 Azure

1. 根據您目前的版本和[支援聲明](#support-statement-for-azure-site-recovery)，請使用[這些指示](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)，先在內部部署設定伺服器上安裝更新。 
2. 如果您有相應放大進程伺服器，請在下一次使用[這些指示](vmware-azure-manage-process-server.md#upgrade-a-process-server)加以更新。
3. 若要更新每部受保護電腦上的行動代理程式，請參閱[這](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)篇文章。

### <a name="reboot-after-mobility-service-upgrade"></a>在行動服務升級後重新開機

每次升級行動服務之後，建議重新開機，以確保來源機器上已載入所有最新的變更。

重新開機並非必要，除非在上次重新開機期間代理程式版本與目前版本之間的差異大於4。

下表中的範例會顯示其運作方式。

|**代理程式版本（上次重新開機）** | **升級至** | **強制重新開機？**|
|---------|---------|---------|
|9.16 |  9.18 | 非強制性|
|9.16 | 9.19 | 非強制性|
| 9.16 | 9.20 | 非強制性
 | 9.16 | 9.21 | Mandatory。<br/><br/> 升級至9.20，然後在升級至9.21 之前重新開機。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM 至 Azure 災害復原

### <a name="between-a-hyper-v-site-and-azure"></a>在 Hyper-v 網站與 Azure 之間

1. 下載 Microsoft Azure Site Recovery 提供者的更新。
2. 在 Site Recovery 中註冊的每部 Hyper-v 伺服器上安裝提供者。 如果您正在執行叢集，請在所有叢集節點上升級。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>在內部部署 VMM 網站及 Azure 之間
1. 下載 Microsoft Azure Site Recovery 提供者的更新。
2. 在 VMM 伺服器上安裝提供者。 如果 VMM 部署在叢集中，請在所有叢集節點上安裝提供者。
3. 在所有 Hyper-v 主機或叢集節點上安裝最新的 Microsoft Azure 復原服務代理程式。


## <a name="between-two-on-premises-vmm-sites"></a>在兩個內部部署 VMM 網站之間
1. 下載最新的 Microsoft Azure Site Recovery 提供者更新。
2. 在管理次要復原網站的 VMM 伺服器上安裝最新的提供者。 如果 VMM 部署在叢集中，請在所有叢集節點上安裝提供者。
3. 更新復原網站之後，請將提供者安裝在管理主要網站的 VMM 伺服器上。

## <a name="next-steps"></a>後續步驟

遵循我們的[Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面，追蹤新的更新和發行。
