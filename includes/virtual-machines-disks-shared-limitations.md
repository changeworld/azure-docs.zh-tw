---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500581"
---
啟用共用磁片僅適用于磁片類型的子集。 目前只有 ultra 磁片和 premium Ssd 可以啟用共用磁片。 已啟用共用磁片的每個受控磁片都受限於下列限制（依磁片類型組織）：

### <a name="ultra-disks"></a>Ultra 磁碟

Ultra 磁片有自己各自的限制清單，與共享磁片無關。 如需 ultra 磁片限制，請參閱[使用 Azure ultra 磁片](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)。

共用 ultra 磁片時，有下列額外的限制：

- 目前僅限於 Azure Resource Manager 或 SDK 支援。 
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱[容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

### <a name="premium-ssds"></a>進階 SSD

- 目前僅在美國中西部區域中支援。
- 目前僅限於 Azure Resource Manager 或 SDK 支援。 
- 只能在資料磁片（而不是 OS 磁片）上啟用。
- 使用的 premium Ssd 無法使用**ReadOnly**主機快取 `maxShares>1` 。
- 使用的 premium Ssd 無法使用磁片負載平衡功能 `maxShares>1` 。
- 搭配 Azure 共用磁片使用可用性設定組和虛擬機器擴展集時，不會對共用資料磁片強制執行與虛擬機器容錯網域的[儲存體容錯網域對齊](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)。
- 使用[鄰近放置群組（PPG）](../articles/virtual-machines/windows/proximity-placement-groups.md)時，所有共用磁片的虛擬機器都必須屬於相同的 PPG。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱[容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- Azure 備份和 Azure Site Recovery 支援尚無法使用。

如果您有興趣嘗試共用磁片，請[註冊以取得存取權](https://aka.ms/AzureSharedDiskGASignUp)。