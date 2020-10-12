---
title: Azure Site Recovery 中的更新和元件升級
description: 提供 Azure Site Recovery 服務更新和元件升級的總覽。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424934"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery 中的服務更新

本文提供 [Azure Site Recovery](site-recovery-overview.md) 更新的總覽，並說明如何升級 Site Recovery 元件。

Site Recovery 定期發行服務更新。 更新包括新功能、支援改進、元件更新和錯誤修正。 為了充分利用最新的功能和修正，建議您執行最新版本的 Site Recovery 元件。 
 
 
## <a name="updates-support"></a>更新支援

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery 的支援聲明

建議您一律升級至最新的元件版本：

一旦**發行 Azure Site Recovery 元件的每個新版本 ' n '，則所有低於 ' n-1 ' 的版本都會被視為不受支援**。 

> [!IMPORTANT]
> 官方支援適用于從 > N-4 版本升級為 N 版。 例如，如果您執行的是 N-6，您必須先升級至 N-4，然後再升級至 N。


### <a name="links-to-currently-supported-update-rollups"></a>目前支援的更新彙總套件連結

 請參閱本文中的 (第 N 版) 最 [新的更新](site-recovery-whats-new.md)匯總。 請記住，Site Recovery 提供了 N-1 個版本的支援。



## <a name="component-expiry"></a>元件到期

Site Recovery 如果您已訂閱電子郵件通知) ，或在入口網站中的保存庫儀表板上，您可以透過電子郵件 (，通知您過期的元件 (或即將到期) 。

- 此外，當有可用的更新時，在入口網站中您案例的基礎結構視圖中，元件旁邊會出現 [ **更新可用** ] 按鈕。 此按鈕會將您重新導向至下載最新元件版本的連結。
-  如果您要複寫 Hyper-v Vm，則無法使用保存庫儀表板通知。 

電子郵件通知會以下面方式傳送。

**Time** | **頻率**
--- | ---
元件到期前60天 | 只要 bi-每週
接下來的53天 | 一週一次
過去 7 天 | 一天一次
到期後 | 只要 bi-每週


### <a name="upgrading-outside-official-support"></a>升級官方支援以外的地方

如果您的元件版本與最新版本之間的差異大於4，則會被視為不支援。 在此情況下，升級如下： 

1. 將目前安裝的元件升級為目前的版本加上四。 例如，如果您的版本為9.16，則升級為9.20。
2. 然後，升級到下一個相容的版本。 因此，在我們的範例中，將9.16 升級到9.20 之後，請升級至9.24。 

遵循所有相關元件的相同程式。

### <a name="support-for-latest-operating-systemskernels"></a>支援最新的作業系統/核心

> [!NOTE]
> 如果您已排程維護視窗，且其中包含重新開機，建議您先升級 Site Recovery 元件，然後繼續進行維護期間的其餘排程活動。

1. 升級作業系統/核心版本之前，請先確認 Site Recovery 是否支援目標版本。 

    - [AZURE VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 支援。
    - [VMware/實體伺服器](vmware-physical-azure-support-matrix.md#replicated-machines) 支援
    - [Hyper-v](hyper-v-azure-support-matrix.md#replicated-vms) 支援。
2. 查看 [可用的更新](site-recovery-whats-new.md) ，以找出您想要升級的內容。
3. 升級至最新的 Site Recovery 版本。
4. 將作業系統/核心升級為所需的版本。
5. 重新開機。


此程式可確保電腦作業系統/核心會升級為最新版本，而且支援新版本所需的最新 Site Recovery 變更會載入電腦上。

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure VM 至 Azure 災害復原

在此案例中，我們強烈建議您 [啟用自動更新](azure-to-azure-autoupdate.md)。 您可以允許 Site Recovery 管理更新，如下所示：

- 在啟用複寫過程中。
- 藉由設定保存庫內的擴充功能更新設定。

如果您想要手動管理更新，您可以選擇下列其中一個選項：

1. 當有新的代理程式更新可用時，Site Recovery 會在保存庫中提供通知給頁面頂端。 在保存庫 > **已**複寫的專案中，按一下畫面頂端的此通知： 
    
    **有新的 Site Recovery 複寫代理程式更新可用。按一下以安裝->** <br/><br/>選取您要套用更新的 Vm，然後按一下 **[確定]**。

2. 在 [VM 嚴重損壞修復] 頁面上，您會找到 [代理程式狀態] 欄位，如果代理程式到期，則會顯示 [重大升級]。 按一下該檔案，然後依照後續指示手動升級虛擬機器。

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/實體伺服器到 Azure 的嚴重損壞修復

1. 根據您目前的版本和 [支援聲明](#support-statement-for-azure-site-recovery)，請使用 [這些指示](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)，先在內部部署設定伺服器上安裝更新。 
2. 如果您有相應放大的進程伺服器，請接著使用 [這些指示](vmware-azure-manage-process-server.md#upgrade-a-process-server)加以更新。
3. 若要更新每部受保護電腦上的行動代理程式，請參閱 [這](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) 篇文章。

### <a name="reboot-after-mobility-service-upgrade"></a>行動服務升級之後重新開機

每次升級行動服務之後，建議您重新開機，以確保所有最新的變更都已載入來源電腦上。

除非在上一次重新開機時代理程式版本與目前版本之間的差異大於四，否則不需要重新開機。

下表中的範例會說明其運作方式。

|**代理程式版本 (上次重新開機) ** | **升級至** | **強制重新開機？**|
|---------|---------|---------|
|9.16 |  9.18 | 非強制性|
|9.16 | 9.19 | 非強制性|
| 9.16 | 9.20 | 非強制性
 | 9.16 | 9.21 | Mandatory。<br/><br/> 升級至9.20，然後重新開機，再升級至9.21。

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM 至 Azure 災害復原

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-v 網站與 Azure 之間

1. 下載 Microsoft Azure Site Recovery 提供者的更新。
2. 在 Site Recovery 中註冊的每部 Hyper-v 伺服器上安裝提供者。 如果您正在執行叢集，請在所有叢集節點上進行升級。


## <a name="between-an-on-premises-vmm-site-and-azure"></a>在內部部署 VMM 網站及 Azure 之間
1. 下載 Microsoft Azure Site Recovery 提供者的更新。
2. 在 VMM 伺服器上安裝提供者。 如果 VMM 部署在叢集中，請在所有叢集節點上安裝提供者。
3. 在所有 Hyper-v 主機或叢集節點上安裝最新的 Microsoft Azure 復原服務代理程式。


## <a name="between-two-on-premises-vmm-sites"></a>在兩個內部部署 VMM 網站之間
1. 下載 Microsoft Azure Site Recovery 提供者的最新更新。
2. 在管理次要復原網站的 VMM 伺服器上，安裝最新的提供者。 如果 VMM 部署在叢集中，請在所有叢集節點上安裝提供者。
3. 更新復原網站之後，在管理主要網站的 VMM 伺服器上安裝提供者。

## <a name="next-steps"></a>後續步驟

遵循 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery) 頁面來追蹤新的更新和發行。
