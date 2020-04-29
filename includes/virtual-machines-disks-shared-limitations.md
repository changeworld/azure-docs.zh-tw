---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008322"
---
在預覽期間，啟用共用磁片僅適用于磁片類型的子集。 目前只有 ultra 磁片和 premium Ssd 可以啟用共用磁片。 已啟用共用磁片的每個受控磁片都受限於下列限制（依磁片類型組織）：

### <a name="ultra-disks"></a>Ultra 磁碟

Ultra 磁片有自己各自的限制清單，與共享磁片無關。 如需 ultra 磁片限制，請參閱[使用 Azure ultra 磁片](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)。

共用 ultra 磁片時，有下列額外的限制：

- 目前僅支援美國西部。
- 目前僅限於 Azure Resource Manager 或 SDK 支援。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱[容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

### <a name="premium-ssds"></a>進階 SSD

- 目前僅在美國中西部區域中支援。
- 所有共用磁片的虛擬機器都必須部署在相同的[鄰近放置群組](../articles/virtual-machines/windows/proximity-placement-groups.md)中。
- 只能在資料磁片（而不是 OS 磁片）上啟用。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱[容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 使用`maxShares>1`的 premium ssd 無法使用 ReadOnly 主機快取。
- 可用性設定組和虛擬機器擴展集只能搭配`FaultDomainCount`設定為1使用。
- Azure 備份和 Azure Site Recovery 支援尚無法使用。

如果您有興趣嘗試共用磁片，請[註冊預覽](https://aka.ms/AzureSharedDiskPreviewSignUp)。
