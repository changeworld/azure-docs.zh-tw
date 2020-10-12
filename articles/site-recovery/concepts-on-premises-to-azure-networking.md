---
title: 使用 Azure Site Recovery 連接到 Azure Vm 內部部署容錯移轉
description: 說明如何使用 Azure Site Recovery，在從內部部署容錯移轉至 Azure 之後連接至 Azure Vm
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 123a68885346062b9e8a53b8d5066204b6b20f5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568773"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>從內部部署容錯移轉後連線至 Azure Vm 


本文說明如何設定連線能力，讓您可以在容錯移轉之後成功連線至 Azure Vm。

當您設定內部部署虛擬機器的嚴重損壞修復 (Vm) 和實體伺服器移至 Azure 時， [Azure Site Recovery](site-recovery-overview.md) 會開始將機器複寫至 azure。 然後，當發生中斷時，您就可以從內部部署網站容錯移轉至 Azure。 發生容錯移轉時，Site Recovery 使用複寫的內部部署資料來建立 Azure Vm。 作為嚴重損壞修復計畫的一部分，您需要瞭解如何在容錯移轉之後連線到在這些 Azure Vm 上執行的應用程式。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 在容錯移轉之前準備內部部署機器。
> * 在容錯移轉之後準備 Azure Vm。 
> * 在容錯移轉後保留 Azure Vm 上的 IP 位址。
> * 在容錯移轉之後，將新的 IP 位址指派給 Azure Vm。

## <a name="prepare-on-premises-machines"></a>準備內部部署機器

為了確保能夠連線到 Azure Vm，請在容錯移轉之前先準備您的內部部署機器。

### <a name="prepare-windows-machines"></a>準備 Windows 電腦

在內部部署 Windows 電腦上，執行下列操作：

1. 設定 Windows 設定。 這些包括移除任何靜態持續性路由或 WinHTTP proxy，以及將磁片 SAN 原則設定為 **OnlineAll**。 [遵循](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) 這些指示。

2. 請確定[這些服務](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)正在執行。

3. 啟用遠端桌面 (RDP) 以允許從遠端連線至內部部署電腦。 [了解如何](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)使用 PowerShell 啟用 RDP。

4. 若要在容錯移轉後透過網際網路存取 Azure VM，請在內部部署電腦的 Windows 防火牆中，允許公用設定檔中的 TCP 和 UDP，並將 RDP 設定為所有設定檔允許的應用程式。

5. 如果您想要在容錯移轉之後透過站對站 VPN 存取 Azure VM，請在內部部署電腦的 Windows 防火牆中，允許網域和私人設定檔的 RDP。 [瞭解](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) 如何允許 RDP 流量。
6. 當您觸發容錯移轉時，請確定內部部署 VM 上沒有任何 Windows 更新擱置中。 如果有，則在容錯移轉之後，更新可能會開始在 Azure VM 上安裝，而且在更新完成之前，您將無法登入 VM。

### <a name="prepare-linux-machines"></a>準備 Linux 電腦

在內部部署 Linux 電腦上，執行下列操作：

1. 檢查安全殼層服務已設定為在系統開機時自動啟動。
2. 確認防火牆規則允許 SSH 連線。


## <a name="configure-azure-vms-after-failover"></a>在容錯移轉之後設定 Azure Vm

在容錯移轉之後，請在所建立的 Azure Vm 上執行下列動作。

1. 若要透過網際網路連線至 VM，請將公用 IP 位址指派給 VM。 您無法針對用於內部部署電腦的 Azure VM 使用相同的公用 IP 位址。 [深入了解](../virtual-network/virtual-network-public-ip-address.md)
2. 檢查 VM 上的網路安全性群組 (NSG) 規則是否允許連至 RDP 或 SSH 連接埠的連入連線。
3. 請檢查 [開機診斷](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) 以查看 VM。


> [!NOTE]
> Azure Bastion 服務會對 Azure VM 提供私密的 RDP 和 SSH 存取權。 [深入了解](../bastion/bastion-overview.md)此服務。

## <a name="set-a-public-ip-address"></a>設定公用 IP 位址

除了手動將公用 IP 位址指派給 Azure VM，您可以在容錯移轉期間使用腳本或 Azure 自動化 runbook 在 Site Recovery 復原 [方案](site-recovery-create-recovery-plans.md)中指派位址，也可以使用 Azure 流量管理員來設定 DNS 層級路由。 [深入瞭解](concepts-public-ip-address-with-site-recovery.md) 如何設定公用位址。


## <a name="assign-an-internal-address"></a>指派內部位址

若要在容錯移轉之後設定 Azure VM 的內部 IP 位址，您有幾個選項：

- **保留相同的 ip 位址**：您可以使用 Azure VM 上的相同 ip 位址，作為配置給內部部署機器的 ip 位址。
- **使用不同的 ip 位址**：您可以針對 Azure VM 使用不同的 ip 位址。


## <a name="retain-ip-addresses"></a>保留 IP 位址

Site Recovery 可讓您在容錯移轉至 Azure 時保留相同的 IP 位址。 在容錯移轉後保留相同的 IP 位址可避免潛在的網路問題，但會導致一些複雜性。

- 如果目標 Azure VM 使用與內部部署網站相同的 IP 位址/子網，您就無法使用站對站 VPN 連線或 ExpressRoute 在兩者之間進行連線，因為位址重迭。 子網必須是唯一的。
- 您需要在容錯移轉後從內部部署連線至 Azure，讓應用程式可在 Azure Vm 上使用。 Azure 不支援延伸的 Vlan，因此，如果您想要保留 IP 位址，您必須在內部部署電腦上容錯移轉整個子網，以將 ip 空間帶到 Azure。
- 子網容錯移轉可確保特定子網無法同時在內部部署和 Azure 中使用。

