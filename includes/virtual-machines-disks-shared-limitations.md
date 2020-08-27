---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944508"
---
啟用共用磁片只適用于磁片類型的子集。 目前只有 ultra 磁片和 premium Ssd 可以啟用共用磁片。 啟用共用磁片的每個受控磁片受限於下列限制，並依磁片類型組織：

### <a name="ultra-disks"></a>Ultra 磁碟

Ultra 磁片有各自不同的限制清單，與共享磁片無關。 如需 ultra 磁片的限制，請參閱 [使用 Azure ultra 磁片](../articles/virtual-machines/disks-enable-ultra-ssd.md)。

共用 ultra 磁片時，它們具有下列額外的限制：

- 目前僅限 Azure Resource Manager 或 SDK 支援。 
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱 [容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。

預設會在所有支援 ultra 磁片的區域中使用共用的 ultra 磁片，但不需要您註冊以取得使用這些磁片的存取權。

### <a name="premium-ssds"></a>進階 SSD

- 目前只有在 [區域子集](#regional-availability)中才支援。
- 目前僅限 Azure Resource Manager 或 SDK 支援。 
- 只能在資料磁片（而不是 OS 磁片）上啟用。
- 的 premium Ssd 無法使用**ReadOnly**主機快取 `maxShares>1` 。
- 的 premium Ssd 無法使用磁片高載功能 `maxShares>1` 。
- 使用可用性設定組和虛擬機器擴展集搭配 Azure 共用磁片時，不會對共用資料磁片強制執行與虛擬機器容錯網域的 [儲存體容錯網域對齊](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) 。
- 使用 [鄰近位置群組 (PPG) ](../articles/virtual-machines/windows/proximity-placement-groups.md)時，共用磁片的所有虛擬機器都必須是相同 PPG 的一部分。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱 [容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 尚未提供 Azure 備份與 Azure Site Recovery 支援。

#### <a name="regional-availability"></a>區域可用性

只有下欄區域支援共用 premium Ssd：

- 美國東部
- 美國東部 2
- 美國西部
- 美國西部 2
- 美國中西部
- 美國中南部
- 美國中北部
- 美國中部
- 西歐
- 北歐
- 南韓中部
- 加拿大中部
- 加拿大東部
- 日本東部
- 日本西部
- US Gov 維吉尼亞州
- US Gov 亞利桑那州

如果您想要嘗試共用 premium Ssd，請 [註冊以取得存取權](https://aka.ms/AzureSharedDiskGASignUp)。