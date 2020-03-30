---
title: SQL Server VM 的儲存體組態 | Microsoft Docs
description: 本主題說明 Azure 如何在佈建期間針對 SQL Server VM 設定儲存體 (Resource Manager 部署模型)。 它也會說明如何針對現有的 SQL Server VM 設定儲存體。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 9d8fce0772f13c6e009b2441ecd85779a7622c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243194"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM 的儲存體組態

當您在 Azure 中設定 SQL Server 虛擬機器映像時，入口網站有協助自動進行儲存體設定。 這包括將儲存體附加至 VM、讓該儲存體可供 SQL Server 存取，並加以設定以針對特定的效能需求最佳化。

本主題說明 Azure 如何在佈建期間針對 SQL Server VM 及針對現有的 VM 設定儲存體。 此設定是以執行 SQL Server 之 Azure VM 的 [效能最佳作法](virtual-machines-windows-sql-performance.md) 為基礎。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Prerequisites

若要使用自動儲存體組態設定，您的虛擬機器需具備下列特性︰

* 使用 [SQL Server 資源庫映像](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)佈建。
* 使用 [Resource Manager 部署模型](../../../azure-resource-manager/management/deployment-models.md)。
* 使用[進階 SSD](../disks-types.md)。

## <a name="new-vms"></a>新的 VM

下列各節說明如何為新的 SQL Server 虛擬機器設定儲存體。

### <a name="azure-portal"></a>Azure 入口網站

使用 SQL Server 庫映射預配 Azure VM 時，請在**SQL Server 設置**選項卡上選擇 **"更改配置**"以打開性能優化存儲配置頁。 您可以將值保留為預設值，也可以根據工作負荷修改最適合您需求的磁片配置類型。 

