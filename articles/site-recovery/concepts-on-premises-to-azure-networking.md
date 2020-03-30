---
title: 通過 Azure 網站恢復連接到本地容錯移轉的 Azure VM
description: 介紹如何使用 Azure 網站恢復在容錯移轉到 Azure 後如何連接到 Azure VM
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281986"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>從本地容錯移轉後連接到 Azure VM 


本文介紹如何設置連接，以便在容錯移轉後成功連接到 Azure VM。

將本地虛擬機器 （VM） 和物理伺服器的災害復原設置為 Azure 時[，Azure 網站恢復](site-recovery-overview.md)將開始將電腦複製到 Azure。 然後，當發生中斷時，可以從本地網站容錯移轉到 Azure。 發生容錯移轉時，網站恢復使用複製的本地資料創建 Azure VM。 作為災害復原規劃的一部分，您需要瞭解如何在容錯移轉後連接到在這些 Azure VM 上運行的應用。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 在容錯移轉之前準備本地電腦。
> * 在容錯移轉後準備 Azure VM。 
> * 容錯移轉後，在 Azure VM 上保留 IP 位址。
> * 容錯移轉後向 Azure VM 分配新的 IP 位址。

## <a name="prepare-on-premises-machines"></a>準備本地電腦

為確保連接到 Azure VM，請在容錯移轉之前準備本地電腦。

### <a name="prepare-windows-machines"></a>準備 Windows 電腦

在內部部署 Windows 電腦上，執行下列操作：

1. 設定 Windows 設定。 其中包括刪除任何靜態持久路由或 WinHTTP 代理，以及將磁片 SAN 策略設置為**OnlineAll**。 [按照](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)這些說明操作。

2. 請確定[這些服務](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)正在執行。

3. 啟用遠端桌面 (RDP) 以允許從遠端連線至內部部署電腦。 [了解如何](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)使用 PowerShell 啟用 RDP。

4. 要在容錯移轉後通過 Internet 訪問 Azure VM，在本地電腦上的 Windows 防火牆中，允許公共設定檔中的 TCP 和 UDP，並將 RDP 設置為所有設定檔的允許應用。

5. 如果要在容錯移轉後通過網站到網站 VPN 訪問 Azure VM，請在本地電腦上的 Windows 防火牆中，允許域和專用設定檔的 RDP。 [了解](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)如何允許 RDP 流量。
6. 在觸發容錯移轉時，請確保本地 VM 上沒有掛起的 Windows 更新。 如果有，更新可能在容錯移轉後開始在 Azure VM 上安裝，並且在更新完成之前無法登錄到 VM。

### <a name="prepare-linux-machines"></a>準備 Linux 電腦

在內部部署 Linux 電腦上，執行下列操作：

1. 檢查安全殼層服務已設定為在系統開機時自動啟動。
2. 確認防火牆規則允許 SSH 連線。


## <a name="configure-azure-vms-after-failover"></a>容錯移轉後配置 Azure VM

容錯移轉後，對創建的 Azure VM 執行以下操作。

1. 若要透過網際網路連線至 VM，請將公用 IP 位址指派給 VM。 您無法針對用於內部部署電腦的 Azure VM 使用相同的公用 IP 位址。 [深入了解](../virtual-network/virtual-network-public-ip-address.md)
2. 檢查 VM 上的網路安全性群組 (NSG) 規則是否允許連至 RDP 或 SSH 連接埠的連入連線。
3. 檢查 [的開機診斷](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) 以查看 VM。


> [!NOTE]
> Azure Bastion 服務會對 Azure VM 提供私密的 RDP 和 SSH 存取權。 [深入了解](../bastion/bastion-overview.md)此服務。

## <a name="set-a-public-ip-address"></a>設置公共 IP 位址

作為手動將公共 IP 位址分配給 Azure VM 的替代方法，可以在容錯移轉期間使用網站恢復[恢復計畫中的](site-recovery-create-recovery-plans.md)腳本或 Azure 自動化 Runbook 分配位址，也可以使用 Azure 流量管理器設置 DNS 級路由。 [詳細瞭解](concepts-public-ip-address-with-site-recovery.md)如何設置公共位址。


## <a name="assign-an-internal-address"></a>分配內部位址

要在容錯移轉後設置 Azure VM 的內部 IP 位址，有幾個選項：

- **保留相同的 IP 位址**：您可以在 Azure VM 上使用與分配給本地電腦的 IP 位址相同的 IP 位址。
- **使用不同的 IP 位址**：可以為 Azure VM 使用不同的 IP 位址。


## <a name="retain-ip-addresses"></a>保留 IP 位址

網站恢復允許您在容錯移轉到 Azure 時保留相同的 IP 位址。 保留相同的 IP 位址可避免容錯移轉後的潛在網路問題，但確實會帶來一些複雜性。

