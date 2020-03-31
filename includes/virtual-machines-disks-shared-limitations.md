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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471672"
---
在預覽版中，已啟用共用磁片的託管磁片受以下限制：

- 目前僅適用于高級 SSD。
- 目前僅在美國中西部地區得到支援。
- 共用磁片的所有虛擬機器都必須部署在同一[接近放置組中](../articles/virtual-machines/windows/proximity-placement-groups.md)。
- 只能在資料磁片上啟用，不能在 OS 磁片上啟用。
- 只有基本磁碟可用於某些版本的 Windows 伺服器容錯移轉叢集，有關詳細資訊，請參閱[容錯移轉叢集硬體要求和存儲選項](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)。
- 唯讀主機緩存不適用於具有`maxShares>1`的高級 SSD。
- 可用性集和虛擬機器規模集只能與設置為 1`FaultDomainCount`一起使用。
- Azure 備份和 Azure 網站恢復支援尚不可用。

如果您有興趣嘗試共用磁片，請[註冊我們的預覽](https://aka.ms/AzureSharedDiskPreviewSignUp)版。