保留 IP 位址需要下列步驟：

- 在複寫專案的 [計算 & 網路] 屬性中，將目標 Azure VM 的網路和 IP 位址設定為鏡像內部部署設定。
- 子網必須作為嚴重損壞修復程式的一部分來管理。 您需要 Azure VNet 以符合內部部署網路，而且在容錯移轉網路路由之後必須修改，以反映子網已移至 Azure，以及新的 IP 位址位置。  

### <a name="failover-example"></a>容錯移轉範例

讓我們看看下列範例。

- 虛構的公司 Woodgrove Bank 裝載其在 Azure 中裝載行動應用程式的內部部署商務應用程式。
- 它們會透過站對站 VPN 從內部部署連線至 Azure。 
- Woodgrove 使用 Site Recovery 將內部部署機器複寫至 Azure。
- 其內部部署應用程式會使用硬式編碼的 IP 位址，因此它們會想要在 Azure 中保留相同的 IP 位址。
- 在內部部署中，執行應用程式的機器會在三個子網中執行：
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- 其在 Azure 中執行的應用程式位於兩個子網中的 Azure VNet **Azure 網路** 中：
    - 172.16.1.0/24
    - 172.16.2.0/24

為了保留位址，以下是其用途。

1. 當他們啟用複寫時，會指定機器應該複寫到 **Azure 網路**。
2. 他們會在 Azure 中建立復原 **網路** 。 此 VNet 會鏡像其內部部署網路中的 192.168.1.0/24 子網。
3. Woodgrove 會設定兩個網路之間的 [vnet 對 vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 連線。 

    > [!NOTE]
    > 視應用程式需求而定，您可以在容錯移轉之前設定 VNet 對 VNet 連線，例如 Site Recovery 復原 [方案](site-recovery-create-recovery-plans.md)中的手動步驟/腳本步驟/Azure 自動化 runbook，或在容錯移轉完成之後。

4. 在容錯移轉之前，它們會在 Site Recovery 的機器屬性上，將目標 IP 位址設定為內部部署電腦的位址，如下一個程式所述。
5. 在容錯移轉之後，會使用相同的 IP 位址建立 Azure Vm。 Woodgrove 會使用 VNet 對等互連 (從 **Azure 網路** 連線到復原 **網路** VNet，且已啟用傳輸連線) 。
6. 在內部部署中，Woodgrove 需要進行網路變更，包括修改路由以反映 192.168.1.0/24 已移至 Azure。  

**容錯移轉之前的基礎結構**

![子網路容錯移轉之前](./media/site-recovery-network-design/network-design7.png)


**容錯移轉之後的基礎結構**

![子網路容錯移轉之後](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>設定目標網路設定

在容錯移轉之前，請指定目標 Azure VM 的網路設定和 IP 位址。

1.  在 [復原服務保存庫-> 已複寫的 **專案**] 中，選取內部部署機器。
2. 在機器的 [ **計算和網路** ] 頁面中，按一下 [ **編輯**]，以設定目標 Azure VM 的網路和介面卡設定。
3. 在 [ **網路屬性**] 中，選取 Azure VM 在容錯移轉後建立時所在的目標網路。
4. 在 [ **網路介面**] 中，設定目標網路中的網路介面卡。 依預設 Site Recovery 會顯示內部部署機器上所有偵測到的 Nic。
    - 在 [ **目標網路介面類別型** ] 中，您可以將每個 NIC 設定為 [ **主要**]、[ **次要**]，或者如果您不需要目標網路中的特定 nic，則 **不會建立** 。 一個網路介面卡必須設定為容錯移轉的主要網路介面卡。 請注意，修改目標網路會影響 Azure VM 的所有 Nic。
    - 按一下 [NIC 名稱]，以指定將在其中部署 Azure VM 的子網。
    - 使用您想要指派給目標 Azure VM 的私人 IP 位址來覆寫 **動態** 。 如果未指定 IP 位址 Site Recovery 會在容錯移轉時將子網中下一個可用的 IP 位址指派給 NIC。
    - [深入瞭解](site-recovery-manage-network-interfaces-on-premises-to-azure.md) 如何管理 nic 以進行內部部署至 Azure 的容錯移轉。


## <a name="get-new-ip-addresses"></a>取得新的 IP 位址

在此案例中，Azure VM 會在容錯移轉之後取得新的 IP 位址。 若要為在容錯移轉後建立的虛擬機器設定新的 IP 位址，可參考下列步驟-

1. 移至 [已複寫的 **專案**]。
2. 選取所需的 Azure 虛擬機器。
3. 選取 [ **計算和網路** ]，然後選取 [ **編輯**]。

     ![自訂容錯移轉網路設定](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. 若要更新容錯移轉網路設定，請選取您要設定的 NIC 的 [ **編輯** ]。 在開啟的下一個頁面中，在測試容錯移轉和容錯移轉位置提供對應的預先建立 IP 位址。

    ![編輯 NIC 設定](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. 選取 [確定]  。

Site Recovery 現在將採用這些設定，並確保容錯移轉上的虛擬機器透過對應的 IP 位址（如果目標 IP 範圍中有提供的話）連接到選取的資源。 在此案例中，不需要容錯移轉整個子網。 需要 DNS 更新才能將已容錯移轉的機器的記錄更新為指向虛擬機器的新 IP 位址。

## <a name="next-steps"></a>後續步驟
[瞭解](site-recovery-active-directory.md) 如何將內部部署 ACTIVE DIRECTORY 和 DNS 複寫至 Azure。
