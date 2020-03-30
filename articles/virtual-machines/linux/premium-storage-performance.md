---
title: Azure 高級存儲：Linux VM 性能設計 |微軟文檔
description: 使用 Azure 高級 SSD 託管磁片設計高性能應用程式。 「進階儲存體」可針對在「Azure 虛擬機器」上執行且需要大量 I/O 的工作負載，提供高效能、低延遲的磁碟支援。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267140"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure 進階儲存體：專為高效能而設計
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 有時候，看似磁碟效能問題的情況，其實是網路瓶頸。 在這些情況下，您應該將您的[網路效能](../../virtual-network/virtual-network-optimize-network-bandwidth.md)最佳化。
>
> 如果您正在尋找磁片的基準測試，請參閱我們關於[磁片基準化](disks-benchmarks.md)的文章。
>
> 如果您的 VM 支援加速網路，您應確實加以啟用。 如果未啟用，您可以對 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 加以啟用。

開始之前，如果對高級存儲新增，請先閱讀[IaaS VM 的 Azure 磁片類型](disks-types.md)和[高級頁面 Blob 存儲帳戶的可伸縮性目標](../../storage/blobs/scalability-targets-premium-page-blobs.md)。


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

如果您正在尋找磁片的基準測試，請參閱我們關於[磁片基準化](disks-benchmarks.md)的文章。

瞭解有關可用磁片類型詳細資訊：[選擇磁片類型](disks-types.md)  

若為 SQL Server 使用者，請參閱「SQL Server 的效能最佳作法」文章：

* [Azure 虛擬機器中 SQL 伺服器的性能最佳實踐](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure 進階儲存體為 Azure VM 中的 SQL Server 提供最高效能](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)