![佈建期間的 SQL Server VM 儲存體設定](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

選擇要在**存儲優化**下部署 SQL Server 的工作負載類型。 使用 **"常規**優化"選項，預設情況下，您將有一個最大 IOPS 的資料磁片，並且您將對資料、事務日誌和 TempDB 存儲使用相同的磁碟機。 選擇**交易處理**（OLTP） 或**資料倉儲**將為數據創建單獨的磁片，為事務日誌創建單獨的磁片，並使用本地 SSD 進行 TempDB。 **交易處理**和**資料倉儲**之間沒有存儲差異，但它確實更改了[條帶配置和跟蹤標誌](#workload-optimization-settings)。 根據[SQL Server VM 性能最佳實踐](virtual-machines-windows-sql-performance.md)，選擇高級存儲將緩存設置為*資料磁碟機的 ReadOnly，* 為日誌磁碟機設置*為 None。* 

![佈建期間的 SQL Server VM 儲存體設定](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

磁片配置是完全可自訂的，因此您可以配置 SQL Server VM 工作負載所需的存儲拓撲、磁片類型和 IP。 如果您的 SQL Server VM 位於受支援的區域之一（美國東部 2、東南亞和北歐），並且已[為訂閱啟用了超磁片](/azure/virtual-machines/windows/disks-enable-ultra-ssd)，則還可以使用 UltraSD（預覽版）作為**磁片類型的**選項。  

此外，您還可以為磁片設置緩存。 Azure VM 具有稱為[Blob 緩存](/azure/virtual-machines/windows/premium-storage-performance#disk-caching)的多層緩存技術，當與[高級磁片](/azure/virtual-machines/windows/disks-types#premium-ssd)一起使用時。 Blob 緩存使用虛擬機器 RAM 和本地 SSD 的組合進行緩存。 

高級 SSD 的磁片緩存可以是*唯讀*、*讀寫*或*無*。 

- *唯讀*緩存對於存儲在高級存儲上的 SQL Server 資料檔案非常有益。 *ReadOnly*緩存帶來了低讀取延遲、高讀取 IOPS 和輸送量，因為讀取是從緩存執行的，緩存在 VM 記憶體和本地 SSD 中運行。 這些讀取速度比從 Azure Blob 存儲的資料磁片讀取快得多。 高級存儲不計算從緩存到磁片 IOPS 的讀取和輸送量。 因此，您適用的能夠實現更高的 IOPS 螞蟻總輸送量。 
- *不應將任何*緩存配置用於託管 SQL Server 日誌檔的磁片，因為日誌檔是按順序編寫的，並且不能從*ReadOnly*緩存中受益。 
- *不應使用 ReadWrite*緩存來承載 SQL Server 檔，因為 SQL Server 不支援與*ReadWrite*緩存的資料一致性。 如果寫入通過*ReadOnly* blob 緩存層，則*寫入*的浪費容量會略有增加。 


   > [!TIP]
   > 確保存儲配置與所選 VM 大小施加的限制相匹配。 選擇超出 VM 大小性能上限的存儲參數將導致錯誤： `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`。 通過更改磁片類型來減少 IP，或者通過增加 VM 大小來增加性能限制。 


根據您的選擇，Azure 會在建立 VM 後執行下列儲存體設定工作︰

* 建立進階 SSD 並將它連結到虛擬機器。
* 設定 SQL Server 可存取的資料磁碟。
* 根據指定的大小和效能 (IOPS 和輸送量) 需求，設定存放集區中的資料磁碟。
* 建立存放集區與虛擬機器上新磁碟機的關聯。
* 根據您指定的工作負載類型 (資料倉儲、交易式處理或一般)，最佳化這個新的磁碟機。

如需 Azure 如何進行儲存體設定的詳細資訊，請參閱 [儲存體設定章節](#storage-configuration)。 有關如何在 Azure 門戶中創建 SQL Server VM 的完整演練，請參閱[預配教程](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="resource-manage-templates"></a>Resource Manager 範本

如果您使用下列 Resource Manager 範本，則預設會連接兩個進階資料磁碟，但不會設定存放集區。 不過，您可以自訂這些範本，以變更連接到虛擬機器的進階資料磁碟數目。

* [使用自動備份建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [使用自動修補建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [使用自 AKV 整合建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>快速入門範本

您可以使用以下快速入門範本使用存儲優化部署 SQL Server VM。 

* [使用存儲優化創建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [使用超SSD創建 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>現有的 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

對於現有的 SQL Server VM，您可以在 Azure 入口網站中修改某些儲存體設定。 打開[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)，然後選擇 **"概述**"。 SQL 伺服器概述頁顯示 VM 的當前存儲使用方式。 下圖顯示您的 VM 上存在的所有磁碟機。 對於每個磁碟機，儲存空間會顯示於四個區段中︰

* SQL 資料
* SQL 記錄檔
* 其他 (非 SQL 儲存體)
* 可用

要修改存儲設置，請選擇"**設置**"下的 **"配置**"。 

![設定現有 SQL Server VM 的儲存體](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

您可以修改 SQL Server VM 創建過程中配置的磁碟機的磁片設置。 選擇 **"擴展磁碟機**"將打開磁碟機修改頁，允許您更改磁片類型，以及添加其他磁片。 

![設定現有 SQL Server VM 的儲存體](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>儲存體組態

本節提供 Azure 在 Azure 門戶中的 SQL VM 預配或配置期間自動執行的存儲配置更改的參考。

* Azure 從從 VM 選擇的存儲中配置存儲池。 本主題的下一部分提供有關存儲池配置的詳細資訊。
* 自動儲存體設定一律使用[進階 SSD](../disks-types.md) P30 資料磁碟。 因此，您選取的 TB 數目與連接到您 VM 的資料磁碟數目會有 1:1 的對應。

如需價格資訊，請參閱 [磁碟儲存體](https://azure.microsoft.com/pricing/details/storage) 索引標籤上的 **儲存體價格** 頁面。

### <a name="creation-of-the-storage-pool"></a>建立存放集區

Azure 會使用下列設定在 SQL Server VM 上建立存放集區。

| 設定 | 值 |
| --- | --- |
| 等量大小 |256 KB (資料倉儲)；64 KB (交易式) |
| 磁碟大小 |每個磁碟 1 TB |
| 快取 |讀取 |
| 配置大小 |64 KB NTFS 配置單位大小 |
| 復原 | 簡單復原 (無恢復功能) |
| Number of columns |資料磁片數高達 8<sup>1</sup> |


<sup>1</sup> 建立存放集區之後，您就無法改變存放集區中的資料行數目。


## <a name="workload-optimization-settings"></a>工作負載最佳化設定

下表描述三個可用的工作負載類型選項以及其對應的最佳化︰

| 工作負載類型 | 描述 | 最佳化 |
| --- | --- | --- |
| **一般** |支援大多數工作負載的預設設定 |None |
| **交易式處理** |可將儲存體最佳化來處理傳統資料庫 OLTP 工作負載。 |追蹤旗標 1117<br/>追蹤旗標 1118 |
| **資料倉儲** |可將儲存體最佳化來處理分析和報告工作負載。 |追蹤旗標 610<br/>追蹤旗標 1117 |

> [!NOTE]
> 當您在儲存體設定步驟中選取 SQL 虛擬機器來進行佈建時，您只能指定此工作負載類型。

## <a name="next-steps"></a>後續步驟

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)。
