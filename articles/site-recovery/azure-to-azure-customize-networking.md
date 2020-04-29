---
title: 自訂容錯移轉 VM 的網路設定 |Microsoft Docs
description: 針對使用 Azure Site Recovery 的 Azure Vm 複寫中的容錯移轉 VM，提供自訂網路設定的總覽。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76292853"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>自訂目標 Azure 虛擬機器的網路設定

當您使用[Azure Site Recovery](site-recovery-overview.md)將 Azure vm 從一個區域複寫及復原到另一個區域時，本文提供在目標 Azure 虛擬機器（VM）上自訂網路設定的指引。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

了解 Site Recovery 如何針對[這種情況](azure-to-azure-architecture.md)提供災害復原。

## <a name="supported-networking-resources"></a>支援的網路資源

您可以在複寫 Azure Vm 時，提供容錯移轉 VM 的下列金鑰資源設定：

- [內部負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [公用 IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- 子網和 NIC 的[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

## <a name="prerequisites"></a>先決條件

- 請務必事先規劃您的復原端設定。
- 事先建立網路資源。 提供它做為輸入，讓 Azure Site Recovery 服務可以接受這些設定，並確保容錯移轉 VM 遵守這些設定。

## <a name="customize-failover-and-test-failover-networking-configurations"></a>自訂容錯移轉和測試容錯移轉網路設定

1. 移至 [已複寫的**專案**]。 
2. 選取所需的 Azure VM。
3. 選取 [**計算與網路**]，然後選取 [**編輯**]。 請注意，NIC 配置設定包含來源上的對應資源。 

     ![自訂容錯移轉網路設定](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 選取測試容錯移轉虛擬網路。 您可以選擇將它保留空白，並在測試容錯移轉時選取一個。
5. 容錯移轉網路是在您想要設定的 NIC 附近選取 [**編輯**]。 在開啟的下一個分頁中，選取測試容錯移轉和容錯移轉位置中對應的預先建立資源。

    ![編輯 NIC 設定](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. 選取 [確定]  。

Site Recovery 現在會接受這些設定，並確保容錯移轉上的 VM 會透過對應的 NIC 連線到選取的資源。

當您透過復原計畫觸發測試容錯移轉時，它一律會詢問 Azure 虛擬網路。 此虛擬網路將會用於未預先設定測試容錯移轉設定之電腦的測試容錯移轉。

## <a name="troubleshooting"></a>疑難排解

### <a name="unable-to-view-or-select-a-resource"></a>無法查看或選取資源

如果您無法選取或查看網路資源，請執行下列檢查和條件：

- 只有當來源 VM 具有對應的輸入時，才會啟用網路資源的 [目標] 欄位。 這是根據嚴重損壞修復案例的原則，您需要來源的確切或相應縮小版本。
- 針對每個網路資源，會在下拉式清單中套用一些篩選準則，以確保容錯移轉 VM 可以將自己附加到選取的資源，並維護容錯移轉的可靠性。 這些篩選器是根據您設定來源 VM 時已驗證的相同網路狀況。

內部負載平衡器驗證：

- 負載平衡器的訂用帳戶和區域和目標 VM 應相同。
- 與內部負載平衡器和目標 VM 相關聯的虛擬網路應該相同。
- 目標 VM 的公用 IP SKU 與內部負載平衡器的 SKU 應相同。
- 如果目標 VM 設定為放置於可用性區域中，則請檢查負載平衡器是否為區域多餘或任何可用性區域的一部分。 （基本 SKU 負載平衡器不支援區域，在此案例中不會顯示在下拉式清單中）。
- 請確定內部負載平衡器已預先建立後端集區和前端設定。

公用 IP 位址：

- 公用 IP 和目標 VM 的訂用帳戶和區域應相同。
- 目標 VM 的公用 IP SKU 與內部負載平衡器的 SKU 應相同。

網路安全性群組︰
- 「網路安全性群組」和「目標 VM」的訂用帳戶和區域應相同。


> [!WARNING]
> 如果目標 VM 與可用性設定組相關聯，則您需要將相同 SKU 的公用 IP 與內部負載平衡器關聯至可用性設定組中其他 VM 的公用 IP 與內部負載平衡器。 如果您沒有這樣做，容錯移轉可能就不會成功。
