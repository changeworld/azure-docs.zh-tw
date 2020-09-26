---
title: SQL Server VM 的儲存體組態 | Microsoft Docs
description: 本主題說明 Azure 如何在布建 (Azure Resource Manager 部署模型) 期間設定 SQL Server Vm 的儲存體。 它也會說明如何針對現有的 SQL Server VM 設定儲存體。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: fa471c201965096c4a0f022ab1199d4853128319
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272016"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM 的儲存體組態
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

當您在 Azure 中設定 SQL Server 虛擬機器 (VM) 映射時，Azure 入口網站有助於將您的儲存體設定自動化。 這包括將儲存體附加至 VM、讓該儲存體可供 SQL Server 存取，並加以設定以針對特定的效能需求最佳化。

本主題說明 Azure 如何在佈建期間針對 SQL Server VM 及針對現有的 VM 設定儲存體。 此設定是以執行 SQL Server 之 Azure VM 的 [效能最佳作法](performance-guidelines-best-practices.md) 為基礎。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>必要條件

若要使用自動儲存體組態設定，您的虛擬機器需具備下列特性︰

* 使用 [SQL Server 資源庫映像](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)佈建。
* 使用 [Resource Manager 部署模型](../../../azure-resource-manager/management/deployment-models.md)。
* 使用[進階 SSD](../../../virtual-machines/disks-types.md)。

## <a name="new-vms"></a>新的 VM

下列各節說明如何為新的 SQL Server 虛擬機器設定儲存體。

### <a name="azure-portal"></a>Azure 入口網站

使用 SQL Server 資源庫映像佈建 Azure VM 時，請在 [SQL Server 設定] 索引標籤上選取 [變更設定]，以開啟 [效能最佳化儲存體設定] 頁面。 您可以保留預設值，也可以根據您的工作負載，修改最符合您需求的磁碟設定類型。 

