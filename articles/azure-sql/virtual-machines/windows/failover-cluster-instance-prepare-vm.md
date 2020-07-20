---
title: 準備虛擬機器以進行 FCI
description: 準備 Azure 虛擬機器以搭配容錯移轉叢集實例（FCI）使用，並在 Azure 虛擬機器上 SQL Server。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 7e62e414182d95a445f37c1c97cdef8aff6a587a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965454"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>準備虛擬機器以進行 FCI （在 Azure Vm 上 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文說明如何準備 Azure 虛擬機器（Vm），以將它們與 SQL Server 容錯移轉叢集實例（FCI）搭配使用。 設定會根據 FCI 儲存體解決方案而有所不同，因此請確認您選擇的是正確的設定，以符合您的環境和企業。 

若要深入瞭解，請參閱[FCI 與 Azure vm 上的 SQL Server](failover-cluster-instance-overview.md)和叢集[最佳做法](hadr-cluster-best-practices.md)。 

## <a name="prerequisites"></a>必要條件 

- Microsoft Azure 訂用帳戶。 [免費](https://azure.microsoft.com/free/)開始使用。 
- Azure 虛擬機器上的 Windows 網域，或使用虛擬網路配對擴充到 Azure 的內部部署資料中心。
- 具有在 Azure 虛擬機器和 Active Directory 中建立物件之許可權的帳戶。
- Azure 虛擬網路和子網路且具有足夠 IP 位址空間以容納這些元件：
   - 兩部虛擬機器
   - Windows 容錯移轉叢集的 IP 位址
   - 每個 FCI 的 IP 位址
- 在 Azure 網路上設定的 DNS，指向網域控制站。

## <a name="choose-an-fci-storage-option"></a>選擇 FCI 儲存體選項

您的虛擬機器設定值會根據您打算用於 SQL Server 容錯移轉叢集實例的儲存體選項而有所不同。 準備虛擬機器之前，請先檢查[可用的 FCI 儲存體選項](failover-cluster-instance-overview.md#storage)，並選擇最適合您的環境和商務需求的選項。 然後，根據您的儲存體選擇，仔細地在本文中選取適當的 VM 設定選項。 

## <a name="configure-vm-availability"></a>設定 VM 可用性 

容錯移轉叢集功能需要將虛擬機器放置在[可用性設定組](../../../virtual-machines/linux/tutorial-availability-sets.md)或[可用性區域](../../../availability-zones/az-overview.md#availability-zones)中。 如果您選擇可用性設定組，您可以使用[鄰近放置群組](../../../virtual-machines/windows/co-location.md#proximity-placement-groups)來尋找較接近的 vm。 事實上，鄰近性放置群組是使用 Azure 共用磁片的必要條件。 

請小心選取符合預期叢集設定的 VM 可用性選項： 

 - **Azure 共用磁片**：將容錯網域和更新網域設定為1，並放在[鄰近放置群組](../../../virtual-machines/windows/proximity-placement-groups-portal.md)中的[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。
 - **Premium 檔案共用**：[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)或[可用性區域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。 如果您選擇可用性區域作為 Vm 的可用性設定，Premium 檔案共用是唯一的共用儲存體選項。 
 - **儲存空間直接存取**：[可用性設定組](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。

>[!IMPORTANT]
>建立虛擬機器後，您即無法設定或變更可用性設定組。

## <a name="create-the-virtual-machines"></a>建立虛擬機器

設定 VM 可用性之後，您就可以開始建立您的虛擬機器。 您可以選擇使用執行或尚未安裝 SQL Server 的 Azure Marketplace 映射。 不過，如果您選擇 Azure Vm 上 SQL Server 的映射，則必須先從虛擬機器卸載 SQL Server，然後再設定容錯移轉叢集實例。 


將兩部虛擬機器放置於：

- 在與您的可用性設定組相同的 Azure 資源群組中，如果您使用的是可用性設定組。
- 在與您的網域控制站相同的虛擬網路上。
- 有足夠 IP 位址空間存放兩部虛擬機器，與最終會用於此叢集所有 FCI 的子網路上。
- 在 Azure 可用性設定組或可用性區域中。

您可以使用[包含](sql-vm-create-portal-quickstart.md)或[不含](../../../virtual-machines/windows/quick-create-portal.md)SQL Server 預先安裝的映射來建立 Azure 虛擬機器。 如果您選擇 SQL Server 的映射，則必須先手動卸載 SQL Server 實例，然後再安裝容錯移轉叢集實例。 


## <a name="uninstall-sql-server"></a>解除安裝 SQL Server

在 FCI 建立過程中，您會將 SQL Server 做為叢集實例安裝到容錯移轉叢集。 *如果您已使用 Azure Marketplace 映射部署虛擬機器，但未 SQL Server，則可以略過此步驟。* 如果您已部署 SQL Server 預先安裝的映射，則必須從 SQL VM 資源提供者取消註冊 SQL Server VM，然後卸載 SQL Server。 

### <a name="unregister-from-the-sql-vm-resource-provider"></a>從 SQL VM 資源提供者取消註冊

來自 Azure Marketplace 的 SQL Server VM 映射會自動向 SQL VM 資源提供者註冊。 卸載預先安裝的 SQL Server 實例之前，您必須先[從 SQL VM 資源提供者取消註冊每個 SQL SERVER VM](sql-vm-resource-provider-register.md#unregister-from-rp)。 

### <a name="uninstall-sql-server"></a>解除安裝 SQL Server

從資源提供者取消註冊之後，您可以卸載 SQL Server。 請在每部虛擬機器上執行下列步驟： 

1. 使用 RDP 連接到虛擬機器。

   當首次透過 RDP 連線到虛擬機器時會出現提示來詢問是否要允許此電腦在網路上可供搜尋。 選取 [是]。

1. 如果您使用其中一個以 SQL Server 為基礎的虛擬機器映射，請移除 SQL Server 實例：

   1. 在 [程式和功能] 中，以滑鼠右鍵按一下 Microsoft SQL Server 201_ (64 位元)，然後選取 [解除安裝/變更]。
   1. 選取 [移除]。
   1. 選取預設執行個體。
   1. 移除所有在 [Database Engine 服務] 下的功能。 請勿移除 [**共用功能**] 底下的任何專案。 您會看到類似下列螢幕擷取畫面的內容：

      ![特性選取](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. 選取 [下一步]，然後選取 [移除]。
   1. 成功移除實例之後，請重新開機虛擬機器。 

## <a name="open-the-firewall"></a>開啟防火牆 

在每部虛擬機器上，開啟 SQL Server 使用的 Windows 防火牆 TCP 通訊埠。 根據預設，這是埠1433。 但是，您可以變更 Azure VM 部署上的 SQL Server 埠，因此請開啟 SQL Server 在您的環境中使用的埠。 此埠會自動開啟，SQL Server 從 Azure Marketplace 部署的映射。 

如果您使用[負載平衡器](hadr-vnn-azure-load-balancer-configure.md)，您也需要開啟健全狀況探查所使用的埠。 根據預設，這是埠59999。 但它可以是您在建立負載平衡器時所指定的任何 TCP 埠。 

下表詳細說明您可能需要開啟的埠，視您的 FCI 設定而定： 

   | 目的 | Port | 注意
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | 適用於 SDL Server 預設執行個體的一般連接埠。 若您曾使用來自資源庫的映像，此連接埠會自動開啟。 </br> </br> **消費者**：所有 FCI 設定。 |
   | 健全狀況探查 | TCP 59999 | 任何開啟的 TCP 連接埠。 設定負載平衡器[健康情況探查](hadr-vnn-azure-load-balancer-configure.md#configure-health-probe)和叢集，以使用此埠。 </br> </br> **消費者**： FCI 搭配負載平衡器。 |
   | 檔案共用 | UDP 445 | 檔案共用服務所使用的埠。 </br> </br> **消費者**： FCI 搭配 Premium 檔案共用。 |

## <a name="join-the-domain"></a>加入網域

您也需要將虛擬機器加入網域。 您可以使用[快速入門範本](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)來執行此動作。 

## <a name="review-storage-configuration"></a>審查存放裝置設定

從 Azure Marketplace 建立的虛擬機器隨附附加的儲存體。 如果您打算使用 Premium 檔案共用或 Azure 共用磁片來設定 FCI 儲存體，您可以移除附加的儲存體以節省成本，因為本機儲存體不會用於容錯移轉叢集實例。 不過，您可以針對儲存空間直接存取 FCI 解決方案使用附加的儲存體，因此在此情況下移除它們可能會無用。 檢查您的 FCI 儲存體解決方案，以判斷是否要移除附加的儲存體，是節省成本的最佳做法。 


## <a name="next-steps"></a>後續步驟

既然您已準備好虛擬機器環境，您就可以設定容錯移轉叢集實例。 

選擇下列其中一個指南，以設定適合您企業的 FCI 環境： 
- [使用 Azure 共用磁片設定 FCI](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [設定具有 Premium 檔案共用的 FCI](failover-cluster-instance-premium-file-share-manually-configure.md)
- [使用儲存空間直接存取設定 FCI](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

若要深入瞭解，請參閱[Azure vm 上具有 SQL Server 的 FCI](failover-cluster-instance-overview.md)和[支援的 HADR](hadr-cluster-best-practices.md)設定的總覽。 

如需相關資訊，請參閱： 
- [Windows 叢集技術](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 容錯移轉叢集實例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
