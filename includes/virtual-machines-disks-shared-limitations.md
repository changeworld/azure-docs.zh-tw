---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 76e52758bf88dde9cdc68dad974f0b65fd55140f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555960"
---
啟用共用磁片只適用于磁片類型的子集。 目前只有 ultra 磁片和 premium Ssd 可以啟用共用磁片。 啟用共用磁片的每個受控磁片受限於下列限制，並依磁片類型組織：

### <a name="ultra-disks"></a>Ultra 磁碟

Ultra 磁片有各自不同的限制清單，與共享磁片無關。 如需 ultra 磁片的限制，請參閱 [使用 Azure ultra 磁片](../articles/virtual-machines/disks-enable-ultra-ssd.md)。

共用 ultra 磁片時，它們具有下列額外的限制：

- 目前僅限 Azure Resource Manager 或 SDK 支援。 
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱 [容錯移轉叢集硬體需求和存放裝置選項](/windows-server/failover-clustering/clustering-requirements)。

預設會在所有支援 ultra 磁片的區域中使用共用的 ultra 磁片，但不需要您註冊以取得使用這些磁片的存取權。

### <a name="premium-ssds"></a>進階 SSD

- 目前僅限 Azure Resource Manager 或 SDK 支援。 
- 只能在資料磁片（而不是 OS 磁片）上啟用。
- 的 premium Ssd 無法使用 **ReadOnly** 主機快取 `maxShares>1` 。
- 的 premium Ssd 無法使用磁片高載功能 `maxShares>1` 。
- 使用可用性設定組和虛擬機器擴展集搭配 Azure 共用磁片時，不會對共用資料磁片強制執行與虛擬機器容錯網域的 [儲存體容錯網域對齊](../articles/virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) 。
- 使用 [鄰近位置群組 (PPG) ](../articles/virtual-machines/windows/proximity-placement-groups.md)時，共用磁片的所有虛擬機器都必須是相同 PPG 的一部分。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱 [容錯移轉叢集硬體需求和存放裝置選項](/windows-server/failover-clustering/clustering-requirements)。
- 尚未提供 Azure 備份與 Azure Site Recovery 支援。

#### <a name="regional-availability"></a>區域可用性

共用 premium Ssd 適用于受控磁片可供使用的所有區域。