![佈建期間的 SQL Server VM 儲存體設定](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

在 [儲存體最佳化] 上，選取要為其部署 SQL Server 的工作負載類型。 使用 [一般] 最佳化選項時，根據預設您會有一個 IOPS 上限為 5000 的資料磁碟，而且您會使用此相同磁碟機來儲存資料、交易記錄和 TempDB。 選取 [交易處理] (OLTP) 或 [資料倉儲] 將會為資料建立個別磁碟、為交易記錄建立個別磁碟，並針對 TempDB 使用本機 SSD。 **交易處理**和**資料倉儲**之間沒有任何儲存上的差異，但其卻會變更您的[等量磁碟區設定和追蹤旗標](#workload-optimization-settings)。 根據 [SQL Server VM 效能最佳做法](performance-guidelines-best-practices.md)，選擇進階儲存體會將資料磁碟機的快取設定為*唯讀*，並將記錄磁碟機的快取設定為*無*。 

![佈建期間的 SQL Server VM 儲存體設定](./media/storage-configuration/sql-vm-storage-configuration.png)

您可以完全地自訂磁碟設定，因此您可以為 SQL Server VM 工作負載設定所需的儲存體拓撲、磁碟類型和 IOPS。 如果您的 SQL Server VM 位於其中一個受支援的區域 (美國東部2、東南亞和北歐)，而且您已啟用[訂用帳戶的 Ultra 磁碟](/azure/virtual-machines/windows/disks-enable-ultra-ssd)，則您也可以使用 UltraSSD (預覽) 作為**磁碟類型**的選項。  

此外，您也能為磁碟設定快取。 與[進階磁碟](/azure/virtual-machines/windows/disks-types#premium-ssd)搭配使用時，Azure VM 具有稱為 [Blob 快取](/azure/virtual-machines/windows/premium-storage-performance#disk-caching)的多層快取技術。 Blob 快取會結合虛擬機器 RAM 和本機 SSD 進行快取。 

進階 SSD 的磁碟快取可以是*唯讀*、*讀寫*或*無*。 

- *唯讀*快取對於儲存在進階儲存體上的 SQL Server 資料檔案非常有用。 從位於 VM 記憶體和本機 SSD 內的快取執行讀取時，*唯讀*快取可帶來低讀取延遲、高讀取 IOPS 和輸送量。 這些讀取速度會比從 Azure Blob 儲存體讀取資料磁片快許多。 進階儲存體不會將快取所服務的讀取計入磁碟 IOPS 和輸送量之內。 因此，應用程式能夠達到較高的 IOPS 和輸送量總計。 
- 裝載 SQL Server 記錄檔的磁碟應使用*無*快取設定，因為記錄檔會依序寫入，並不會受益於*唯讀*快取。 
- 不應使用*讀寫*快取來裝載 SQL Server 檔案，因為 SQL Server 未對*讀寫*快取支援資料一致性。 如果寫入時經過*唯讀* Blob 快取層，則寫入會浪費*唯讀* Blob 快取的容量，且延遲會稍微增加。 


   > [!TIP]
   > 請確定您的儲存體設定符合所選 VM 大小所加諸的限制。 選擇超過 VM 大小效能上限的儲存體參數將會導致錯誤：`The desired performance might not be reached due to the maximum virtual machine disk performance cap.`。 請變更磁碟類型來減少 IOPS，或增加 VM 大小來增加效能上限。 


根據您的選擇，Azure 會在建立 VM 後執行下列儲存體設定工作︰

* 建立進階 SSD 並將它連結到虛擬機器。
* 設定 SQL Server 可存取的資料磁碟。
* 根據指定的大小和效能 (IOPS 和輸送量) 需求，設定存放集區中的資料磁碟。
* 建立存放集區與虛擬機器上新磁碟機的關聯。
* 根據您指定的工作負載類型 (資料倉儲、交易式處理或一般)，最佳化這個新的磁碟機。

如需 Azure 如何進行儲存體設定的詳細資訊，請參閱 [儲存體設定章節](#storage-configuration)。 如需如何在 Azure 入口網站中建立 SQL Server VM 的完整逐步解說，請參閱 [佈建教學課程](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)。

### <a name="resource-manager-templates"></a>Resource Manager 範本

如果您使用下列 Resource Manager 範本，則預設會連接兩個進階資料磁碟，但不會設定存放集區。 不過，您可以自訂這些範本，以變更連接到虛擬機器的進階資料磁碟數目。

* [使用自動備份建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [使用自動修補建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [使用自 AKV 整合建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>快速入門範本

您可以使用下列快速入門範本，透過儲存體最佳化來部署 SQL Server VM。 

* [使用儲存體最佳化建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [使用 UltraSSD 建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>現有的 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

對於現有的 SQL Server VM，您可以在 Azure 入口網站中修改某些儲存體設定。 開啟 [SQL 虛擬機器資源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)，然後選取 [概觀]。 [SQL Server 概觀] 頁面會顯示您 VM 目前的儲存體使用量。 下圖顯示您的 VM 上存在的所有磁碟機。 對於每個磁碟機，儲存空間會顯示於四個區段中︰

* SQL 資料
* SQL 記錄檔
* 其他 (非 SQL 儲存體)
* 可用

若要修改儲存體設定，請選取 [設定] 底下的 [設定]。 

![設定現有 SQL Server VM 的儲存體](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

您可以修改在 SQL Server VM 建立過程中所設定磁碟機的磁碟設定。 選取 [延伸磁碟機] 會開啟 [磁碟機修改] 頁面，讓您可以變更磁碟類型，以及新增額外的磁碟。 

![設定現有 SQL Server VM 的儲存體](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>儲存體組態

本節提供 Azure 在 Azure 入口網站中 SQL Server VM 布建或設定期間自動執行之儲存體設定變更的參考。

* Azure 會從選自 VM 的儲存體設定存放集區。 本主題的下一節提供關於存放集區設定的詳細資料。
* 自動儲存體設定一律使用[進階 SSD](../../../virtual-machines/disks-types.md) P30 資料磁碟。 因此，您選取的 TB 數目與連接到您 VM 的資料磁碟數目會有 1:1 的對應。

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
| Number of columns |資料磁碟數目，最多可達 8 個<sup>1</sup> |


<sup>1</sup> 建立存放集區之後，您就無法改變存放集區中的資料行數目。


## <a name="workload-optimization-settings"></a>工作負載最佳化設定

下表描述三個可用的工作負載類型選項以及其對應的最佳化︰

| 工作負載類型 | 描述 | 最佳化 |
| --- | --- | --- |
| **一般** |支援大多數工作負載的預設設定 |None |
| **交易式處理** |可將儲存體最佳化來處理傳統資料庫 OLTP 工作負載。 |追蹤旗標 1117<br/>追蹤旗標 1118 |
| **資料倉儲** |可將儲存體最佳化來處理分析和報告工作負載。 |追蹤旗標 610<br/>追蹤旗標 1117 |

> [!NOTE]
> 您只可以在布建 SQL Server 的虛擬機器時，在「儲存體設定」步驟中加以選取，以指定工作負載類型。

## <a name="next-steps"></a>後續步驟

如需在 Azure VM 中執行 SQL Server 的其他相關主題，請參閱 [Azure 虛擬機器上的 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)。