- 如果目標 Azure VM 使用與本地網站相同的 IP 位址/子網，則由於位址重疊，無法使用網站到網站 VPN 連接或 ExpressRoute 在它們之間連接。 子網必須是唯一的。
- 容錯移轉後，您需要從本地連接到 Azure 的連接，以便應用在 Azure VM 上可用。 Azure 不支援拉伸的 VLAN，因此，如果要保留 IP 位址，除了本地電腦之外，還需要通過對整個子網失敗來將 IP 空間轉移到 Azure。
- 子網容錯移轉可確保特定子網在本地和 Azure 中不同時可用。

保留 IP 位址需要以下步驟：

- 在複製項的計算&網路屬性中，為目標 Azure VM 設置網路和 IP 定址以鏡像本地設置。
- 子網必須作為災害復原過程的一部分進行管理。 您需要 Azure VNet 與本地網路匹配，在容錯移轉後必須修改網路路由以反映子網已移動到 Azure 和新的 IP 位址位置。  

### <a name="failover-example"></a>容錯移轉範例

讓我們看看以下範例。

- 虛構的公司 Woodgrove Bank 在本地託管其業務應用他們在 Azure 中託管其移動應用程式。
- 它們通過網站到網站 VPN 從本地連接到 Azure。 
- Woodgrove 正在使用網站恢復將本地電腦複製到 Azure。
- 其本地應用使用硬式編碼 IP 位址，因此他們希望在 Azure 中保留相同的 IP 位址。
- 在本地運行應用的電腦在三個子網中運行：
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- 在 Azure 中運行的應用位於 Azure VNet **Azure 網路中**的兩個子網中：
- 172.16.1.0/24
- 172.16.2.0/24.

為了保留位址，下面是他們所做的。

1. 啟用複製時，它們指定電腦應複製到**Azure 網路**。
2. 它們在 Azure 中創建**恢復網路**。 此 VNet 反映其本地網路中的 192.168.1.0/24 子網。
3. 伍德格羅夫在兩個網路之間建立了[VNet 到 VNet 連接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 

    > [!NOTE]
    > 根據應用程式要求，可以在容錯移轉之前設置 VNet 到 VNet 連接，作為網站恢復[恢復計畫中的](site-recovery-create-recovery-plans.md)手動步驟/腳本化步驟/Azure 自動化運行簿，或在容錯移轉完成後。

4. 在容錯移轉之前，在網站恢復中的電腦屬性上，他們將目標 IP 位址設置為本地電腦的位址，如下一步所述。
5. 容錯移轉後，使用相同的 IP 位址創建 Azure VM。 Woodgrove 使用 VNet 對等互連（啟用傳輸連接）從**Azure 網路**連接到**恢復網路**VNet。
6. 在本地，Woodgrove 需要進行網路更改，包括修改路由以反映 192.168.1.0/24 已移動到 Azure。  

**容錯移轉之前的基礎結構**

![子網路容錯移轉之前](./media/site-recovery-network-design/network-design7.png)


**容錯移轉之後的基礎結構**

![子網路容錯移轉之後](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>設置目標網路設置

在容錯移轉之前，指定目標 Azure VM 的網路設置和 IP 位址。

1.  在恢復服務保存庫 ->**複製項**中，選擇本地電腦。
2. 在電腦的計算**和網路**頁中，按一下 **"編輯**"以配置目標 Azure VM 的網路和配接器設置。
3. 在**Network 屬性**中，選擇在容錯移轉後創建 Azure VM 時將位於其的目標網路。
4. 在**網路介面**中，配置目標網路中的網路介面卡。 預設情況下，網站恢復顯示本地電腦上檢測到的所有 NIC。
    - 在**目標網路介面類別型**中，您可以將每個 NIC 設置為**主**、**輔助**，或者，如果目標網路中不需要該特定 NIC，則可以**創建**該 NIC。 必須將一個網路介面卡設置為容錯移轉的主配接器。 請注意，修改目標網路會影響 Azure VM 的所有 NIC。
    - 按一下 NIC 名稱以指定將部署 Azure VM 的子網。
    - 使用要分配給目標 Azure VM 的私人 IP 位址覆蓋**動態**。 如果未指定 IP 位址，網站恢復將在容錯移轉時將子網中的下一個可用 IP 位址分配給 NIC。
    - [詳細瞭解](site-recovery-manage-network-interfaces-on-premises-to-azure.md)如何管理 NIC，以便本地容錯移轉到 Azure。


## <a name="get-new-ip-addresses"></a>獲取新的 IP 位址

在這種情況下，Azure VM 在容錯移轉後獲取新的 IP 位址。 用於更新電腦上故障記錄以指向 Azure VM 的 IP 位址的 DNS 更新。



## <a name="next-steps"></a>後續步驟
[瞭解如何](site-recovery-active-directory.md)將本地活動目錄和 DNS 複製到 Azure。


