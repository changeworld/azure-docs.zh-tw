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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008322"
---
在預覽版中,啟用共用磁碟僅適用於磁碟類型的子集。 目前只有超磁碟和先進 SSD 才能啟用共用磁碟。 每個已啟用共用磁碟的託管磁碟都受以下限制的約束,按磁碟類型組織:

### <a name="ultra-disks"></a>Ultra 磁碟

Ultra 磁碟有其各自的限制清單,與共用磁碟無關。 有關超磁碟限制,請參閱[使用 Azure 超級磁碟](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md)。

共用超磁碟時,它們具有以下附加限制:

- 目前僅在美國西部支援。
- 當前僅限於 Azure 資源管理器或 SDK 支援。
- 只有基本磁碟可用於某些版本的 Windows 伺服器故障轉移群集,有關詳細資訊,請參閱[故障轉移叢集硬體要求和儲存選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

### <a name="premium-ssds"></a>進階 SSD

- 目前僅在美國中西部地區得到支援。
- 共用磁碟的所有虛擬機器都必須部署在同一[接近放置群組中](../articles/virtual-machines/windows/proximity-placement-groups.md)。
- 只能在數據磁碟上啟用,不能在 OS 磁碟上啟用。
- 只有基本磁碟可用於某些版本的 Windows 伺服器故障轉移群集,有關詳細資訊,請參閱[故障轉移叢集硬體要求和儲存選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 唯讀主機快取不適用於具有`maxShares>1`的先進 SSD。
- 可用性集和虛擬機器規模集只能與設置為`FaultDomainCount`1 一起使用。
- Azure 備份和 Azure 網站恢復支援尚不可用。

如果您有興趣嘗試共用磁碟,請[註冊我們的預覽](https://aka.ms/AzureSharedDiskPreviewSignUp)版。
