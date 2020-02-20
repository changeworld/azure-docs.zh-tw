---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471672"
---
在預覽期間，已啟用共用磁片的受控磁片會受到下列限制：

- 目前僅適用于 premium Ssd。
- 目前僅在美國中西部區域中支援。
- 所有共用磁片的虛擬機器都必須部署在相同的[鄰近放置群組](../articles/virtual-machines/windows/proximity-placement-groups.md)中。
- 只能在資料磁片（而不是 OS 磁片）上啟用。
- 只有基本磁碟可以與某些版本的 Windows Server 容錯移轉叢集搭配使用，如需詳細資訊，請參閱[容錯移轉叢集硬體需求和存放裝置選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 具有 `maxShares>1`的 premium Ssd 無法使用 ReadOnly 主機快取。
- 可用性設定組和虛擬機器擴展集只能與設為1的 `FaultDomainCount` 搭配使用。
- Azure 備份和 Azure Site Recovery 支援尚無法使用。

如果您有興趣嘗試共用磁片，請[註冊預覽](https://aka.ms/AzureSharedDiskPreviewSignUp)。
