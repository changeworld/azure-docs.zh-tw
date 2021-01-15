---
title: 準備 FCI 的虛擬機器
description: 準備您的 Azure 虛擬機器，以在 Azure 虛擬機器上 (FCI) 和 SQL Server 的容錯移轉叢集實例使用這些虛擬機器。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 1b8d88167dac6b2d0b1ba2afc90c443fd80b9e46
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223154"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>針對 Azure Vm 上的 FCI (SQL Server 準備虛擬機器) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何準備 Azure 虛擬機器 (Vm) 將它們與 SQL Server 容錯移轉叢集實例搭配使用 (FCI) 。 設定設定會因 FCI 儲存體解決方案而異，因此請驗證您所選擇的設定是否符合您的環境和企業。 

若要深入瞭解，請參閱 [使用 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md) 和叢集 [最佳作法](hadr-cluster-best-practices.md)的 FCI 總覽。 

## <a name="prerequisites"></a>先決條件 

- Microsoft Azure 訂用帳戶。 [免費](https://azure.microsoft.com/free/)開始使用。 
- Azure 虛擬機器上的 Windows 網域或內部部署資料中心會透過虛擬網路配對延伸至 Azure。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件之許可權的帳戶。
- Azure 虛擬網路和子網路且具有足夠 IP 位址空間以容納這些元件：
   - 兩部虛擬機器
   - Windows 容錯移轉叢集的 IP 位址
   - 每個 FCI 的 IP 位址
- 在 Azure 網路上設定的 DNS，指向網域控制站。

## <a name="choose-an-fci-storage-option"></a>選擇 FCI 儲存體選項

虛擬機器的設定會根據您打算用於 SQL Server 容錯移轉叢集實例的儲存體選項而有所不同。 準備虛擬機器之前，請先檢查 [可用的 FCI 儲存體選項](failover-cluster-instance-overview.md#storage) ，並選擇最適合您環境和商務需求的選項。 然後，根據您的儲存體選取專案，仔細地在本文中選取適當的 VM 設定選項。 

## <a name="configure-vm-availability"></a>設定 VM 可用性 

「容錯移轉叢集」功能需要將虛擬機器放置於 [可用性設定組](../../../virtual-machines/linux/tutorial-availability-sets.md) 或 [可用性區域](../../../availability-zones/az-overview.md#availability-zones)中。 如果您選擇可用性設定組，您可以使用 [鄰近放置群組](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) 來找出更接近的 vm。 事實上，鄰近放置群組是使用 Azure 共用磁片的必要條件。 

請小心選取符合您預期叢集設定的 VM 可用性選項： 

- **Azure 共用磁片**：如果您使用 Premium Ssd 或 UltraDisk，可用性選項會有所不同：
   - 進階 SSD：放在[鄰近放置群組](../../../virtual-machines/windows/proximity-placement-groups-portal.md)中之高階 ssd 的不同錯誤/更新網域中的[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。
   - Ultra 磁片： [可用性區域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) ，但 vm 必須放在相同的可用性區域中，以將叢集的可用性降低到99.9%。 
- **Premium 檔案共用**： [可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 或 [可用性區域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。
- **儲存空間直接存取**： [可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。

> [!IMPORTANT]
> 建立虛擬機器後，您即無法設定或變更可用性設定組。

## <a name="create-the-virtual-machines"></a>建立虛擬機器

設定 VM 可用性之後，您就可以開始建立虛擬機器。 您可以選擇使用執行或尚未安裝 SQL Server 的 Azure Marketplace 映射。 不過，如果您為 Azure Vm 上的 SQL Server 選擇映射，則必須先從虛擬機器卸載 SQL Server，然後再設定容錯移轉叢集實例。 

### <a name="considerations"></a>考量

在 Azure VM 來賓容錯移轉叢集上，我們建議每個伺服器 (叢集節點) 和單一子網的單一 NIC。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。

將兩部虛擬機器放置於：

- 如果您使用的是可用性設定組，請在與可用性設定組相同的 Azure 資源群組中。
- 在與您的網域控制站相同的虛擬網路上，或與您的網域控制站具有適當連線的虛擬網路上。
- 有足夠 IP 位址空間存放兩部虛擬機器，與最終會用於此叢集所有 FCI 的子網路上。
- 在 Azure 可用性設定組或可用性區域中。

您可以使用已預先安裝或[未](../../../virtual-machines/windows/quick-create-portal.md)安裝[SQL Server 的映射](sql-vm-create-portal-quickstart.md)來建立 Azure 虛擬機器。 如果您選擇 SQL Server 映射，則必須先手動卸載 SQL Server 實例，然後再安裝容錯移轉叢集實例。 


## <a name="uninstall-sql-server"></a>解除安裝 SQL Server

在 FCI 建立流程中，您會將 SQL Server 安裝為容錯移轉叢集的叢集實例。 *如果您在沒有 SQL Server 的情況下，使用 Azure Marketplace 映射部署虛擬機器，則可以略過此步驟。* 如果您已部署 SQL Server 預先安裝的映射，您必須從 SQL IaaS 代理程式擴充功能中取消註冊 SQL Server VM，然後卸載 SQL Server。 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>從 SQL IaaS 代理程式擴充取消登錄

從 Azure Marketplace SQL Server VM 映射會自動向 SQL IaaS 代理程式延伸模組註冊。 卸載預先安裝的 SQL Server 實例之前，您必須先 [從 SQL IaaS 代理程式擴充功能中取消註冊每個 SQL SERVER VM](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)。 

### <a name="uninstall-sql-server"></a>解除安裝 SQL Server

從擴充功能取消註冊之後，您可以卸載 SQL Server。 請在每部虛擬機器上執行下列步驟： 

1. 使用 RDP 連接到虛擬機器。

   當首次透過 RDP 連線到虛擬機器時會出現提示來詢問是否要允許此電腦在網路上可供搜尋。 選取 [是]。

1. 如果您是使用其中一個以 SQL Server 為基礎的虛擬機器映射，請移除 SQL Server 實例：

   1. 在 [程式和功能] 中，以滑鼠右鍵按一下 Microsoft SQL Server 201_ (64 位元)，然後選取 [解除安裝/變更]。
   1. 選取 [移除]。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務] 下的功能。 請勿移除 **共用功能** 下的任何功能。 您會看到類似下列螢幕擷取畫面的內容：

      ![特性選取](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. 選取 [下一步]，然後選取 [移除]。
   1. 成功移除實例之後，請重新開機虛擬機器。 

## <a name="open-the-firewall"></a>開啟防火牆 

在每部虛擬機器上，開啟 SQL Server 使用的 Windows 防火牆 TCP 埠。 根據預設，這是埠1433。 但是，您可以變更 Azure VM 部署上的 SQL Server 埠，因此請開啟 SQL Server 在您的環境中使用的埠。 此埠會在從 Azure Marketplace 部署 SQL Server 映射上自動開啟。 

如果您使用 [負載平衡器](failover-cluster-instance-vnn-azure-load-balancer-configure.md)，您也必須開啟健康情況探查所使用的埠。 根據預設，這是埠59999。 但它可以是您在建立負載平衡器時所指定的任何 TCP 埠。 

下表詳細說明您可能需要開啟的埠，取決於您的 FCI 設定： 

   | 目的 | Port | 注意
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。 </br> </br> **消費者**：所有 FCI 設定。 |
   | 健全狀況探查 | TCP 59999 | 任何開啟的 TCP 連接埠。 設定負載平衡器 [健全狀況探查](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) 和叢集，以使用此埠。 </br> </br> **由**： FCI 與負載平衡器搭配使用。 |
   | 檔案共用 | UDP 445 | 檔案共用服務使用的埠。 </br> </br> **消費者**： FCI 與 Premium 檔案共用。 |

## <a name="join-the-domain"></a>加入網域

您也需要將虛擬機器加入網域。 您可以使用 [快速入門範本](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)來完成這項作業。 

## <a name="review-storage-configuration"></a>查看儲存體設定

從 Azure Marketplace 建立的虛擬機器隨附連接的儲存體。 如果您打算使用 Premium 檔案共用或 Azure 共用磁片來設定 FCI 儲存體，您可以移除附加的儲存體以節省成本，因為本機儲存體不會用於容錯移轉叢集實例。 不過，您可以針對儲存空間直接存取 FCI 解決方案使用連接的儲存體，因此在此情況下移除它們可能會無用。 請參閱您的 FCI 儲存體解決方案，判斷移除連接的儲存體是否最適合用來節省成本。 


## <a name="next-steps"></a>後續步驟

現在您已準備好虛擬機器環境，接下來您可以設定容錯移轉叢集實例。 

選擇下列其中一個指南，以設定適用于您公司的 FCI 環境： 
- [使用 Azure 共用磁片設定 FCI](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [使用 Premium 檔案共用設定 FCI](failover-cluster-instance-premium-file-share-manually-configure.md)
- [使用儲存空間直接存取設定 FCI](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

若要深入瞭解，請參閱 [使用 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md) 以及 [支援的 HADR](hadr-cluster-best-practices.md)設定的 FCI 總覽。 

如需相關資訊，請參閱： 
- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集執行個體](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
